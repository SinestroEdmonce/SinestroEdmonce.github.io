---
layout: post
title: Webpack介绍和配置详解
categories: [Web, Front-end]
description: Webpack项目管理工具可以非常方便地帮助前端开发人员管理不同类型的文件，例如各种语言的代码和图片等。通过使用Webpack可以简化前端开发时的项目整合，可以比较容易地构建或发布项目。本文主要介绍Webpack和其相关的一些基本配置。
keywords: Web, Webpack, Front-end
---

本质上，`Webpack`是一个现代Javascript应用程序的静态模块打包器（Module Bundler）。当`Webpack`处理应用程序时，它会递归地构建一个依赖关系图（Dependency Graph），其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个`bundle`。从`Webpack v4.0.0`开始，可以不用引入一个配置文件。然而，`Webpack`仍然还是高度可配置的。

首先我们来看一下`Webpack`官网给出的用于介绍的图片：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/webpack_intro.jpg">
</div>

下面我们依据官网给出的用于介绍`Webpack`的图片来简要地介绍`Webpack`这个项目管理工具。

## `Webpack`基本介绍

### 概念的引入

- **思考：** 在网页中，我们经常会引入哪些常见的静态资源？

    > **JS**  
    > .js .jsx .coffee .ts *[Typescript]*  
    >
    > **CSS**  
    > .css .less .sass .scss  
    >
    > **Images**  
    > .jpg .png .gif .bmp .svg  
    >
    > **Fonts**  
    > .svg .ttf .eot .woff .woff2  
    >
    > **模板文件**  
    > .ejs .jade .vue *[这是在webpack中定义组件的方式，推荐这么用]*


- **问题：** 网页中静态资源多了以后存在的问题？

  - 网页加载速度变慢，因为要多次重复的发送资源请求
  - 要处理错综复杂的依赖关系

- **方案：** 如何解决上述两个问题？
  
  - 合并、压缩、精灵图、图片的Base64编码
  - 其他更高效的解决方案如下：可以使用之前学过的`requireJS`、也可以使用`Webpack`可以解决各个包之间的复杂依赖关系
    - 使用`Gulp`， 是基于`task`任务的；
    - 使用`Webpack`， 是基于整个项目进行构建的；

### 什么是`Webpack`

`Webpack`是一个前端的**项目构建工具**，它是基于`Nodejs`开发出来的一个前端工具

### `Webpack`的安装

首先新建项目`webpack-basic`，初始化`package.json`文件。输入如下命令：

```bash
mkdir webpack-basic   # 第一步创建项目webpack-basic
cd ./webpack-basic    # 进入项目路径   
npm init             # 初始化package.json 文件
```

然后安装`Webpack`相关包：

```bash
npm install webpack webpack-cli webpack-dev-server
```

命令完成后，我们会发现项目中多了一个`node_modules`文件夹，该文件夹是用来存放项目中安装的依赖包。

## `Webpack`基本使用

### 搭建项目结构

- 为项目创建三个文件夹：
  
  > `config`：配置文件夹  
  > `dist`：编译文件夹  
  > `src`：项目代码文件夹

  输入命令：
  ```bash
  mkdir config dist src
  ```
  如图增加了三个文件夹：

  <div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/webpack_project_structure.jpg">
  </div>
  

- 创建项目入口html、js文件：

  输入命令：
  ```bash
  touch dist/index.html src/index.js  # 该命令window系统不支持，请手动创建
  ```

  <div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/webpack_project_files.jpg">
  </div>

### 利用`webpack`构建项目

在`package.json` -> `scripts`中栏目配置`build`的运行命令。配置如下所示：

```json
{
  "name": "webpack-basic",
  "version": "1.0.0",
  "description": "",
  "author": "sinestro",
  "license": "ISC",
  "main": "index.js",
  "scripts": {  // npm run 运行的命令配置
    "build": "webpack"  
  },
  "dependencies": {   // 项目所需要的第三方安装包
    "webpack": "^4.29.6",
    "webpack-cli": "^3.2.3",
    "webpack-dev-server": "^3.2.1"
  }
}
```

配置完成后运行命令：

```bash
npm run build
```

