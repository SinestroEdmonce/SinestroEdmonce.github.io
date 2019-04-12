---
layout: post
title: Linux服务器后台运行程序，不响应断开连接
categories: [Linux, Ubuntu]
description: 远程连接Linux服务器后台运行程序，断开连接后程序继续运行等相关需求的简明教程
keywords: Linux, Ubuntu, screen, nohup, servers
---

近段时间在学校的服务器上跑程序，远程连接到Linux终端后，如果发生意外或者人为断开了远程连接，那么正在运行的程序或命令(例如训练深度神经网络这种比较耗时的操作)就会中断。原因是因为断开连接后,终端会收到``SIGHUP``(hang-up挂断)信号从而关闭该会话期的子进程。所以希望能后台运行程序，并且断开连接后程序依旧运行。 

> 从网上搜集资料后有如下两种解决方法。

## **解决方法**

1. **nohup命令**  
``nohup``命令的功能就是忽略``SIGHUP``信号，不响应挂断信号，不挂断进程，进程仍然属于当前会话(终端关闭后，进程会变成孤儿进程)，使用``nohup``命令执行程序后，会返还一个当前进程的PID，如果是在GPU上运行的程序可以通过``nvidia-smi``命令查看到当前运行的所有程序的PID。在结束``ssh``会话后，该PID程序将会成为孤儿进程，但是重新登录会话后在``nvidia-smi``显示的GPU上的所有进程里仍能找到上述的PID，也就是说断开会话程序依旧保持运行，知道结束或者遇错终止。命令使用方法简述如下：  

    ```shell
    nohup [COMMANDS] > [OUTPUT FILE NAME] 2>&1 &
    ```

    例如如下命令：

    ```shell
    nohup python2 train.py > [train_log.txt] 2>&1 &
    ```

    该命令就会返回一个进程的PID，并且所有输出将会保存在``train_log.txt``文件中。断开会话后该进程将继续运行。

    > 如果末尾不加``&``，则父进程(也就是当前shell终端)将会被阻塞直到程序运行结束。

2. **screen命令**  

如果程序需要交互等复杂的操作，可以使用功能强大的``screen``。``screen``提供了``ANSI/VT100``的终端模拟器，使它能够在一个真实终端下运行多个全屏的伪终端。不是所有Linux都自带``screen``(比如我使用的Ubuntu终端需要自行安装)。

> 查询安装包信息: ``rpm -qa|grep screen``

- **安装方法：**  
    - CentOS系统: ``yum install screen``

    - Debian/Ubuntu系统: ``apt-get install screen``

- **用法：**  

    - 打开新的会话窗口: ``screen``
    - 结束当前会话: ``exit``
    - 在新会话中执行程序(程序关闭时会话自动结束): ``screen [COMMAND]``
        > ``screen vim test.py``
    - 打开新会话并起个名字: ``screen -S [SESSION_NAME]``
        > ``screen -S test_session``
    - 暂时离开会话(经常用): ``Ctrl+a+d``(按键需要按给定顺序)
    - 查看会话列表: ``screen -ls``
    - 恢复之前离开的会话: ``screen -r [SESSION_NAME]/[PID]``
    - 启动一个开始就是Detached状态的会话: ``screen -dmS [SESSION_NAME] [COMMAND]``

    | 常用快捷键(按键按给定顺序) | 命令功能 |
    | ------------- | ------------- |
    | Ctrl+a+? | 显示所有键绑定信息 |
    | Ctrl+a+d | 暂时离开会话返回之前的shell(会话进入Detached状态) |
    | Ctrl+a+w | 显示所有窗口列表(不包括Detached状态的) |
    | Ctrl+a Ctrl+a | 切换到之前显示的窗口(不切换Detached状态的) |
    | Ctrl+a+c | 创建一个新的运行shell的窗口并切换到该窗口 |
    | Ctrl+a+n | 切换到下一个窗口 |
    | Ctrl+a+p | 切换到上一个窗口 |
    | Ctrl+a+k | 杀掉当前窗口(会提示确认) |
    | Ctrl+a+[0..9] | 切换到窗口0..9 |

- **注意事项：**

如果运行需要环境变量的某些特定程序(如在GPU上需要CUDA的神经网络训练程序)，在使用``screen``命令创建的新会话窗口直接运行特定程序，将会报错(找不到相应的库)，例如：

```shell
ImportError: libcusolver.so.9.0: cannot open shared object file: No such file or directory
```

这种情况下，只需要在新建的会话中输入``export [ENV_PATH]``即可解决：

```shell
export PATH=/usr/local/cuda-9.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH
```
