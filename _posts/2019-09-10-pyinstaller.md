---
layout: post
title:  "PyInstaller - Standalone Python Applications for MacOS, Windows, and Linux"
date: '2019-09-10'
image: /img/pyinstaller.png
categories: [python]
tags: [python, app, development]
---

We will explore the use of `PyInstaller` to create a cross-platform standalone application using python. For this tutorial I will be using `Tkinter` to visually demonstrate.

PyInstaller can be used in **Python3** and **Python2**, similarly it creates applications for MacOS, Windows, and Linux. 

I will be using:
* Python 3
* MacOS 10.12.6
* PyInstaller 3.5

**N.B.** - Currently Python 3 has issues with importing a compatible version of tcl on MacOS, however the below describes a method that works around this.

## Setup
PyInstaller runs from within the terminal, from here we will install PyInstaller, create a working directory, and cd to it. 

```
$ pip install pyinstaller
$ mkdir DogsGo
$ cd DogsGo
```
## Write your python script
This will vary depending on your task, below I will demonstrate visually by creating a `tkinter` app.

If needed: `pip install tkinter`.

Save the below `tkinter` app as `DogsGo.py`.
```
import tkinter as tk 
root = tk.Tk()
root.title("DogsGo...")
root.geometry('350x200')
lbl = tk.Label(root, text="Woof?")
lbl.grid(column=0, row=0)
tk.mainloop()
```
Run the above `$ python DogsGo.py` or from within python.

You should have generated an app that shows: 

<img src="/img/appDogsGo.png" alt="app"  height="250" />

**N.B.** 
Python 2 uses `import Tkinter` and Python 3 `import tkinter`.

## Build the application
Before we build the application you should first understand flags, these are settings which you apply at the time of building. In many cases these can be applied after, however many are easiest to add at the point of build. For this example I will describe two, however more can be found [here](https://pyinstaller.readthedocs.io/en/stable/usage.html). 

**-w, --windowed, --noconsole** - Prevents terminal from appearing when app is opened.
>Windows and Mac OS X: do not provide a console window for standard i/o. On Mac OS X this also triggers building an OS X .app bundle. On Windows this option will be set if the first script is a ‘.pyw’ file. This option is ignored in *NIX systems.

**-F, --onefile** - App is bundled into a single file. 
>Create a one-file bundled executable.

**If you are a MacOS user don't use --onefile**, I'll elaborate below.

### Windows and Linux
```
$ pyinstaller --windowed --onefile DogsGo.py
```
Once the build is complete, navigate to `dist` and run the `DogsGo` application. Or:
```
$ open dist/DogsGo.app
````
*change extension and path depending on OS.

### MacOS

Currently the pyinstaller build on MacOS uses an incompatible version of tcl, resulting in the app immediately crashing when it opens (i.e. if you follow the above). Below is a method which allows the application to be opened by editing the `init.tcl` file. I have written a python script which makes these changes.

```
$ pyinstaller --windowed  DogsGo.py
```

The build will be created however the `Contents` of the application will remain as separate files. Clone the `TCLChanger.py` from my git repository and run in the same working directory as your original python script. 

```
$ git clone https://github.com/jacob-brown/TCLChanger.git
$ python TCLChanger/TCLChanger.py
```
*Or download the file if you don't use git* :'(

You should now be able to open the application.

### Exploring .spec and adding an icon
Adding an application icon can be done using the flag `--icon=icon-name.icns` during the build, however we will use this as an oppertunity to delve into the .spec file that has been generated. 

Opening `DogsGo.spec` will show a number of settings we can update, for the application icon we want to update `exe = EXE(...)` and `app = BUNDLE(...)`. 

Let us update the icon to [dog.icns](https://icon-icons.com/download/15240/ICNS/128/), don't forget to rename it `dog.icns` and copy it to your current directory.

**exe = EXE(...)**

Add `icon='dog.icns'`.

```
exe = EXE(pyz,
          a.scripts,
          [],
          exclude_binaries=True,
          name='DogsGo',
          debug=False,
          bootloader_ignore_signals=False,
          strip=False,
          upx=True,
          console=False , 
          icon='dog.icns') 
```

**app = BUNDLE()**

Change `icon=None` to `icon='dog.icns'`.

```
app = BUNDLE(coll,
             name='DogsGo.app',
             icon='dog.icns', 
             bundle_identifier=None)
```

Now that `DogsGo.spec` has been updated, run the pyinstaller for the `DogsGo.spec`.

```
$ pyinstaller DogsGo.spec
```

*MacOS users, remember to run* `$ python TCLChanger.py` *again*

## Final Note
That's it! You will now be able to transfer the standalone application and use it. 


<img src="/img/DogsGo.png" alt="dog"  height="200" />

> woof.

## Helpful links
* Pyinstaller Manual: [https://pyinstaller.readthedocs.io/en/stable/index.html](https://pyinstaller.readthedocs.io/en/stable/index.html)
* TKDocsL: [https://tkdocs.com/index.html](https://tkdocs.com/index.html)
* PyInstaller repository: [https://github.com/pyinstaller/pyinstaller](https://github.com/pyinstaller/pyinstaller)
* TCLChanger Repository: [https://github.com/jacob-brown/TCLChanger](https://github.com/jacob-brown/TCLChanger)
