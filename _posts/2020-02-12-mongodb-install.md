---
layout: post
title: Ubuntu 18.10安装MongoDB的一些笔记
categories: [Ubuntu, MongoDB, Notes]
description: 在Ubuntu 18.10上安装MongoDB的教程资源不算很多，大部分网络教程或者指导都是针对Ubuntu 18.04的。虽然在这两个系统上安装MongoDB的流程基本一致，但是还是有一些细微差别，并且在安装过程中可能会出现一些问题。特此，写下相关教程，帮助自己记忆的同时也为别人提供方便。
keywords: Ubuntu, MongoDB, Notes
---

这段时间在学习后端开发，以``Node.js``作为切入点。在学习过程中涉及到了服务端数据持久化，于是需要安装``MongoDB``。在Ubuntu 18.10上安装``MongoDB``的流程比较简单，但是在安装过程中可能会出现一些问题。特此，写下相关教程，帮助自己记忆的同时也为别人提供方便。

## 安装流程

- 首先需要在``MongoDB``官网上下载``.tgz``的压缩文件用于安装。有以下两种方式可以从官网上下载：
    - 可以直接在``MongoDB``官网上选择``Community Server``，然后文件形式选择``.tgz``，版本选择``Ubuntu 18.04``（由于没有Ubuntu 18.10的版本）

    - 也可以通过Linux命令行下载：

        ```shell
        curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-4.2.3.tgz 
        ```

- 然后将下载好的``.tgz``资源文件解压：

    ```shell
    tar -zxvf mongodb-linux-x86_64-4.2.3.tgz 
    ```

- 将解压好的文件夹移动到指定的存放位置，这边的指定是用户指定，即可以放到自己想放到的任何位置：

    ```shell
    mv mongodb-linux-x86_64-4.2.3 /usr/local/mongodb
    ```

- 最后需要添加相应的环境变量。保险起见，最好的选择是同时添加全局环境变量和用户环境变量。分别是：

    - 全局环境变量在``/etc/profile``中：

        ```bash
        export MONGODB_HOME=/usr/local/mongodb # 此处是前面存放MongoDB相关文件的位置
        export PATH=${MONGODB_HOME}/bin:$PATH
        ```

    - 用户环境变量在``~/.profile``中，添加方式同上。

    - 最后分别激活环境变量：

        ```shell
        source /etc/profile
        source ~/.profile
        ```

- 安装完毕后可以进行相关测试。这里需要提醒一下：如果根目录下不存在``/data/db``文件夹，则使用``mongod``的相关命令都会报错，因为``MongoDB``默认将数据文件放在``data/db``文件夹下。  
本人不建议放在根目录下，这主要涉及到了权限问题。可以通过以下命令，在启动``MongoDB``服务的时候，临时修改默认的数据存储位置，避免混乱的权限问题。

    ```shell
    mongod --dbpath ~/data/mongodb 
    ```

    执行上述命令后，``MongoDB``的服务就开始运行了，可以通过``mongo``命令连接到数据库。  

    除此之外，可以通过使用：

    ```shell
    mongod --version
    ```

    直接查看版本来确认是否安装成功。

## 相关问题

- 使用``mongod``等相关命令的时候出现以下错误：

    > ./mongod: error while loading shared libraries: libcurl.so.4: cannot open shared object file: No such file or directory.

    直接安装相关库即可：

    ```shell
    sudo apt-get install libcurl4-openssl-dev
    ```

- 使用``mongo``等相关命令时出错：

    > ./mongod: error while loading shared libraries: libnetsnmpmibs.so.30: cannot open shared object file: No such file or directory

    同样直接安装相关库即可：

    ```shell
    sudo apt-get install snmpd snmp snmp-mibs-downloader
    ```