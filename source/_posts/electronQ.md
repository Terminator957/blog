---
title: Electron-Q&A
date: 2023-01-11 17:30:03
cover: https://bu.dusays.com/2023/01/11/63be1ba2cf2b0.jpg
top_img: https://bu.dusays.com/2023/01/11/63be5983a519e.jpeg
tags:
- Electron
categories:
- Electron
---

# electron踩坑排错

## 项目启动

项目框架，依赖及插件版本如下：

- `"vue": "^3.2.45"`
-  `"vite": "^4.0.0"`
- `"electron": "^22.0.2"`
- `"vite-plugin-electron": "^0.11.1"`

### 项目首次运行

![](https://bu.dusays.com/2023/01/16/63c5053b523e7.png)

### 删除package.json文件中`"type":"module"`，添加`"main": "dist/index.js"`后![](https://bu.dusays.com/2023/01/16/63c5053bd3ca4.png)

### 提示缺少入口文件，修改`vite.config.ts`

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-16 09:35:31
 * @LastEditTime: 2023-01-16 09:50:43
 * @LastEditors: Do not edit
 */
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), electron({
    // main: { 不需要外层套main对象
    entry: 'electron/index.ts'
    // }
  })],
})
```

### vite项目启动成功，electron仍然提示缺少入口文件

![](https://bu.dusays.com/2023/01/16/63c5053c17349.png)

我们的文件中配置的![](https://bu.dusays.com/2023/01/16/63c5053ba9689.png)

### 修改package.json中`"main": "dist-electron/index.js"`后

![](https://bu.dusays.com/2023/01/16/63c5053bdcb5b.png)

**启动成功！**

## 项目打包

打包安装后应用内白屏，加载打包后文件失败

![](https://bu.dusays.com/2023/01/16/63c5053bd757c.png)

```js
// 加载文件路径
file:///Applications/electron-demo.app/Contents/Resources/app.asar/index.html 
```

### 应该加载项目打包后dist文件夹下的index.html，修改electron下的index.ts中加载文件的路径

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-11 10:40:48
 * @LastEditTime: 2023-01-16 13:47:22
 * @LastEditors: Do not edit
 */
import { app, BrowserWindow } from 'electron'
import path from 'path'
const createWindow = () => {
    const win = new BrowserWindow({
        webPreferences: {
            devTools: true,
            contextIsolation: false,
            nodeIntegration: true
        }
    })
    // console.log(process.env.VITE_DEV_SERVER_URL);
    // console.log(app.isPackaged, 'app.isPackaged');
    if (app.isPackaged) {
        win.loadFile(path.join(__dirname, '../dist/index.html'))
        win.webContents.openDevTools();
    } else {
        win.loadURL(`${process.env.VITE_DEV_SERVER_URL}`)
        // 开发环境打开开发者工具
        win.webContents.openDevTools();
    }
    // win.loadURL('https://www.xiuji957.cn/')
}

app.whenReady().then(createWindow)
```

重新打包安装后,仍然加载不到文件

![](https://bu.dusays.com/2023/01/16/63c5053b6564d.png)

### `vite-plugin-electron ^0.11.1版本`将打包后的入口文件输出在`dist-electron`路径下，尝试修改插件的文件输出路径，将打包后的入口文件输出在`dist/electron`路径下，同步修改package.json中入口文件路径`"main": "dist/electron/index.js"`，重新打包

![](https://bu.dusays.com/2023/01/16/63c5053bd6e7f.png)

```bash
 ⨯ Application entry file "dist/electron/index.js" in the "/Users/xiuji/Desktop/workplace/own/electron-demo/release/mac/electron-demo.app/Contents/Resources/app.asar" does not exist. Seems like a wrong configuration.  failedTask=build stackTrace=Error: Application entry file "dist/electron/index.js" in the "/Users/xiuji/Desktop/workplace/own/electron-demo/release/mac/electron-demo.app/Contents/Resources/app.asar" does not exist. Seems like a wrong configuration.
```

### 修改package.json中打包配置项

```json
{
  "name": "electron-demo",
  "private": true,
  "version": "0.0.0",
  "main": "dist/index.js",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build && electron-builder",
    "dist-win64": "electron-builder --win --x64",
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
      "dist" // dist-electron修改为dist
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
      "oneClick": false,
      "perMachine": false,
      "allowToChangeInstallationDirectory": true,
      "deleteAppDataOnUninstall": false
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

### 修改`electron/index.ts`

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-01-11 10:40:48
 * @LastEditTime: 2023-01-16 14:19:55
 * @LastEditors: Do not edit
 */
import { app, BrowserWindow } from 'electron'
import path from 'path'
const createWindow = () => {
    const win = new BrowserWindow({
        webPreferences: {
            devTools: true,
            contextIsolation: false,
            nodeIntegration: true
        }
    })
    // console.log(process.env.VITE_DEV_SERVER_URL);
    // console.log(app.isPackaged, 'app.isPackaged');
    if (app.isPackaged) {
        win.loadFile(path.join(__dirname, '../index.html')) // 修改
        console.log(__dirname);
        win.webContents.openDevTools();
    } else {
        win.loadURL(`${process.env.VITE_DEV_SERVER_URL}`)
        // 开发环境打开开发者工具
        win.webContents.openDevTools();
    }
    // win.loadURL('https://www.xiuji957.cn/')
}

app.whenReady().then(createWindow)
```

### 重新安装运行

![](https://bu.dusays.com/2023/01/16/63c5053b42e11.png)

**资源加载成功，白屏解决！**
