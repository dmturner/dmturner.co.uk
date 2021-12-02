---
title: How to zip files and folders in Linux
date: 2020-04-17T21:03:28.000+00:00
tags:
- linux
- dev-ops
- terminal
---


It's useful to know how to compress a file or folder using the terminal for those times that you're working remotely on a server and don't have access to the usual desktop compression tools. We can use the `zip` command to do this.

You'll likely already have `zip` installed as part of your chosen distribution. If you don't have it then you can grab it using the appropriate package manager:

##### Ubuntu

```bash
sudo apt install zip
```

##### Red Hat / CentOS / Fedora

```bash
sudo yum install zip
```

##### Arch / Manjaro

```bash
sudo pacman -S zip
```

Once installed, the command needed to zip a file or folder is pretty simple. It takes the following syntax:

```bash
zip [option] [archive_name.zip] [file_or_folder_to_compress]
```

If, for example, we're currently in a directory that contains a folder called "log_files" and we want to compress that folder we could use the following command:

```bash
zip -r archive.zip log_files
```

Here we've specified the name of the new .zip file followed by the actual directory to zip up. We're also passing the `-r` option which simply stands for "recursive". This means that the zip file will include the entire directory tree of the specified folder.

Remember that the above command assumes that we're in the same directory as the file or folder that we want to zip up. If we're somewhere else within the file system, then we need to provide the full path of the folder that we want to zip up:

```bash
zip -r archive.zip /path/to/folder
```

To compress a file rather than a folder, we just specify the file name instead of the folder name. In this example, the `-r` option isn't needed as we're dealing with a single file:

```bash
zip archive.zip /path/to/file.log
```

There's plenty more you can do with this command by utilising the available options when issuing the command. Take a look at the manual pages for `zip` to find out more:

```bash
man zip
```

The `zip` command is especially useful for archiving log files or compressing entire directories so that they can be downloaded or transferred across servers. Give it a go!
