---
layout: default
title: tkinter 知识点
parent: python
date:   2021-11-10 20:28:05 -0400
categories: python
---


---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## tkinter Introduction

## How to use tkinter

```python

from pathlib import Path
import tkinter as tk

from tkinter import Button

from tkinter.messagebox import showinfo
from tkinter import *
from tkinter import filedialog

import yaml, os

from ocr_wokflow import ocr_workflow



root = tk.Tk()
root.title('OCR App')


root.resizable(True, True)
root.geometry('750x400')


frame_center = Frame(root)
frame_left = Frame(root)
frame_right = Frame(root)


# in windows
with open('./config.yml','r') as f:
    config = yaml.safe_load(f)

def select_directories():

    # batch folders
    for w in frame_right.winfo_children():
        w.destroy()

    global list_folder
    list_folder = []

    # option 2: can only choose one directory
    dirSelect = filedialog.Directory()
    # list_folder.append(dirSelect.show())
    list_folder.append(
        filedialog.askdirectory())

    Label(frame_right, text='Folder path', bg='yellow').pack(pady=10)
    for b in list_folder:
        print(b)
        Radiobutton(frame_right, text=b).pack()

    frame_right.pack()

def convert2text():
    for path_folder in list_folder:
        ocr_workflow(
            path_imgFolder=path_folder,
            path_textFolder=path_folder
        )
    showinfo(
        title='Convert',
        message="Convert to Plain Text: Finished"
    )

Label(frame_center, text='OCR',
      font=('Helvetica 20 bold')).grid(row=0, column=3, padx= 10, pady= 10)

Button(frame_center, text='Choose Folder',
       command=select_directories, bg='#F0A500', fg='white').grid(row=1, column=2, pady= 3)
Button(frame_center,text='Process',
       command= convert2text,bg='#F0A500',fg='black').grid(row=1, column=4, pady= 3)



# Button(frame_center, text='Choose PCR Folder',
#        command=select_directories, bg='#95CD41', fg='white').grid(row=1, column=4, pady= 3)
# Button(frame_center, text='Generate PCR Map',
#        command=create_PCR_map, bg='#95CD41', fg='black').grid(row=2, column=4, pady= 3)



frame_center.pack()




root.mainloop()

```

## use pyinstaller to generate an APP
