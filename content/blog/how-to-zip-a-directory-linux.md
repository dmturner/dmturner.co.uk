---
title: "How to zip a directory in Linux"
date: 2020-04-17T22:03:28+01:00
draft: true
---
Sooner or later you may need to zip up a folder - otherwise known a directory - on your linux machine. The code is needed is pretty simple:

```bash
zip [option] your_new_file.zip the_folder_to_zip
```

So a real-world example of the above logic might be something like this:

```bash
zip -r html_files.zip html
```

We're passing the ```-r``` option which simply stands for "recursive". This means that the zip command will continue beyond the specified folder(s) and zip anything inside those folder also.
