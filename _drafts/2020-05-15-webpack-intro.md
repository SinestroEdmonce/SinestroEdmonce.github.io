---
layout: post
title: template page
categories: [cate1, cate2]
description: some word here
keywords: keyword1, keyword2
---

前言
本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。
从 webpack v4.0.0 开始，可以不用引入一个配置文件。然而，webpack 仍然还是高度可配置的。在开始前你需要先理解四个核心概念：

入口(entry)
输出(output)
loader
插件(plugins)

本文档旨在给出webpack的入门使用。
一、新建项目webpack1.0.0，初始化package.json文件
输入命令
mkdir webpack1.0.0   // 第一步创建项目webpack1.0.0
cd ./webpack1.0.0    // 进入项目路径   
npm  init            // 初始化package.json 文件
复制代码二、项目安装webpack webpack-cli webpack-dev-server插件，为webpack项目提供相应的安装包。
输入命令：
npm install webpack webpack-cli webpack-dev-server
复制代码命令完成后，我们会发现项目中多了一个 node_modules 文件夹，该文件夹是用来存放项目中安装的依赖包，无需画较大的精力关注，有深入学习的同学可以在后期慢慢深入了解如何构建一个npm安装包，本文不作介绍。

三、完整项目目录及项目文件
3.1为项目创建三个文件夹（config --配置文件夹 dist --编译文件夹 src --项目正文文件夹）
输入命令：
mkdir config dist src
复制代码如图增加了三个文件夹：

3.2、创建项目入口html、js文件
输入命令：
touch dist/index.html src/index.js
//该命令window系统不支持，请手动创建
复制代码
四、执行webpack 打包命令。
4.1 在package.json -> scripts 栏目 配置build的运行命令。配置如图所示

配置完成后运行命令：
npm run  build
复制代码执行命令后大家会发现，大家会发现dist文件夹下多了个main.js，命令运行时，npm取出build对应配置的命令在node环境中执行，webpack打包默认入口为src/index.js, 默认打包模式为 --model development， 打包模式总共有两种：

--model development（开发环境）
--model production（生产环境）

生成环境的命令配置如图：

配置完成后运行命令：
npm run  build:prod
复制代码命令执行后结果如上述一致，黄色的警告提示消失了，打包的文件会更小。
以上操作完成后webpack的初步打包过程就有所了解啦，但是真实项目中webpack怎么可能这么简单的使用，接下来我们要讲讲webpack的重点了，从以上的结果我们不难发现webpack有编码打包编译的功能，但是要在大型项目中运用自如我们就要掌握webpack的打包配置文件书写了，下面来一一介绍webpack的配置文件具体内容。
五、webpack 配置文件结构介绍
5.1 webpack打包配置文件介绍及启用
从 webpack v4.0.0 开始，可以不用引入一个配置文件。然而，webpack 仍然还是高度可配置的。在开始前你需要先理解四个核心概念：

入口 ------ (entry)   打包文件的入口（对应上述默认入口src/index.js），支持多入口
输出 ------ (output)  打包压缩文件后的输出位置
加载器 ---- (loader)  针对对应文件进行转义，解析 成浏览器识别的文件
插件 ------ (plugins) 插件目的在于解决 loader 无法实现的其他事。

讲了概念过后，接下来我们创建一个webpack.dev.js, 做一个简单的配置解析。
touch config/webpack.dev.js
//该命令window系统不支持，请手动创建
复制代码
配置文件类容，及配置介绍
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
const path = require('path');        //node内置path模块，该模块主要集成文件系统路径操作API

const config = {
    mode: 'development',        //webpack打包的模式，上述命令里有介绍，也可以在本配置中配置
    entry: {    //js的入口文件，支持多入口 注释①
        main: path.resolve(__dirname,'../src/index.js')
    },
    output: {   //js打包压缩后的出口文件，多入口时对应的配置应做相对变化 注释②
        path: path.resolve(__dirname,'../dist'),
        filename:'bundle.js'
    },
    module: {
        rules: [] // 配置loder使用的规则、作用范围、控制输出的名称、位置等；主要作用是编译，解析文件； 暂时不使用loader
    },
    plugins: [
        new HtmlWebpackPlugin({template: './src/index.html'})  //根据项目提供HTML模板，生成新页面，并将对应的输出打包压缩输出的js，链接到页面中；详细配置见注释④
    ]
};

