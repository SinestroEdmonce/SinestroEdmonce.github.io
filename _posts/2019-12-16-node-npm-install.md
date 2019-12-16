---
layout: post
title: Ubuntu18.04安装NodeJs及npm相关包
categories: [Ubuntu, NodeJs]
description: 在Ubuntu18.04上直接通过apt-get等相关原生指令安装npm和NodeJs的简明教程
keywords: Ubuntu, NodeJs, npm, install
---

1. 更新源仓库的packages:  
    ```bash
    sudo apt-get update
    ```

2. 通过``apt-get``安装npm和nodejs:  
    ```bash
    sudo apt-get install nodejs
    sudo apt-get install npm
    ```

3. 通过``n``指令安装node的稳定版本(或最新版本):
    - 安装``n``指令:
        ```bash
        sudo npm install -g n
        ```
    - 安装稳定版本:
        ```bash
        sudo n stable
        ```
    - 安装最新版本:
        ```bash
        sudo n latest
        ```

4. 更新npm版本至最新:
    ```bash
    sudo npm install npm@latest -g
    ```

5. 查看nodejs和npm版本:
    ```bash
    sudo node -v
    sudo npm -v
    ```

6. 安装npm源管理工具nrm:
    ```bash
    sudo npm install -g nrm
    ```

7. 查看npm源:
    ```bash
    nrm ls
    ```
    ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/npm_source.png)

8. 切换npm源:
    ```bash
    nrm use xxx     # xxx表示源的名称
    ```
