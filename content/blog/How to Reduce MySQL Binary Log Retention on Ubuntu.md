---
title: How to Reduce MySQL Binary Log Retention on Ubuntu
date: 2026-11-21T12:30:28.000+00:00
draft: false
tags:
- linux
- dev-ops
- terminal
---

I recently had an issue with a client's Ubuntu server where MySQL binary log files were taking up a considerable amount of disk space. In this case, they had accumulated to around 14GB, which was becoming a concern on a server with a relatively small disk.

Binary logs (binlogs) record changes made to a MySQL database. They're useful for replication and point-in-time recovery, but if they're retained for longer than necessary, they can gradually consume a surprising amount of storage.

In my case, the server was configured to retain 30 days of binary logs. As I was relying on DigitalOcean backups and snapshots, rather than using the binlogs for point-in-time recovery, I decided to reduce the retention period to seven days.

## Changing the retention period

On Ubuntu, MySQL's configuration is generally stored in:

```bash
/etc/mysql/mysql.conf.d/mysqld.cnf
```

Open the file:

```bash
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

Look for the binary logging section, where you'll usually find a commented-out setting similar to:

```ini
# binlog_expire_logs_seconds = 2592000
```

The value is measured in seconds. The default 2,592,000 seconds represents 30 days.

I changed this to:

```ini
binlog_expire_logs_seconds = 604800
```

This sets the retention period to seven days (604,800 seconds).

Save the file and restart MySQL to apply the change:

```bash
systemctl restart mysql
```

You can verify that the new setting is active with:

```bash
mysql -u root -p -e "SHOW VARIABLES LIKE 'binlog_expire_logs_seconds';"
```

The returned value should be `604800`.

## Removing existing binary logs

One thing worth knowing is that changing the retention setting doesn't necessarily remove all the existing older files immediately.

To remove binary logs older than seven days, I used MySQL's own purge command:

```bash
mysql -u root -p -e "PURGE BINARY LOGS BEFORE (NOW() - INTERVAL 7 DAY);"
```

**Important:** Never manually delete MySQL binary log files from the filesystem. Use the MySQL `PURGE BINARY LOGS` command so that MySQL can update its binary log index correctly.

## Checking the recovered disk space

Finally, I checked how much storage the remaining binlogs were using and how much free space was available:

```bash
du -ch /var/lib/mysql/binlog.* 2>/dev/null | tail -1
df -h /
```

On my server, this recovered approximately 8.5GB of disk space, taking the binlog storage down from around 14GB to approximately 5.5GB.

## A final consideration

Before reducing retention or purging existing logs, check whether your server uses MySQL replication or relies on binary logs for point-in-time recovery. You should also consider how frequently your database backups are taken and how much recovery history you need.

DigitalOcean snapshots and backups are useful, but they don't provide the same point-in-time recovery capability as a database backup combined with the relevant binary logs.

For my particular setup, seven days was a sensible compromise. It reduced unnecessary disk usage while keeping a short window of binary logs available.
