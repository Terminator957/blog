---
title: webpack优化项目打包后体积
date: 2023-04-11 14:07:08
cover: https://bu.dusays.com/2023/03/22/641ab8d2175a8.jpg
top_img: https://bu.dusays.com/2023/03/22/641aba20928f2.webp
tags:
- Webpack
categories:
- Webpack
---

# webpack优化项目打包后体积

Webpack 是一个强大的模块打包工具，可以将多个模块打包成一个或多个文件，提高前端开发的效率和性能。下面是一些常见的 Webpack 优化打包技巧：

1. 减少打包文件的体积：可以通过压缩代码、移除未使用的代码、使用 Tree Shaking 技术等方式减小打包文件的体积。
2. 使用代码分离：将代码拆分成更小的模块，可以实现按需加载，提高页面加载速度。
3. 使用缓存机制：通过给打包文件加上 hash 值，可以实现浏览器缓存，减少不必要的网络请求。
4. 使用 DLLPlugin 进行依赖分析：可以将不经常变化的依赖单独打包，避免重复打包浪费时间。
5. 配置 externals：将一些不需要打包的依赖通过 externals 配置排除在打包之外，可以减小打包文件体积。
6. 使用 ParallelUglifyPlugin：使用多进程并行压缩代码，加快打包速度。
7. 使用 Happypack：使用多进程并行处理文件，加快编译速度。
8. 使用 CDN：将一些公共资源通过 CDN 加载，可以减轻服务器压力，提高访问速度。
9. 开启 Scope Hoisting：将模块的作用域提升到最高级别，减少函数声明代码，优化打包结果。

# 减少打包文件体积

**移除未使用的代码**

通过 tree shaking 技术可以移除未使用的代码，从而减少打包体积。Webpack 默认支持 tree shaking 技术，只需要确保代码中使用 ES6 模块化语法，并开启 production 模式即可。例如：

```js
import { add } from './math';

console.log(add(1, 2));
```

如果 math.js 中的 subtract 函数未被使用，Webpack 会将其移除。

**代码压缩**

通过使用 UglifyJsPlugin 插件可以将代码进行压缩，从而减少打包体积。例如：

```js
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
  // ...
  optimization: {
    minimizer: [new UglifyJsPlugin({
      test: /\.js(\?.*)?$/i,// 指定应用 UglifyJsPlugin 的文件，这里我们使用正则表达式匹配所有以 .js 结尾的文件
      exclude: /\/node_modules/,// 指定应该排除的文件，这里排除了 node_modules 目录
      cache: true,// 开启缓存，以便下次构建时可以重复使用缓存提高构建速度
      parallel: true,// 开启并行压缩，以提高构建速度
      sourceMap: true,// 生成 source map 文件，以便在调试时定位源代码位置，生产环境建议关闭
      uglifyOptions: {// 指定 UglifyJS 的选项，可以配置一些压缩选项。在示例中，我们开启了压缩选项并移除了一些不必要的代码
        compress: {
          warnings: false,// 指定是否在压缩代码时输出警告信息，false表示关闭警告信息的输出。需要注意的是，虽然关闭警告信息可以提高代码压缩的速度，但在某些情况下可能会影响代码的质量，因此需要根据实际情况进行取舍
          drop_console: true,// true-移除console.log
          drop_debugger: true,// true-移除生产环境中的调试语句，如debugger
          pure_funcs: ['console.log'],// 指定打包时不移除的纯函数，生产环境建议移除
        },
        output: {
          comments: false,
        },
      },
    })],
  },
  // ...
};
```

通过配置 UglifyJsPlugin，我们可以轻松地将 JavaScript 代码压缩成更小的体积，从而加快页面加载速度。

# 使用代码分离

代码分离是将代码分成更小的块，以便更好地利用浏览器的缓存机制，从而提高页面加载速度。Webpack 提供了多种代码分离的方式，包括入口起点、动态导入和代码分割插件。

**入口起点：**

可以将代码分成多个入口起点，这样每个入口起点都会被打包成一个独立的文件。

```js
module.exports = {
  entry: { // 自定义入口起点，webpack会把entry中每个入口起点打包成独立的文件
    app: './src/app.js',
    vendor: './src/vendor.js',
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

**动态导入：**

可以使用 ES6 的 import() 语法实现动态导入，从而将某个模块打包成一个独立的文件。

```js
import(/* webpackChunkName: "lodash" */ 'lodash').then(_ => {
  // ...
});
```

这里使用 import() 导入了 lodash 模块，Webpack 会将 lodash 模块打包成一个独立的文件，并在运行时动态加载。

**代码分割插件：**

Webpack 提供了多个代码分割插件，包括 CommonsChunkPlugin、SplitChunksPlugin、MiniCssExtractPlugin 等。其中，SplitChunksPlugin 是最常用的代码分割插件，它可以根据一些策略将代码分割成更小的块。

```js
module.exports = {
  // ...
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendors: {// 用于打包第三方依赖库
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: -10,
          reuseExistingChunk: true,
        },
        default: {// 用于打包应用程序代码
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true,
        },
      },
    },
  },
  // ...
};
```

上面使用 optimization.splitChunks 配置项，将代码分割成两个块，一个是 vendors，用于打包第三方依赖库，一个是 default，用于打包应用程序代码。SplitChunksPlugin 会根据一些策略将代码分割成更小的块，从而更好地利用浏览器的缓存机制。

# 使用缓存机制

Webpack 提供了缓存机制，可以加快二次打包速度。在 Webpack 中，缓存机制主要分为两种：

**HashedModuleIdsPlugin**

这个插件可以根据模块的相对路径生成一个 Hash 作为模块的唯一标识符，如果模块没有发生变化，那么它的 Hash 值也不会变化，从而可以有效地利用浏览器的缓存机制。

使用方法：

```js
const webpack = require('webpack');