module.exports = config;
复制代码下面对代码的注释出做一个详细介绍的链接导向（建议在本文完全看完后、再去做详细深入了解）：
打包入口详解-注释①
打包输出详解-注释②
HTML模板插件详解-注释④
下面我们把上述 一 ~ 三节完成的项目里的项目内容进行下调整，我们把dist中文件清空，并在src文件夹中添加 index.html 模板HTML，完成后文件目录如图：

接下来，我们使用 npm install html-webpack-plugin 将项目中使用的未安装的包安装了。剩下的操作是我们重新配置下webpack打包的命令，第三节我们有介绍在package.json 文件中配置运行命令；下面我们贴出package.json代码：
{
  "name": "webpack-1.0",
  "version": "1.0.0",
  "description": "webpacke基础训练",
  "author": "张啸",
  "license": "ISC",
  "main": "index.js",
  "scripts": {  //npm run 运行的命令配置
    "build": "webpack --config config/webpack.dev.js"  //打包命令的配置 --config表示以何种文件配置方式启动项目打包
  },
  "dependencies": {   //项目所需要的第三方安装包
    "webpack": "^4.29.6",
    "webpack-cli": "^3.2.3",
    "webpack-dev-server": "^3.2.1"
  }
}
复制代码命令行运行下面命令：
npm run build
复制代码运行过后的结果如图所示：

打包后生成了index.html 并且引入了output中的打包压缩后的JS文件。综合上述一个简单的项目打包就完成了，但是大家有没有发现，这些只是打包了，并不适合在开发的过程中使用，那在开发的时候我们更想直接去流浪器查看开发项目的直接页面。这时候我们就要用到webpack-dev-server插件了，这个插件是干嘛的呢？
5.2 webpack-dev-server 启动服务及命令参数
webpack-dev-server的作用是打包项目后，并启动一个服务，可以直接在浏览器查看项目页面。
具体配置如下，webpack.dev.js添加devserver修改下：
const HtmlWebpackPlugin = require('html-webpack-plugin'); //通过 npm 安装
const path = require('path');        //node内置path模块，该模块主要集成文件系统路径操作API

const config = {
    mode: 'development',        //webpack打包的模式，上述命令里有介绍，也可以在本配置中配置
    entry: {    //js的入口文件，支持多入口 注释①
        main: path.resolve(__dirname,'../src/index.js')
    },
    output: {   //js打包压缩后的出口文件，多入口时对应的配置应做相对变化 注释②
        path: path.resolve(__dirname,'../dist'),
        filename:'bundle.js'
    },
    module: {
        rules: [] // 配置loder使用的规则、作用范围、控制输出的名称、位置等；主要作用是编译，解析文件； 暂时不使用loader
    },
    plugins: [
        new HtmlWebpackPlugin({template: './src/index.html'})  //根据项目提供HTML模板，生成新页面，并将对应的输出打包压缩输出的js，链接到页面中；详细配置见注释④
    ],
    devServer: {        //webpack-dev-server配置（仅开发环境需要）
		contentBase: path.join(__dirname, './dist'), //编译打包文件的位置
		publicPath: '/',    
		port: 8080,                 //服务器端口号
		host: '0.0.0.0',
		proxy: {},                  //代理列表
		compress: true,
		historyApiFallback: true,   //开启服务器history重定向模式
	}
};
module.exports = config;
复制代码package.json 文件添加开发启动命令，如下：
{
  "name": "webpack-1.0",
  "version": "1.0.0",
  "description": "webpacke基础训练",
  "main": "index.js",
  "scripts": {
    "build": "webpack --config config/webpack.dev.js",
    "dev" : "webpack-dev-server --config config/webpack.dev.js --color --progress --hot" // --color 启用彩色打印  --progerss 启用进程监测 --hot 启用热加载
  },
  "author": "张啸",
  "license": "ISC",
  "dependencies": {
    "babel-loader": "^8.0.5",
    "html-webpack-plugin": "^3.2.0",
    "webpack": "^4.29.6",
    "webpack-cli": "^3.2.3",
    "webpack-dev-server": "^3.2.1"
  }
}

复制代码如上我们有添加了webpack-dev-server 命令，下面我们启动项目查看结果：
npm run dev
复制代码
打开页面http://localhost:8080/查看结果如图

以上是一个简单webpack全过程运用的例子了，下面我给大家推荐一些webpack常用的一些loader、plugins的使用。
六、webpack项目中常用loader，以及配置。
6.1 JavaScript方面的所运用的loader

