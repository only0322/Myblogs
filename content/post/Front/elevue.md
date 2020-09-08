---
title: "Vue+Electron环境搭建"
date: 2020-06-22T20:54:59+08:00
draft: true
---



## 一、创建项目

vue init simulatedgreg/electron-vue

## 二、报错处理

先npm install

然后修改webpack.web.config.js：

```js
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: path.resolve(__dirname, '../src/index.ejs'),
      templateParameters(compilation, assets, options) {
        return {
          compilation: compilation,
          webpack: compilation.getStats().toJson(),
          webpackConfig: compilation.options,
          htmlWebpackPlugin: {
            files: assets,
            options: options
          },
          process,
        };
      },
      minify: {
        collapseWhitespace: true,
        removeAttributeQuotes: true,
        removeComments: true
      },
      nodeModules: false
    }),

```

webpack.render.config.js：

```js
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: path.resolve(__dirname, '../src/index.ejs'),
      minify: {
        collapseWhitespace: true,
        removeAttributeQuotes: true,
        removeComments: true
      },
      templateParameters(compilation, assets, options) {
        return {
          compilation: compilation,
          webpack: compilation.getStats().toJson(),
          webpackConfig: compilation.options,
          htmlWebpackPlugin: {
            files: assets,
            options: options
          },
          process,
        };
      },
      nodeModules: process.env.NODE_ENV !== 'production'
        ? path.resolve(__dirname, '../node_modules')
        : false
    }),


```

替换对应的函数即可



## 三、白名单

如果发现某个模块明明安装了但是说未找到，需要添加白名单。

webpack.redener.config.js里面**const**下添加

```js
let whiteListedModules = [
  'vue',
		'axios',
		'vue-electron',
		'vue-router',
		'vuex',
		'vuex-electron',
    'element-ui',
    'view-design',
]
```