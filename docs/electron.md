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

## If you'd like to exchange information between Render.js and main.js (two-way)

A common application for two-way IPC is calling a main process module from your renderer process code and waiting for a result. This can be done by using `ipcRenderer.invoke`(in preload.js) paired with `ipcMain.handle`(in main.js).

Here's a example 

`main.js`

```javascript
const { app, BrowserWindow,ipcMain  } = require('electron');
const path = require('path');

function handlePing () {
  return 'Pong'
}

const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js'),
    },
  });
  ipcMain.handle('ping',handlePing) // the main will handle the ping function 
  win.loadFile('index.html');
};

app.whenReady().then(() => {
  createWindow();

  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) {
      createWindow();
    }
  });
});

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});
```

`preload.js`

```javascript
const { contextBridge,ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('versions', {
  node: () => process.versions.node,
  chrome: () => process.versions.chrome,
  electron: () => process.versions.electron,
});

contextBridge.exposeInMainWorld('electronAPI', {
    ping: () => ipcRenderer.invoke('ping') // the renderer.js can use the ping function
})
```

`renderer.js`

```javascript
const func = async () => {
    const response = await window.versions.ping()
    console.log(response) // prints out 'pong'
  }
  
func()
const information = document.getElementById('info');

information.innerText = `This app is using Chrome (v${versions.chrome()}), Node.js (v${versions.node()}), and Electron (v${versions.electron()})`;
const btn = document.getElementById('btn')
const filePathElement = document.getElementById('filePath')

btn.addEventListener('click', async () => {
    const filePath = await window.electronAPI.ping()
    filePathElement.innerText = filePath
  })

```

`index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <meta
      http-equiv="X-Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <title>Hello from Electron renderer!</title>
  </head>
  <body>
    <h1>Hello from Electron renderer!</h1>
    <p>👋</p>
    <p id="info"></p> 
<!--  the info will show the version information in render.js    -->
    
    <button type="button" id="btn">Open a File</button>
    File path: <strong id="filePath"></strong>
<!--   the filePath id will show the function ping   -->
    <script src="./renderer.js"></script>
  </body>
  <!-- <script src="./renderer.js"></script> -->
</html>
```
