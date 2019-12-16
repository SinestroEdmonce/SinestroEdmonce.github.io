---
layout: post
title: Ubuntu18.04更新apt时出现lock问题的解决方法
categories: [Ubuntu, apt-get]
description: Ubuntu18.04系统执行apt-get update命令时，有时会出现lock的情况，这种情况下需要对lock文件进行处理
keywords: Ubuntu, apt-get, lock
---

1. 出现的问题:
```
E: Could not get lock /var/lib/dpkg/lock - open (11: Resource temporarily unavailable)

E: Unable to lock the administration directory (/var/lib/dpkg/), is another process using it?
```

2. 解决方法：

- 结束所有的``apt``进程:  
    - 查找所有``apt``相关的进程:
        ```bash
        ps afx|grep apt
        ```
    - 并用命令结束:
        ```bash
        sudo kill -9 xxxxx      # xxxxx是进程号
        ```

- 删除锁定文件:
    - 锁定的文件会阻止``Linux``系统中某些文件或者数据的访问，这个概念也存在于``Windows``或者其他的操作系统中。一旦运行了``apt-get``或者``apt``命令，锁定文件将会创建于``/var/lib/apt/lists/``，``/var/lib/dpkg/``，``/var/cache/apt/archives/``中，这有助于运行中的``apt-get``或者``apt``进程能够避免被其它需要使用相同文件的用户或者系统进程所打断。当该进程执行完毕后，锁定文件将会删除。

    - 移除对应目录下的锁文件:
        ```bash
        sudo rm -rf /var/lib/dpkg/lock
        ```
        或
        ```bash
        sudo rm -rf /var/lib/dpkg/lock-frontend
        ```

    - 强制重新配置软件包:
        ```bash
        sudo dpkg --configure -a
        ```

    - 更新软件包源文件:
        ```bash
        sudo apt update
        ```
    
3. 删除锁定文件后配置软件包可能出现的问题:
    - 问题描述:
        ```
        dpkg: error: parsing file '/var/lib/dpkg/updates/0006' near line 0: newline in field name `#padding'
        ```

    - 解决方法:
        ```bash
        sudo rm -rf /var/lib/dpkg/updates/0006
        sudo dpkg --configure -a
        sudo apt update
        ```
        或
        ```bash
        sudo rm -rf /var/lib/dpkg/updates/*
        sudo dpkg --configure -a
        sudo apt update
        ```

4. 最后再重新执行相关安装
    ```bash
    sudo apt-get install xxxx       # xxxx是软件包名
    ```