---
title: Electron-渲染进程和主进程通信
date: 2023-01-17 14:37:33
cover: https://bu.dusays.com/2023/01/11/63be1ba2cf2b0.jpg
top_img: https://bu.dusays.com/2023/01/11/63be5983a519e.jpeg
tags:
- Electron
categories:
- Electron
---

# Electron-渲染进程和主进程通信

渲染进程调用ipcRenderer进行发送

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-11 10:22:30
 * @LastEditTime: 2023-01-17 14:43:58
 * @LastEditors: Do not edit
-->
<script setup lang="ts">
import { ref } from 'vue'
import {ipcRenderer} from 'electron'
defineProps<{ msg: string }>()

const count = ref(0)

const send = () => {
  count.value++
  // 渲染进程使用ipcRenderer发送
  ipcRenderer.send('message',count.value)
}
</script>

<template>
  <h1>{{ msg }}</h1>

  <div class="card">
    <button type="button" @click="send">count is {{ count }}</button>
    <p>
      Edit
      <code>components/HelloWorld.vue</code> to test HMR
    </p>
  </div>

  <p>
    Check out
    <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank"
      >create-vue</a
    >, the official Vue + Vite starter
  </p>
  <p>
    Install
    <a href="https://github.com/johnsoncodehk/volar" target="_blank">Volar</a>
    in your IDE for a better DX
  </p>
  <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>

```

主进程使用ipcMain接收

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-11 10:40:48
 * @LastEditTime: 2023-01-17 15:05:10
 * @LastEditors: Do not edit
 */
import { app, BrowserWindow, ipcMain } from 'electron'
import path from 'path'
const createWindow = () => {
    const win = new BrowserWindow({
        width: 1200,
        height: 900,
        webPreferences: {
            devTools: true,
            contextIsolation: false,
            nodeIntegration: true
        }
    })
    // console.log(process.env.VITE_DEV_SERVER_URL);
    // console.log(app.isPackaged, 'app.isPackaged');
    if (app.isPackaged) {
        win.loadFile(path.join(__dirname, '../index.html'))
        // win.loadURL('https://www.xiuji957.cn/')
        // win.webContents.openDevTools();
    } else {
        win.loadURL(`${process.env.VITE_DEV_SERVER_URL}`)
        // 开发环境打开开发者工具
        win.webContents.openDevTools();
    }
    // 主进程使用ipcMain接收
    ipcMain.on('message', (e, iterm) => {
        // 参数e是一个event，参数iterm是渲染进程传输过来的参数
        console.log(iterm, '收到');
    })

}

app.whenReady().then(createWindow)
```

启动项目报错![](https://bu.dusays.com/2023/01/17/63c6476862b2d.png)

安装vite-plugin-electron-renderer

```bash
pnpm i vite-plugin-electron-renderer -D
```

配置vite.config.ts

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-11 10:22:30
 * @LastEditTime: 2023-01-17 15:01:33
 * @LastEditors: Do not edit
 */
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron' // 引入插件
import electronRender from 'vite-plugin-electron-renderer'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), electron({
    entry: 'electron/index.ts' // 配置入口文件
  }), electronRender()],
})
```

成功启动项目,渲染进程与主进程通信成功![](https://bu.dusays.com/2023/01/17/63c6495b74535.png)

# 主进程与渲染进程通信

主进程发送

```ts
// 主进程向渲染进程通信
setTimeout(() => {
  win.webContents.send('load', { msg: '主进程发来贺电' })
}, 2000);
```

渲染进程使用ipcRenderer接收

```
ipcRenderer.on('load', (e, i) => {
  console.log(i,'渲染进程接收');
})
```

成功![](https://bu.dusays.com/2023/01/17/63c64b921459b.png)