执行命令后会发现`dist`文件夹下多了个`bundle.js`。命令运行时，`npm`取出`build`对应配置的命令在`node`环境中执行，`Webpack`打包默认入口为`src/index.js`，默认生成路径是`dist/bundle.js`，默认打包模式为`development`， 打包模式总共有两种：

  - `development`（开发环境）
  - `production`（生产环境）

生成环境下`bundle.js`的体积更小。

### `Webpack`配置介绍

从`webpack v4.0.0`开始，可以不用引入一个配置文件。然而，`Webpack`仍然还是高度可配置的。在开始前需要先理解四个核心概念：

- 入口（entry）：打包文件的入口（对应上述默认入口`src/index.js`），支持多入口
- 输出（output）：打包压缩文件后的输出位置
- 加载器（loader）：针对对应文件进行转义，解析成浏览器识别的文件
- 插件（plugins）：插件目的在于解决`loader`无法实现的其他事。

讲了概念过后，接下来我们创建一个`webpack.dev.js`，也可以命名为`webpack.config.js`, 做一个简单的配置解析。

```bash
touch config/webpack.dev.js   # 该命令window系统不支持，请手动创建
```

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/webpack_config.jpg">
</div>

#### 配置文件内容及配置介绍

`webpack.dev.js`这个配置文件的基本内容如下，其中`html-webpack-plugin`是负责打包生成HTML文件的插件：

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const path = require('path');        // node内置path模块，该模块主要集成文件系统路径操作API

const config = {
    mode: 'development',        // webpack打包的模式，上述命令里有介绍，也可以在本配置中配置
    entry: {    // js的入口文件，支持多入口
        main: path.resolve(__dirname,'../src/index.js')
    },
    output: {   // js打包压缩后的出口文件，多入口时对应的配置应做相对变化
        path: path.resolve(__dirname,'../dist'),
        filename:'bundle.js'
    },
    module: {
        rules: [] // 配置loder使用的规则、作用范围、控制输出的名称、位置等；主要作用是编译，解析文件； 暂时不使用loader
    },
    plugins: [
        new HtmlWebpackPlugin({template: './src/index.html'})  // 根据项目提供HTML模板，生成新页面，并将对应的输出打包压缩输出的js，链接到页面中；
    ]
};

module.exports = config;
```

接下来，我们使用`npm install html-webpack-plugin`将项目中使用的未安装的包安装了。然后我们重新配置下webpack打包的命令，在`package.json`文件中进行配置：

```json
{
  "name": "webpack-basic",
  "version": "1.0.0",
  "description": "",
  "author": "sinestro",
  "license": "ISC",
  "main": "index.js",
  "scripts": {  // npm run 运行的命令配置
    "build": "webpack --config config/webpack.dev.js"  // 打包命令的配置 --config表示以何种文件配置方式启动项目打包
  },
  "dependencies": {   // 项目所需要的第三方安装包
    // ...
  }
}
```

```bash
npm run build
```

运行过后的结果如图所示：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/webpack_build_result.jpg">
</div>

打包后生成了`index.html`并且引入了`output`中的打包后的JS文件。

#### `webpack-dev-server`启动服务及命令参数

`webpack-dev-server`的作用是打包项目后，并启动一个服务，可以直接在浏览器查看项目页面，并且实时监听项目内容的修改。具体配置如下：

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const path = require('path');        // node内置path模块，该模块主要集成文件系统路径操作API

const config = {
    mode: 'development',        // webpack打包的模式，上述命令里有介绍，也可以在本配置中配置
    entry: {    // js的入口文件，支持多入口
        main: path.resolve(__dirname,'../src/index.js')
    },
    output: {   // js打包压缩后的出口文件，多入口时对应的配置应做相对变化
        path: path.resolve(__dirname,'../dist'),
        filename:'bundle.js'
    },
    module: {
        rules: [] // 配置loder使用的规则、作用范围、控制输出的名称、位置等；主要作用是编译，解析文件； 暂时不使用loader
    },
    plugins: [
        new HtmlWebpackPlugin({template: './src/index.html'})  // 根据项目提供HTML模板，生成新页面，并将对应的输出打包压缩输出的js，链接到页面中；
    ],
    devServer: {        //webpack-dev-server配置（仅开发环境需要）
        contentBase: path.join(__dirname, './dist'), //编译打包文件的位置
        publicPath: '/',            // bunlde.js等资源文件的url访问路径
        port: 8080,                 // 服务器端口号
        host: '0.0.0.0',
        proxy: {},                  // 代理列表
        compress: true,
        historyApiFallback: true,   // 开启服务器history重定向模式
	}
};
module.exports = config;
```