"babel-core" //JavaScript编译核心babel-core（必须）
"babel-loader" //JavaScript编译babel-loader（必须）
"babel-plugin-transform-runtime" //JavaScript 运行环境转换器
"babel-preset-stage-0" //ES6,ES7语法转换ES5编译器（ES7的提案，且包含了stage-2，stage-1所有功能）
"babel-polyfill" //IE低版本扩展JS某些API（如promise  object.defindproperty）（根据项目需求是否兼容IE低版本安装）
"babel-runtime" //提供将ES6转变成ES5运行环境,并不污染全局完成代码填充
"babel-plugin-transform-decorators-legacy" //JavaScript 装饰器语法编译器（使用@语法需添加）
"babel-plugin-transform-react-jsx" //react jsx语法转换器
"babel-plugin-react-html-attrs" //react dom属性识别语法转换器
"babel-preset-react" //react语法编译器
以上都是babel loader 相关的loader包，当然项目使用babel-loader前，还需要配置一个babel配置，通知babel-loader在使用时对应的语法文件采用何种方式去处理。该配置文件名称为 .babelrc;
babelrc配置如下：

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
复制代码在 webpack.dev.js -> module.rules 选项添加loader规则； 添加配置如下：
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
复制代码babelrc配置详情可见官网
6.2 CSS方面的所运用的loader

"style-loader"              //样式loader（必须）
"css-loader"                //css-loader （必须）
"autoprefixer"              //css3自动补全功能
"postcss-cssnext"           //利用cssnext 额外增加的一些 css 规范,
"postcss-loader"
"less":                     //以下都是使用less相关语法加载库
"less-loader"
"node-sass": "^4.5.3",      //以下都是使用sass，scss相关语法加载库
"sass-loader": "^6.0.6",

同样用到postcss-loader的话依然需要有配置告诉loader以何种方式编译css。配置文件postcss.config.js如下：
module.exports = {
    plugins: {
        'postcss-cssnext': {},
    }
};
复制代码postcss配置详情可见官网
在 webpack.dev.js -> module.rules 选项添加loader规则； 添加配置如下：
module: {
    rules: [{
			test: /\.css$/,
			use: [{
					loader: 'style-loader',
					options: { sourceMap: false }
				}, {
					loader: 'css-loader',
					options: { sourceMap: false}
				}, 'postcss-loader',
			]
	},{
      test: /\.less$/,
      use: [
        {loader: 'style-loader', options: {sourceMap: false}},
        {loader: 'css-loader', options: {sourceMap: false}},
        {loader: 'postcss-loader', options: {sourceMap: false}},
        {loader: 'less-loader', options: {sourceMap: false}}
      ]
    }, {
      test: /\.(scss|sass)$/,
      use: [
        {loader: 'style-loader', options: {sourceMap: false}},
        {loader: 'css-loader', options: {sourceMap: false}},
        {loader: 'postcss-loader', options: {sourceMap: false}},
        {loader: 'sass-loader', options: {sourceMap: false}}
      ]
    }]
}
复制代码6.3 资源文件加载的所运用的loader

"file-loader"   文件处理loader
"url-loader"    url链接处理loader

在 webpack.dev.js -> module.rules 选项添加loader规则； 添加配置如下：
module: {
    rules: [{
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
    }, {
      test: /\.(woff|woff2|svg|eot|ttf|otf)$/,
      exclude: path.resolve(__dirname, 'node_modules/'),
      use: [{
        loader: 'file-loader',
        options: {
          limit: 10000,
          name: 'fonts/[name].[ext]'
        }
      }]
    }]
}
复制代码webpack的loader种类和用途十分繁多，这里列举了几个常用loader，有兴趣的同学可以去官网查看，本文只适合webpack入门新手查阅

Webpack基本使用（详解）
在开始之前，我们先来看以下Webpack官网首页的图片，并思考一下官网图片传达给我们的含义​


一.Webpack基本介绍
1.1 概念的引入
思考：在网页中，我们经常会引入哪些常见的静态资源

JS
.js .jsx .coffee .ts（TypeScript 类 C# 语言）
CSS
.css .less .sass .scss
Images
.jpg .png .gif .bmp .svg
字体文件（Fonts）
.svg .ttf .eot .woff .woff2
模板文件
.ejs .jade .vue【这是在webpack中定义组件的方式，推荐这么用】
问题：网页中静态资源多了以后存在的问题

网页加载速度变慢，因为要多次重复的发送资源请求
要处理错综复杂的依赖关系
如何解决上述两个问题？