module.exports = {
  // ...
  plugins: [
    new webpack.HashedModuleIdsPlugin(),
  ],
  // ...
};
```

**cache-loader**

cache-loader 可以将一些性能开销较大的 loader 的结果缓存起来，从而在二次打包时可以直接使用缓存结果，而不需要重新执行这些 loader。cache-loader 需要安装并配置在 loader 的前面。

使用方法：

```js
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          'cache-loader',
          'babel-loader',
        ],
        exclude: /node_modules/,
      },
    ],
  },
  // ...
};
```

在 babel-loader 前面添加了 cache-loader，这样在执行 babel-loader 时，如果缓存中存在对应的结果，就会直接使用缓存结果，从而加快打包速度。

使用缓存机制可以加快二次打包速度，从而提高开发效率，**但是在修改了代码后，需要手动清除缓存才能看到最新的打包结果。**

# 使用 DLLPlugin 进行依赖分析

使用 DllPlugin 插件将不经常变化的依赖单独打包：

```js
const path = require('path');
const webpack = require('webpack');

module.exports = {
  // ...
  entry: {
    vendor: ['react', 'react-dom', 'lodash'],
  },
  output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].js',
    library: '[name]',
  },
  plugins: [
    new webpack.DllPlugin({
      name: '[name]',
      path: path.join(__dirname, 'dist', '[name]-manifest.json'),
    }),
  ],
  // ...
};
```

使用 AddAssetHtmlPlugin 插件将 DLL 文件注入到 HTML 中：

```js
const path = require('path');
const AddAssetHtmlPlugin = require('add-asset-html-webpack-plugin');

module.exports = {
  // ...
  plugins: [
    new AddAssetHtmlPlugin({
      filepath: path.join(__dirname, 'dist', 'vendor.js'),
    }),
  ],
  // ...
};
```

# 配置 externals

在 Webpack 中，可以通过配置 externals 来告诉 Webpack 哪些模块不需要打包，而是直接从外部引入。

例如，你想在打包的过程中不将 React 和 React-DOM 打包进去，而是在运行时通过全局变量引入。那么可以通过 externals 来配置：

```js
module.exports = {
  // ...
  externals: {
    react: 'React',
    'react-dom': 'ReactDOM',
  },
  // ...
};
```

这样，Webpack 在打包时就会忽略 react 和 react-dom 模块，并且在使用这些模块时会从全局变量 React 和 ReactDOM 中引入。

注意，externals 配置只是告诉 Webpack 哪些模块不需要打包，但是在使用这些模块时，还需要在 HTML 页面中通过 script 标签引入相应的库文件。你可以使用 html-webpack-plugin 插件来自动将这些文件引入到 HTML 页面中。例如：

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  // ...
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify: {
        removeComments: true,
        collapseWhitespace: true,
      },
    }),
  ],
  // ...
};
```

在这个示例中，我们使用了 html-webpack-plugin 插件，并将它添加到 plugins 中。通过指定 template 选项来告诉插件使用哪个 HTML 模板文件。这时只需在html文件中使用cdn引入js等文件即可：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Vue App</title>
  </head>
  <body>
    <div id="app"></div>
    <!-- cdn引入vue资源 -->
    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
  </body>
</html>
```

在打包时，插件会自动将打包后的 JS 文件和指定的库文件添加到 HTML 页面中。

# 使用 ParallelUglifyPlugin

使用 ParallelUglifyPlugin 插件并行压缩代码：

```js
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin');

module.exports = {
   plugins: [
    // ... 其他插件
    new ParallelUglifyPlugin({
      cacheDir: '.cache/',//指定缓存目录，可以加快二次打包速度
      uglifyJS: {//压缩选项，可以根据需要进行配置。
        output: {
          comments: false,
        },
        compress: {
          warnings: false,
          drop_console: true,
          collapse_vars: true,
          reduce_vars: true,
        },
      },
    }),
  ]
}
```

**注意：如果你已经使用了 UglifyJsPlugin 进行代码压缩，那么不要同时使用 ParallelUglifyPlugin，否则可能会出现冲突。**
