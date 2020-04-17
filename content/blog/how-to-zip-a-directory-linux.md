---
title: "How to zip a directory in Linux"
date: 2020-04-17T22:03:28+01:00
draft: true
---
Sooner or later you may need to zip up a folder - otherwise known a directory - on your linux machine. The code is needed is pretty simple:

```bash
zip [option] output_file_name input1 input2
```
So a real-world example of the above logic might be something like this:

```bash
zip -r output_file.zip file1 folder1
```

We're passing the ```-r``` parameter which simply stands for "recursive". This means that the zip command will continue beyond the specified folder(s) and zip anything inside those folder also.

