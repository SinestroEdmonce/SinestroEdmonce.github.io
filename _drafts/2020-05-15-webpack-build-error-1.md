---
layout: post
title: template page
categories: [cate1, cate2]
description: some word here
keywords: keyword1, keyword2
---

webpack.config.js配置遇到Error: Cannot find module '@babel/core'&&Cannot find module '@babel/plugin-transform-react-jsx' 问题
一、 问题描述
在配置webpack.config.js自动打包的时候,出现Error: Cannot find module '@babel/core'错误
最初以为是babel-core没有安装上。重装了好几遍babel-core还是不行。对照以前的项目,发现babel-loader的版本不一样,之前的是@7.1.5版本,而现在是@8.0.0版本。

1、为什么会报错 ？
　　这里抱着错误是因为 babel 的版本冲突。

　　多是因为你的 babel 依赖包不兼容。

 

　　可以查看你的 package.json 的依赖列表

　　即有 babel 7.0 版本的( @babel/core ， @babel/preset-react )

　　也可命令查看 bebel-cli 的版本 （ babel -V ）

　　也有 babel 6.0 版本的 ( babel-core@6.26.0 , babel-cli@6.26.0 , babel-preset-react@6.24.1 )

　　

　　如果在你的 package.json 依赖包中既有 babel 7.0 版本，又有 babel 6.0 版本，就会报这个错误

　　很现实就是两个版本是不兼容的

 
二、 解决方法
带着半信半疑的心情安装回@7.1.5版本

npm uninstall babel-loader
npm install babel-loader@7.1.5
再npm run build发现成功了！
有点纳闷,距离上次安装不过才几天,就更新成babel-loader@8.0.0。而且还不支持原来的配置了。网上没有找到方法解决,原理也还不清楚。先mark一下,以后解决了@8.0.0的这个问题再回来补充。

附上webpack.config.js代码:

var webpack = require('webpack');
var path = require('path');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: __dirname + '/client/root/index', //入口文件
    output: {
        path: path.join(__dirname + '/dist'),
        filename: 'bundle.js',  //打包后文件名
    },

    module: {
        loaders : [{
            test :/(\.jsx|\.js)$/,
            exclude : /node_modules/,
            loader :'babel-loader',
            options:{
                presets:[
                    "env", "react", 
                ]
            }
        },
        {
            test: /\.css$/,
            loader: 'style-loader!css-loader'
        },
        {
            test: /\.less$/,
            loader: 'style-loader!css-loader!less-loader'
        },
        {
            test: /\.(jpg|.png)$/,
            loader: 'url-loader'
        }
        ]
    },

    plugins: [
        //打包引用模板
        new HtmlWebpackPlugin({
            template: __dirname + '/client/views/template.html'
        }),
    ]
}
关于babel-loader@8.0.0出现错误原因已经找到,感谢@Sky__zt的回答.
(忘了去看官方文档了)

官方默认babel-loader | babel 对应的版本需要一致: 即babel-loader需要搭配最新版本babel



具体请参考:《npm_babel-loader》

总结:
两种解决方案: 1. 回退低版本
npm install -D babel-loader@7 babel-core babel-preset-env

更新到最高版本:
npm install -D babel-loader @babel/core @babel/preset-env webpack