---
layout: post
title: MacOS及Ubuntu16.04平台远程连接服务器GUI界面
categories: [MacOS, Ubuntu]
description: 针对MacOS和Ubuntu用户远程连接服务器图形界面的简明教程
keywords: MacOS, Ubuntu, GUI, servers
---

近段时间在学校的服务器上测试几篇论文中的模型。由于毕业论文的研究重心是基于点云数据的三维物体语义分割，所以这些模型基本都应用了深度学习框架，像TensorFlow、Caffe这样的。一般应用了深度学习算法（尤其深层神经网络）的模型，如果不在GPU上跑，那训练时间几乎永无止境，于是只能将模型的代码、数据集等相关文件移植到学校的GPU服务器上。

> 在服务器上能用上GTX 1080Ti，对训练速度有蛮大提升。

说到服务器，一般来说GPU集群的服务器是没有图形界面的，因为会占用不少额外显存空间，有点浪费资源。不过，我用的这个服务器恰好有GUI界面。既然有图形界面，那我肯定得用，毕竟GUI界面确实很方便。

> 在Windows操作系统上，Mircosoft配备了``mstsc``也就是远程桌面连接，可以很方便地远程连接服务器的图形界面。
> 还可以用``Xshell+Xmanager``来连接远程桌面。

我用的是MacOS和Ubuntu16.04，需要自己去寻找方法远程连接GUI界面。  

实际上，在MacOS和Ubuntu上有多种方法可以实现我的目的：  

- 第一种方法是在本机上安装``VNC viewer``，在远端服务器上安装``VNC servers``。这个方法比较简单快捷，但是可惜我没有``root``权限，不太方便安装。

- 第二种方法则是配置``ssh``命令，通过修改本机和服务器端``ssh``命令的配置文件，可以实现远端服务器上有图形界面的软件在本机上打开图形界面。

我采用的是第二种方法。  

### **方法详述**

要实现远程图形访问，必须借助X11协议转发图片。X11将``xclient``端的图像发送回``xserver``端。在我们登录远程服务器这个流程中，``xserver``端为Mac端，``xclient``为Linux服务器端，比如我连接的服务器是Ubuntu。

![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/x11_local2server_intro.png)

1. 首先需要在Linux服务器端打开X11转发。以Ubuntu为例，则需要编辑``/etc/ssh/sshd_config``配置文件，配置X11相关的转发参数为``yes``。  

    ```
    X11Forwarding yes
    X11DisplayOffset 10
    X11UseLocalhost  yes
    ```
    
    然后重启服务器端的ssh服务。（这几步操作都需要``root``权限，不过在我使用的服务器上已经配置好了，基本上正常情况下有图形界面的服务器端都应该是配置好的）

    ```
    service ssh restart
    ```

2. 然后需要配置本机上的文件。

    - 编辑Mac端下的配置文件，在terminal下vim打开``/etc/ssh/ssh_config``或者如果只希望某Mac登录账户下可以使用``ssh``配置，则打开``~/.ssh/config``（可能是个新文件），然后配置如下：

        ```
        ForwardX11 yes
        ```

        一般来说，保险起见可以两个文件都配置。  
    
        然后需要在Mac端安装``XQuartz`` ，可以在[官网](https://www.xquartz.org)下载，也可以尝试在Mac端的terminal上使用命令：

        ```
        sudo brew cask install xquartz
        ```
    - 编辑Linux端下的配置文件（以Ubuntu为例)。在terminal下vim打开``/etc/ssh/ssh_config``或者如果只希望某登录账户下可以使用``ssh``配置，则打开``~/.ssh/config``（可能是个新文件）。具体配置和Mac一样，不再赘述。（如果配置``/etc/ssh/sshd_config``，则是指server端）

3. 在Mac端或Linux端远程连接服务器桌面。

    - 打开terminal，输入如下命令：

        ```
        ssh -X {username}@{remote-server-ip}        # X要大写
        ```

    - 登陆成功后可以尝试打开有图形界面的应用，这里我打开了``Pycharm Community 2018.3``来测试配置情况。

        ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/x11_pycharm_show_pic1.png)

        ![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/x11_pycharm_show_pic2.png)

    - **Warning：**如果Mac端登陆远程服务器后，出现以下错误描述：

        ```
        xauth key data not generated
        ```
        
        这是由于Mac安全性设置的关系，需要编辑Mac下``~/.ssh/config``，添加以下字段即可。
        
        ```
        ForwardX11Trusted yes
        ```

至此，所有配置全部完成，可以在本机查看服务器端的带图形界面的软件了。