合并、压缩、精灵图、图片的Base64编码
可以使用之前学过的requireJS、也可以使用webpack可以解决各个包之间的复杂依赖关系；
1.2 什么是Webpack
Webpack是一个前端的项目构建工具，它是基于node.js开发出来的一个前端工具

如何实现上述的2种解决方案？

使用Gulp， 是基于 task 任务的；
使用Webpack， 是基于整个项目进行构建的；
借助于webpack这个前端自动化构建工具，可以完美实现资源的合并、打包、压缩、混淆等诸多功能。
根据官网的图片介绍webpack打包的过程
webpack官网
1.3 Webpack支持的规范
Webpack支持以下规范

CommonJS规范
//moduleA.js 导出
module.exports = function(){
    //...
}

//moduleB.js 导入
var moduleA = require('./moduleA')
复制代码
AMD规范（推崇依赖前置）
//moduleA.js 导入和导出
define(['jquery','./math.js'],function($,math){
    //AMD是依赖前置，将文件的依赖通过数组的形式导入，然后当作函数的参数传递进函数使用
    
    //通过return来实现对外接口
    return helloWorld
})
复制代码
CMD规范（推崇就近依赖，需要用到的时候再去加载模块）

标准语法：define（id？，deps?，factory）

一个文件一个模块，所以经常用文件名作为模块id
CMD推崇依赖就近，所以一般不在define的参数中写依赖，在factory中写
factory是一个函数，该函数拥有三个参数 function（require，exports，module）
require：一个方法，接收模块标识，用来获取其它模块提供的接口
exports：一个对象，用来向外提供模块接口
module：一个对象，存储了与当前模块相关联的一些属性和方法
define(fcuntion(require,exports,module){
       var $ = require('jquery.js')
       })
复制代码
ES6规范
在ES6规范中，使用import和exports命令来导入和导出文件
//moduleA.js 导出

//...内容区

//导出函数(还可以导出对象以及任何你想导出的数据类型)
exports.func = someFunc

//moduleB.js 导入
import func from './moduleA'
复制代码
掘金：AMD和CMD的区别

1.4 安装Webpack
运行 npm i webpack -g 全局安装Webpack，这样就能在全局使用Webpack命令
注意，如果是 webpack4.0以上的版本，需要全局安装 Webpack-cli
在项目根目录运行 npm i webpack --save-dev安装到项目依赖中
1.5 命令行的使用
查看Webpack版本信息
npm info webpack
复制代码
安装指定版本的Webpack
npm install webpack@版本号
复制代码
卸载webpack
npm uninstall webpack webpack-cli -g
复制代码
二.Webpack基本使用
2.1 Webpack基本的使用方式-实例
目标：使用Webpack打包构建列表隔行变色案例

创建基本的目录结构

webpack-study

dist
src
js
css
images
index.html
main.js
在项目目录下面运行npm init初始化项目

使用 npm i jquery --save 安装jquery类库

创建main.js并书写各行变色的代码逻辑

main.js

//导入jquery内库
import $ from 'jquery'

$(function(){
    $("li:odd").css('backgroundColor','red')
    $("li:even").css('backgroundColor','tomato')
})
复制代码
在页面直接引用main.js会报错，因为浏览器不认识ES6的新语法import，需要使用Webpack进行处理，Webpack默认会把这种高级语法转换为低级浏览器能够识别的语法

运行 webpack 入口文件路径 输出文件路径 对main.js进行处理

webpack ./src/main.js ./dist/bundle.js
复制代码
在index.html中引入bundle.js代替main.js

2.2 Webpack基本配置
问题描述：

每次修改文件之后，都要使用 webpack 入口文件路径 输出文件路径来对文件进行处理，使用起来比较繁琐
期待实现：

直接在控制台输入 webpack 命令，即可自动完成文件的处理
实现方法

在项目根目录添加Webpack的配置文件 webpack.config.js
在Webpack配置文件里面配置处理的入口文件和输出文件
配置完成之后，即可在调试台通过 webpack命令来对文件进行处理
const path = require('path')


module.exports = {
    entry:path.join(__dirname,'./src/main.js'),

    output:{
        path:path.join(__dirname,'./dist'),
        filename:'bundle.js'
    }
}
复制代码
三.webpack-dev-server的使用
3.1 实现自动打包编译
问题描述：每次改完代码，都需要手动执行webpack命令打包编译文件，比较繁琐

目标：每次改完代码，我们点击保存之后就可以帮我们自动打包编译

安装 webpack-dev-server

