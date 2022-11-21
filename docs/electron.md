---
layout: default
title: Electron documentation
date:   2021-09-11 20:28:05 -0400
categories: Linux
nav_order: 100

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

# The communication between main.js, preload.js and render.js

The reason why we need these three files is security. When you want to communicate information between main.js and render.js, you need to specify the `API` in preload.js.
So the render.js can send message to main.js, or receive message from main.js.

## If you'd like to transmit message from `Renderer to main (one-way)`

As shown in the [official documentation](https://www.electronjs.org/docs/latest/tutorial/ipc#:~:text=To%20fire%20a%20one%2Dway,API%20from%20your%20web%20contents.):To fire a one-way IPC message from a renderer process to the main process, you can use the `ipcRenderer.send` (in preload.js) API to send a message that is then received by the `ipcMain.on`(in main.js) API.
