---
title: "How to zip a directory in Linux"
date: 2020-04-17T22:03:28+01:00
draft: false
---
It's useful to know how to compress a file or folder using the terminal for those times that you're working remotley on a server and don't have access to the usual desktop compression tools. We can use the ```zip``` command to do this.

If you don't already have ```zip``` installed then you can grab it using the ```apt``` package manager:

```bash
sudo apt install zip
```

Once installed, the command needed zip a file or folder is pretty simple. It takes the following synax:

```bash
zip [option] [archive_name.zip] [the_file_or_folder_to_compress]
```

If, for example, we want to compress the /html folder on our server we could use the following command:

```bash
zip -r html.zip html
```

Here we've specified the name of the new .zip file followed by the actual directory to zip up. We're also passing the ```-r``` option which simply stands for "recursive". This means that the zip command will continue beyond the specified folder and also zip up the files and folders contained within the specified folder.

The above command assumes that we're in the same directory as the file or folder that we want to zip up. Remember to specify the path of the folder to zip if you're somewhere else within the file system:

```bash
zip -r html.zip /var/www/html
```

There's plenty more you can do with this command by utilising the available options when issuing the command. Take a look at the ```man``` pages to find out more.

```bash
man zip
```