---
title: Securing a ZNC IRC Bouncer with an SSH Tunnel and autossh
date: 2026-11-25T15:00:00.000+00:00
draft: false
tags:
- linux
- dev-ops
- irc
---

When running your own IRC bouncer, it's tempting to expose its listening port directly to the internet. It makes connecting from clients such as WeeChat straightforward, but it also means adding another publicly accessible service to your server.

As part of a wider server security review, I decided to take a different approach: keep ZNC listening only on the server's loopback interface and use an SSH tunnel to access it remotely.

This has the advantage of reducing the server's public attack surface without losing the convenience of connecting to ZNC from my desktop.

Here's how I set it up.

## The security issue

ZNC was running successfully on the server, listening on port `1025`. Its web interface was accessible through an Apache reverse proxy, using HTTPS and a normal domain name.

However, the IRC client connection was a different matter. WeeChat was configured to connect directly to the server on port `1025`.

During the security review, I had tightened the firewall rules and removed the public access to that port. ZNC was also configured to listen only on:

```text
127.0.0.1:1025
```

This meant that connections from outside the server could no longer reach ZNC directly.

That was intentional. There was no reason to expose the ZNC listening port publicly when SSH was already available.

The challenge was finding a way to connect remotely while keeping the service private.

## Creating an SSH tunnel

SSH supports local port forwarding, which allows a port on your local computer to be securely forwarded to a service running on a remote machine.

The following command creates a tunnel:

```bash
ssh -N -L 1025:127.0.0.1:1025 root@your-server
```

The options are worth understanding:

- `-N` tells SSH not to execute a remote command. We only want the tunnel.
- `-L` creates a local port forward.
- The first `1025` is the port on the local computer.
- `127.0.0.1:1025` is the destination as seen from the remote server.
- `root@your-server` is the SSH connection to the server.

Once connected, anything sent to port `1025` on the local machine is forwarded through the encrypted SSH connection to port `1025` on the server.

WeeChat can therefore connect to the local tunnel rather than trying to access the server's private ZNC listener directly.

You can check that the tunnel is listening with:

```bash
ss -ltnp | grep 1025
```

And test connectivity with:

```bash
nc -vz 127.0.0.1 1025
```

A successful connection confirms that something is listening on the local port and accepting connections.

## Keeping TLS certificate verification working

There was one additional complication.

ZNC was configured with an SSL certificate issued for its public hostname, `znc.example.com`. When WeeChat connected to `127.0.0.1`, TLS certificate verification failed because the certificate was issued for the hostname, not the IP address.

Disabling certificate verification would have been an easy workaround, but it wasn't a solution I wanted to use.

Instead, I kept the original hostname in WeeChat's server configuration and added a local hosts-file entry on the desktop:

```text
127.0.0.1 znc.example.com
```

WeeChat could now connect to `znc.example.com` on port `1025`, but the operating system would resolve that hostname to the local loopback address.

The connection therefore followed this path:

1. WeeChat connects to `znc.example.com:1025`.
2. The local hosts file resolves the hostname to `127.0.0.1`.
3. The local SSH tunnel receives the connection.
4. SSH forwards it securely to `127.0.0.1:1025` on the remote server.
5. ZNC responds through the tunnel.

Crucially, WeeChat still sees the original hostname during TLS verification, so the certificate remains valid and verification can stay enabled.

This avoids having to weaken the TLS settings just to accommodate the tunnel.

## Making the tunnel persistent with autossh

Starting the tunnel manually works, but it's inconvenient to have to open a terminal and run the SSH command every time the computer starts.

I wanted the tunnel to run in the background, reconnect automatically if the connection dropped, and start whenever I logged into my desktop.

For this, I used `autossh`.

On Arch Linux, it can be installed with:

```bash
sudo pacman -S autossh
```

`autossh` monitors an SSH connection and can restart it if it terminates unexpectedly. SSH's own keepalive options also help detect a connection that has stopped responding.

I chose to manage it using a systemd user service.

First, create the user service directory if it doesn't already exist:

```bash
mkdir -p ~/.config/systemd/user
```

Then create the service file:

```bash
vim ~/.config/systemd/user/znc-tunnel.service
```

Add the following:

```ini
[Unit]
Description=SSH tunnel to ZNC
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/bin/autossh -M 0 -N -L 1025:127.0.0.1:1025 -i /home/username/.ssh/id_rsa -o IdentitiesOnly=yes -o ServerAliveInterval=30 -o ServerAliveCountMax=3 root@your-server
Restart=always
RestartSec=10

[Install]
WantedBy=default.target
```

A few details are important here:

- `-M 0` disables autossh's separate monitoring port. Instead, it relies on SSH keepalives to detect a failed connection.
- `-i` specifies the SSH private key to use.
- `IdentitiesOnly=yes` ensures SSH uses the specified identity rather than trying other available keys.
- `ServerAliveInterval=30` sends a keepalive every 30 seconds.
- `ServerAliveCountMax=3` terminates the SSH connection if three consecutive keepalives receive no response.
- `Restart=always` tells systemd to restart the service if it exits.
- `RestartSec=10` adds a short delay before restarting.

Replace `/home/username/.ssh/id_rsa` with the actual path to your private key, and change the SSH username and server address to suit your setup.

Specifying the key explicitly is particularly useful with systemd user services. An interactive SSH session may have access to keys loaded into an SSH agent, but a background service doesn't necessarily inherit the same agent environment. Using `-i` avoids relying on that.

## Enabling and starting the service

Once the service file is saved, tell systemd to reload its configuration:

```bash
systemctl --user daemon-reload
```

Enable the service and start it immediately:

```bash
systemctl --user enable --now znc-tunnel.service
```

Check its status:

```bash
systemctl --user status znc-tunnel.service
```

You should see:

```text
Active: active (running)
```

You can also inspect its logs if something goes wrong:

```bash
journalctl --user -u znc-tunnel.service -n 40 --no-pager
```

Finally, check that the local port is accepting connections:

```bash
nc -vz 127.0.0.1 1025
```

Once that succeeds, WeeChat should be able to connect through the tunnel.

## The end result

The finished setup gives me the best of both approaches.

- ZNC remains running on the remote server.
- Its IRC listening port is not exposed publicly.
- The existing HTTPS reverse proxy continues to provide access to the web interface.
- WeeChat connects through an encrypted SSH tunnel.
- TLS certificate verification remains enabled.
- `autossh` and systemd keep the tunnel running and recover it if the connection drops.

One important distinction is that the systemd user service starts when the desktop user session starts. It doesn't need to run continuously for ZNC itself to work: the bouncer remains online on the server and continues maintaining its IRC connections while the desktop is switched off.

The tunnel is only needed when I want to connect my local IRC client.

It's a relatively simple change, but it removes another publicly exposed service from the server while retaining a convenient desktop workflow.