-D 将该插件的依赖写入开发依赖中
npm install webpack-dev-server -D
复制代码
在 package.json里面的 scripts属性里面添加 webpack-dev-server命令到开发环境

"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server"
  },
复制代码
在本地安装webpack，webpack-dev-server想要在本地项目中运行，必须在项目中也安装 webpack

npm install webpack -D
复制代码
执行 npm run dev命令即可,会有如下返回


由第四步可知，webpack-dev-server帮我们生成的bundle.js运行于项目根目录，这个文件并没有存到物理磁盘上，而是托管到了电脑内存中，所以我们在项目中根本看不到这个bundle.js文件，但是我们可以通过将index.html中的bundle.js引用路径修改为项目根路径，即可引用到该文件

<script src="/bundle.js"></script>
复制代码
可以认为 webpack-dev-server 把打包好的文件，以一种虚拟的形式，托管到了项目的根目录中，虽然我们看不到它，但是可以认为和 dist，src，nodemodule，平级，有一个看不见的文件，叫做bundle.js

注意：

若安装过程中有中断，需要把 node-modules文件夹删掉，重新执行 npm install下载依赖，否则会报错
最后执行npm run dev 之后，就会开始自动监听我们的修改，每次修改保存都会触发自动打包编译
3.2 额外的参数
webpack-dev-server 除了帮我们实现自动编译打包的功能之外，还可以添加 额外的参数帮我们实现更强大的功能

方式1（推荐）：
项目启动后自动打开浏览器：--open
指定项目端口号：--port 3000
指定项目启动后的主页面：--contentBase src
指定热加载：--hot
不加热加载之前，每次修改都会生成一个新的bundle.js
加了热加载之后，每次都会在原基础上更新bundle.js，提升 效率
//package.json

"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server --open --port 3000 --contentBase src --hot"
  },
复制代码
方式2（了解即可）：
//webpack.config.js

const webpack = require('webpack')	//引入Webpack，启用热更新的第2步

devServer:{     //设置dev-server命令参数的第二种形式，相对麻烦一些
        open:true,
        port:3000,
        contentBase:'src',
        hot:true    // 启用热更新的第一步
    },
    plugins:[       //配置插件的节点，热更新的第2步
        new webpack.HotModuleReplacementPlugin()    //new 一个热更新的模块对象，这是启用热更新的第3步
    ]
复制代码
3.3 html-webpack-plugin插件
插件作用：

在内存中，生成HTML页面的插件
将打包好的bundle.js插入页面中去，无需我们手动引入bundle.js
插件用法：

安装 html-webpack-plugin插件

npm i html-webpack-plugin -D
复制代码
在webpack.config.js文件中添加插件

配置模版页面，即根据该模版页面生成内存中的页面
指定生成页面的名称
//webpack.config.js

const htmlWebpackPlugin = require('html-webpack-plugin')

plugins:[ 
        new htmlWebpackPlugin({     //创建一个在内存中生成html页面的插件
            template:path.join(__dirname,'./src/index.html'),//指定模版页面，将来会根据指定的页面路径，去生成内存中的页面
            filename:'index.html'
        })
    ]
复制代码
实际展示:


拓展：-S，-D，-g说明
npm install module_name -S 即 npm install module_name –save 写入dependencies

npm install module_name -D 即 npm install module_name –save-dev 写入devDependencies

npm install module_name -g 全局安装(命令行使用)

npm install module_name 本地安装(将安装包放在 ./node_modules 下)

dependencies与devDependencies有什么区别呢？

devDependencies 里面的插件只用于开发环境，不用于生产环境
dependencies 是需要发布到生产环境的
四.loader的使用
注意：Webpack默认只能打包处理Js类型的文件，无法处理其它的非Js类型的文件

如果要处理非Js类型的文件，我们需要手动安装一些合适的第三方loader加载器

4.1 loader处理样式表
打包处理CSS文件：安装 style-loader css-loader

npm i style-loader css-loader -D
复制代码
在webpack.config.js配置文件里面新增module节点对象，在这个module对象身上，有个rules属性数组，这个数组中，存放了所有的第三方文件爱你的匹配和处理规则

module:{        //这个节点，用来配置所有第三方模块加载器
        rules:[     //配置第三方模块的匹配规则
            {test:/\.css$/,use:['style-loader','css-loader']}   //配置处理 .css文件的第三方loader规则
        ]
    }
复制代码
在main.js入口文件里面引入CSS文件即可成功使用