`package.json`文件添加开发启动命令，如下：

```json
{
    "name": "webpack-1.0",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "build": "webpack --config config/webpack.dev.js",
      "dev" : "webpack-dev-server --config config/webpack.dev.js --color --progress --hot" // --color 启用彩色打印  --progerss 启用进程监测 --hot 启用热加载
    },
    "author": "sinestro",
    "license": "ISC",
    "dependencies": {
      // ...
    }
}
```

### `Webpack`项目中常用`loader`以及配置。

#### Javascript方面的所运用的`loader`

```json
"babel@core" // JavaScript编译核心babel@core（必须）
"babel@loader" // JavaScript编译babel@loader（必须）
"babel@plugin-transform-runtime" // JavaScript 运行环境转换器
"babel@preset-stage-0" // ES6,ES7语法转换ES5编译器（ES7的提案，且包含了stage-2，stage-1所有功能）
"babel@polyfill" // IE低版本扩展JS某些API（如promise  object.defindproperty）（根据项目需求是否兼容IE低版本安装）
"babel@runtime" // 提供将ES6转变成ES5运行环境,并不污染全局完成代码填充
"babel@plugin-transform-decorators-legacy" // JavaScript 装饰器语法编译器（使用@语法需添加）
"babel@plugin-transform-react-jsx" // react jsx语法转换器
"babel@plugin-react-html-attrs" // react dom属性识别语法转换器
"babel@preset-react" // react语法编译器
```

以上都是`babel-loader`相关的`loader`包，当然项目使用`babel-loader`前，还需要配置一个`babel`配置，告知`babel-loader`在使用时对应的语法文件采用何种方式去处理。该配置文件名称为`.babelrc`，`.babelrc`配置如下：

```json
{
    "presets": [
        "env",
        "react",
        "stage-0"
    ],
    "plugins": [
        "transform-decorators-legacy",
        "transform-react-jsx",
        [
            "transform-runtime",
            {
                "helpers": false,
                "polyfill": false,
                "regenerator": true,
                "moduleName": "babel-runtime"
            }
        ],
        "react-html-attrs"
    ]
}
```

然后在`webpack.dev.js` -> `module.rules`选项添加`loader`规则，添加配置如下：

```javascript
module: {
    rules: [{
      test: /\.js$/,
      loader: "babel-loader",
      options: {
        cacheDirectory: false,
        babelrc: true
      },
      exclude: path.resolve(__dirname, 'node_modules/'),
    }]
}
```

需要注意的是**官方默认`babel-loader`和`babel` 对应的版本需要一致，即`babel-loader`需要搭配最新版本`babel`**。所以如果出现类似下述的错误：

```
Error: Cannot find module '@babel/core'
Error: Cannot find module '@babel/plugin-transform-react-jsx'
```

必须首先要检查版本是否一致。一般有两种解决方法：

```bash
# 回退低版本，旧版本的babel是类似于babel-xxxx这样的格式
npm install -D babel-loader@7 babel-core babel-preset-env

# 更新到最高版本，可以先卸载当前已经安装的版本，新版本的babel是类似于babel@xxxx这样的格式
npm install -D babel-loader @babel/core @babel/preset-env webpack
```

#### CSS方面的所运用的`loader`

```json
"style-loader"              // style-loader（必须）
"css-loader"                // css-loader （必须）
"autoprefixer"              // css3自动补全功能
"less":                     // 以下都是使用less相关语法加载库
"less-loader"
"node-sass": "^4.5.3",      // 以下都是使用sass，scss相关语法加载库
"sass-loader": "^6.0.6",
```

在`webpack.dev.js` -> `module.rules`选项添加`loader`规则，添加配置如下：

```javascript
module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: 'style-loader',
            options: { sourceMap: false }
          },
          {
            loader: 'css-loader',
            options: { sourceMap: false}
          },
        ]
      },
      {
        test: /\.(scss|sass)$/,
        use: [
          {
            loader: 'style-loader',
            options: {sourceMap: false}
          },
          {
            loader: 'css-loader',
            options: {sourceMap: false}
          },
          {
            loader: 'sass-loader',
            options: {sourceMap: false}
          },
        ]
      }
    ]
}
```

