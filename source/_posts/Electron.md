---
title: Electron
date: 2023-01-11 10:07:46
cover: https://bu.dusays.com/2023/01/11/63be1ba2cf2b0.jpg
top_img: https://bu.dusays.com/2023/01/11/63be5983a519e.jpeg
tags:
- Electron
categories:
- Electron
---

# 使用vite构建electron项目

## 创建一个vite项目

```bash
pnpm create vite electron-demo
```

## 选择Vue+TypeScript

![](https://bu.dusays.com/2023/01/16/63c5042041456.png)

## 安装electron

```bash
pnpm i electron -D
pnpm i vite-plugin-electron -D
```

## 文件根目录新建electron文件夹，新增index.ts

![](https://bu.dusays.com/2023/01/16/63c5042048a59.png)

## 修改vite.config.ts

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-11 10:22:30
 * @LastEditTime: 2023-01-11 11:14:17
 * @LastEditors: Do not edit
 */
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron' // 引入插件
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), electron({
    entry: 'electron/index.ts' // 配置入口文件
  })],
})

```

## index.ts配置

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-11 10:40:48
 * @LastEditTime: 2023-01-11 13:37:57
 * @LastEditors: Do not edit
 */
import { app, BrowserWindow } from 'electron'

const createWindow = () => {
    const win = new BrowserWindow({
        webPreferences: {
            contextIsolation: false,
            nodeIntegration: true
        }
    })
    console.log(process.env.VITE_DEV_SERVER_URL);
    win.loadURL(`${process.env.VITE_DEV_SERVER_URL}`) // 指向项目启动地址
}

app.whenReady().then(createWindow)
```

## 配置package.json

删除`"type":"module"`

```json
{
  "name": "electron-demo",
  "private": true,
  "version": "0.0.0",
  "main": "dist/index.js", // 新增main，路径指向打包后生成的index.js
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "vue": "^3.2.45"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^4.0.0",
    "electron": "^22.0.0",
    "typescript": "^4.9.3",
    "vite": "^4.0.0",
    "vite-plugin-electron": "^0.11.1",
    "vue-tsc": "^1.0.11"
  }
}
```

## pnpm dev

![](https://bu.dusays.com/2023/01/16/63c5042124ac2.png)

# 打包Electron

## 安装electron-builder插件

```bash
pnpm install electron-builder -D
```

## package.json修改build打包命令

```json
{
  "name": "electron-demo",
  "private": true,
  "version": "0.0.0",
  "main": "dist/electron/index.js",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build && electron-builder", // 新增electron-builder，打包dmg应用
    "dist-win64": "electron-builder --win --x64", // 新增，打包exe程序
    "preview": "vite preview"
  },
  "dependencies": {
    "vue": "^3.2.45"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^4.0.0",
    "electron": "^22.0.0",
    "electron-builder": "^23.6.0",
    "typescript": "^4.9.3",
    "vite": "^4.0.0",
    "vite-plugin-electron": "^0.11.1",
    "vue-tsc": "^1.0.11"
  },
  "build": {
    "appId": "com.electron.desktop",
    "productName": "electron-demo",
    "asar": true,
    "copyright": "Copyright © 2023 electron",
    "directories": {
      "output": "release/"
    },
    "files": [
      "dist-electron"
    ],
    "mac": {
      "artifactName": "${productName}_${version}.${ext}",
      "target": [
        "dmg"
      ]
    },
    "win": {
      "target": [
        {
          "target": "nsis",
          "arch": [
            "x64"
          ]
        }
      ],
      "artifactName": "${productName}_${version}.${ext}"
    },
    "nsis": {
      "oneClick": false, // 创建一键安装程序还是辅助安装程序（默认是一键安装）
      "perMachine": false,
      "allowToChangeInstallationDirectory": true, // 是否允许修改安装目录 （仅作用于辅助安装程序）
      "deleteAppDataOnUninstall": false // 是否在卸载时删除应用程序数据（仅作用于一键安装程序）
    },
    "publish": [
      {
        "provider": "generic",
        "url": "http://127.0.0.1:8080"
      }
    ],
    "releaseInfo": {
      "releaseNotes": "版本更新的具体内容"
    }
  }
}
```

## 安装运行

![](https://bu.dusays.com/2023/01/16/63c504218f141.png)