import './css/index.css'
复制代码
4.2 loader处理URL地址
问题描述：

默认情况下，Webpack无法处理CSS文件中的URL地址，无论是图片还是字体库，只要是URL，都处理不了
解决方法：

通过安装 url-loader file-loader插件来对URL进行处理

npm i url-loader file-loader -D
复制代码
使用步骤

安装 url-loader file-loader插件

在webpack.config.js文件里面配置URL的处理规则

参数介绍
limit：图片的大小，单位是byte，如果引用的图片大于给定的limit值，则不会被转为base64格式的字符串， 如果 图片小于给定的 limit 值，则会被转为 base64的字符串
name：设置URL指定的路径名，默认会以hash值来命名（防止重名），可以通过如下方式通过hash值拼图片原始名的方式来达到同样的效果，且辨识度更高
//URL图片路径的匹配规则
{test:/\.(jpg|jepg|png|gif)$/,use:'url-loader?limit=1000&name=[hash:8]-[name].[ext]'},
//字体图标的匹配规则
{test:/\.(eot|svg|woff|woff2|ttf)$/,use:'url-loader'}
复制代码
4.3 拓展：Webpack处理第三方文件类型的过程
先校验文件类型，如果是js文件直接打包
如果非js文件，拿到后缀名，去webpack.config.js里面找对应匹配规则
找到则调用规则打包，否则报错
rules的use规则数组从右到左调用，会将后面调用完毕的处理结果交给前面的规则继续处理
调用完毕之后会将处理结果直接交给Webpack进行打包合并，最终输出到bundle.js中去
示例：

先使用css-loader插件进行处理，将处理结果交给style-loader继续处理
 module: { 
    rules: [ 
      { test: /\.css$/, use: ['style-loader', 'css-loader'] }
    ]
  }
复制代码
4.4 总结
由于Webpack默认只能打包处理Js类型的文件，无法处理其它的非Js类型的文件，所以如果要处理非Js类型的文件，我们需要手动安装一些合适的第三方loader加载器。

安装并使用第三方加载器的方法为以下几步

在命令行安装相对应的loader加载器（注意一般的loader加载器都有相对应的依赖模块，依赖模块同样也需要安装，否则程序会运行报错）
安装完对应的loader加载器之后，在Webpack.config.js配置文件中配置相对应的匹配规则
//添加module对象
//在module对象里面添加rules数组
//在rules数组里面添加相对应文件的匹配规则

module:{
    rules:[
        {test:/\.css$/,use:['style-loader','css-loader']}
    ]
}
复制代码
如果有依赖的CSS或者其它文件，都以import命令的方式在入口js文件里面引入
完成以上步骤即可成功启用loader加载器

五.Babel的使用
在Webpack中，默认只能处理一部分ES6语法，一些更高级的ES6或者ES7语法，Webpack是处理不了的，这时候，就需要借助第三方loader，来帮助Webpack处理这些高级的语法，当第三方loader把高级语法转为低级的语法之后，会把结果交给Webpack去打包到bundle.js中

通过Babel，可以帮助我们将高级语法转换为低级的语法

5.1 安装
在Webpack中，可以运行如下两套命令，安装两套包，去安装Babel相关的loader功能
第一套包（类似于转换器）：cnpm i babel-core babel-loader babel-plugin-transform-runtime -D
第二套包（提供转换关系）：cnpm i bebel-preset-env babel-preset-stage-0 -D
5.2 配置
在Webpack.config.js配置文件中，在module节点下的rules数组中添加一个新的匹配规则

注意：

在配置的babel的loader规则的时候，一定要把node_modules目录通过exclude属性排除掉
如果不排除node_modules,则Babel会把node_modules中所有的第三方Js文件都打包编译，这些会消耗CPU，并且导致打包速度非常慢
哪怕最终babel把node_modules中的js全部转换完了，但是，项目也无法运行
{test:/\.js$/,use:'babel-loader',exclude:/node_modules/}
复制代码
在项目的根目录中，新建一个叫做 .babelrc 的Babel配置文件

这个配置文件属于json格式，必须复核JSON语法规范，不能写注释，字符串必须使用双引号
该配置文件可参照安装的两套包中的插件
第一套包里面有个 babel-plugin-transfrom
第二套包里面有 babel-preset-env，babel-preset-stage-0
{
    "presets":["env","stage-0"],
    "plugins":["transform-runtime"]
}
复制代码
完成以上配置，即可在项目中使用ES6语法，可以编写一个Class类，设置静态属性并打印它们来测试