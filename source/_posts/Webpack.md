---
title: Webpack构建Vue3项目
date: 2023-03-21 16:07:30
cover: https://bu.dusays.com/2023/03/22/641ab8d2175a8.jpg
top_img: https://bu.dusays.com/2023/03/22/641aba20928f2.webp
tags:
- Webpack
categories:
- Webpack
---

# 为什么手写webpack

目的只有一个：加深对webpack的了解，方便以后灵活运用webpack

# 初始化项目结构

参照vue-cli创建的初始项目创建如下目录：

```bash
├── public
│   └── index.html
├── src
│   ├── App.vue
│   ├── assets
│   ├── main.ts
│   └── views
└── webpack.config.js
```

执行如下命令初始化项目:

```bash
pnpm init
tsc --init
```

# 安装基础依赖

```bash
{
"name": "wepack-diy",
"version": "1.0.0",
"description": "",
"main": "index.js",
"scripts": {
"test": "echo \"Error: no test specified\" && exit 1",
"dev": "webpack-dev-server",//启动本地开发环境
"build": "webpack"// 打包项目
},
"keywords": [],
"author": "",
"license": "ISC",
"dependencies": {
    "@vue/compiler-sfc": "^3.2.47",
    "css-loader": "^6.7.3",
    "html-webpack-plugin": "^5.5.0",
    "sass": "^1.59.3", //处理sass
    "sass-loader": "^13.2.1",//处理sass文件
    "style-loader": "^3.3.2",//处理style样式
    "ts-loader": "^9.4.2",//处理ts文件
    "typescript": "^5.0.2",
    "vue": "^3.2.47",
    "vue-loader": "^17.0.1", //解析vue
    "webpack": "^5.76.2",
    "webpack-bundle-analyzer": "^4.8.0",// 打包完成后展示可视化打包分析报告
    "webpack-cli": "^5.0.1",// webpack3.0之后版本需要配套安装cli
    "webpack-dev-server": "^4.13.1"
  },
  "devDependencies": {
    "chalk": "4.0.0", // 控制台美化工具
    "progress-bar-webpack-plugin": "^2.1.0" // 控制台美化工具
  }
}
```

# 配置webpack.config.js

```js
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-03-20 10:31:36
 * @LastEditTime: 2023-03-21 10:49:55
 * @LastEditors: Do not edit
 */
const { Configuration } = require('webpack');
const path = require('path');
const chalk = require("chalk");
const htmlWebPackPlugin = require('html-webpack-plugin');
const { VueLoaderPlugin } = require('vue-loader');
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');
const ProgressBarPlugin = require('progress-bar-webpack-plugin');
/**
 * @type {Configuration} 通过注解的方式使用Configuration
*/

const config = {
    mode: 'development',
    resolve: {
        alias: {
            "@":path.resolve(__dirname,'/src') // 配置别名
        },
        extensions:['.js','.json','.ts','.vue','.sass','.css'] // 识别文件后缀
    },
    entry: './src/main.ts', // 配置入口文件
    module: {
        rules: [
            {
                test: /\.vue$/,
                use:"vue-loader"
            },
            {
                test: /\.scss$/,
                use:['style-loader','css-loader','sass-loader']
            },
            {
                test: /\.css$/,
                use:['style-loader','css-loader']
            },
            {
                test: /\.ts$/,  //解析ts
                loader: "ts-loader",
                options: {
                    configFile: path.resolve(process.cwd(), 'tsconfig.json'),
                    appendTsSuffixTo: [/\.vue$/]
                },
            }
        ]
    },
    output: {
        filename: '[hash].js', // 输出文件名
        path: path.resolve(__dirname, 'dist'), // 输出路径
        clean:true // 清除历史打包文件
    },
    devServer: {
        client: {
          overlay:true // 当出现编译错误或警告时，在浏览器中显示全屏覆盖。
        },
        port: 8088,
        open: true,
    },
    stats:'errors-only',//运行开发环境或打包项目时控制台输出信息，errors-only取消提示
    plugins: [
        new htmlWebPackPlugin({
            template:'./public/index.html'
        }),
        new VueLoaderPlugin(),
        new ProgressBarPlugin({
            width: 50, 					 // 默认20，进度格子数量即每个代表进度数，如果是20，那么一格就是5。
            format: chalk.blue.bold("build") + chalk.yellow('[:bar] ') + chalk.green.bold(':percent') + ' (:elapsed秒)',
            stream: process.stderr,        // 默认stderr，输出流
            complete: "#",                 // 默认“=”，完成字符
            clear: true,                  // 默认true，完成时清除栏的选项
            renderThrottle: "",            // 默认16，更新之间的最短时间（以毫秒为单位）
            callback() {                   // 进度条完成时调用的可选函数
                console.log(chalk.red.bold("完成"))
            }
        }),
        new BundleAnalyzerPlugin(), // 可视化打包分析报告
    ]，
   	externals: {
        vue: "Vue" //CDN 引入，优化项目打包体积，推荐个人项目使用
    },
}

module.exports = config
```
