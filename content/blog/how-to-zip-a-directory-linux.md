---
title: "How to zip a directory in Linux"
date: 2020-04-17T22:03:28+01:00
draft: false
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

```javascript
function callOnce (fn) {
  let fulfilled = false;

  return function onceWrapper (err) {
    if (!fulfilled) {
      fulfilled = true;
      return fn.apply(this, arguments);
    }
    else if (err) {
      // The callback has already been called, but now an error has occurred
      // (most likely inside the callback function). So re-throw the error,
      // so it gets handled further up the call stack
      throw err;
    }
  };
}
```
Some text at the end of the article goes here.

