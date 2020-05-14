---
layout: post
title: Nodejs中package-lock.json文件的作用
categories: [Nodejs, Notes]
description: 简要介绍一下package-lock.json文件在工程中的作用。
keywords: Nodejs, package-lock.json
---

在创建node的工程的时候一般会出现``package.json``和``package-lock.json``两个文件，尤其是在安装第三方库的的时候一定会出现，这两个文件的文件名很相似，但是文件本身在功能上还是有一些区别和联系的。

1. ``package.json``的功能如下：

    - ``package.json``中保存了当前工程直接依赖的第三方外部库。
    - ``package.json``中保存的第三方依赖版本一定是大于等于当前安装的第三方外部库的版本，比如：

        ```json
        dependencies: {
            express: ^4.1.2
        }
        ```

    在这个条目中，``^``符号代表此后如果重新再安装一遍外部库，安装的版本一定大于或等于当前的版本号。

2. ``package-lock.json``的功能如下：

    - ``package-lock.json``中保存了当前工程依赖的所有第三方外部库，即比如当前工程依赖``express``，则``package-lock.json``中会直接保存``express``所需要的所有外部库信息。即将所有依赖链上的所有包的信息都直接存储下来。  
        > 这样一来，更新或者安装工程依赖库的时候会加快速度，不需要在沿着依赖链下载。

    - ``package-lock.json``可以锁住当前工程中依赖的第三方依赖库的版本。不会像``package.json``一样会自动更新第三方依赖的版本（由于有``^``的符号，而该符号在``package-lock.json``中不存在。