在`Webpack`中也可以将CSS文件模块化。但是需要注意的是，由于很多第三方库也有许多CSS文件，如果全部模块化，则大量第三方库的CSS文件会失效。所以一般来说，为了仅在自己的项目代码中能使用CSS文件的模块化，可以将自己写的CSS文件替换为SCSS文件，这样通过模块化SCSS文件来避免影响第三方库的CSS文件。

#### 资源文件加载的所运用的`loader`

```json
"file-loader"   // 文件处理loader
"url-loader"    // url链接处理loader
```
在`webpack.dev.js` -> `module.rules`选项添加`loader`规则，添加配置如下：

```javascript
module: {
    rules: [
      {
        test: /\.(png|jpg|gif|ico|jpeg|bmp|swf)$/,
        exclude: path.resolve(__dirname, 'node_modules/'),
        use: [{
          loader: 'url-loader',
          options: {
            limit: 10000,
            name(file) { //图片超过 10000 Kb处理
              return file.replace(/.*assets(\/|\\)/, '').replace(/\\/g, '/')
            }
          }
        }]
      },
      {
        test: /\.(woff|woff2|svg|eot|ttf|otf)$/,
        exclude: path.resolve(__dirname, 'node_modules/'),
        use: [{
          loader: 'file-loader',
          options: {
            limit: 10000,
            name: 'fonts/[name].[ext]'
          }
        }]
      }
    ]
}
```

### Hint：`Webpack`配置

下面的代码是我自己在`React`项目中所用的配置文件：

```javascript
const htmlWebpackPlugin = require('html-webpack-plugin'); 
const path = require('path');

const htmlPlugin = new htmlWebpackPlugin({
    template: path.join(__dirname, "./src/index.html"),
    filename: "index.html"
});

const config = {
    mode: "development",
    // mode: "production",   
    entry: {    
        main: path.resolve(__dirname,'./src/index.js')
    },
    output: {   
        path: path.resolve(__dirname,'./dist'),
        filename: 'bundle.js',
        // Only for the production
        // publicPath: "/dist/"
    },
    module: {
        rules: [
            {
                test: /.jsx?$/,
                use: "babel-loader",
                include: [
                    path.resolve(__dirname, 'src')
                ],
                exclude: /node_modules/
            }, 
            {
                test: /\.css$/,
                use: ["style-loader", "css-loader"]
            },
            {
                test: /\.scss$/,
                use: [
                    {
                        loader: "style-loader"
                    }, 
                    {
                        loader: "css-loader",
                        options: {
                            modules: {
                                localIdentName: "[name]__[local]___[hash:base64:7]"
                            }
                        }
                    }, 
                    {
                        loader: "sass-loader"
                    }
                ],
                exclude: /node_modules/
            },
            {
                test: /\.(png|jpg|jpeg|gif|svg|woff|woff2)$/,
                use: ["url-loader"]
            },
            {
                test: /\.(eot|ttf|wav|mp3)$/,
                use: ["file-loader"]
            }
        ]
    },
    resolve: {
        extensions: [".js", ".jsx", ".json"],
        alias: {
            "@": path.join(__dirname, "./src")
        }
    },
    plugins: [
        htmlPlugin
    ],
    devServer: {
        historyApiFallback: true,
        hot: true,
        inline: true,
        progress: true,
        port: 8000,
        proxy: {
            "/G/*": {
                target: "http://localhost:8001",
                changeOrigin: true,
                secure: false
            },
            "/NY/*": {
                target: "http://localhost:8001",
                changeOrigin: true,
                secure: false
            },
            "/public/*": {
                target: "http://localhost:8001",
                changeOrigin: true,
                secure: false
            }
        }
    },
    // Only need in development mode
    devtool: "eval-source-map"
};

module.exports = config;
```

## 参考

- [webpack 4.x.x 搭建项目完整详解步骤](https://juejin.im/post/5c7f28056fb9a049bb7d169a)
- [Webpack基本使用（详解）](https://juejin.im/post/5d2fd19be51d4576bc1a0eb0)
- [webpack.config.js配置问题](https://www.cnblogs.com/soyxiaobi/p/9554565.html)