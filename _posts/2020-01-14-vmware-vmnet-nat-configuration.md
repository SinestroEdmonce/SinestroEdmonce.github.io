---
layout: post
title: VMware Player在Windows系统下使用NAT模式的端口映射
categories: [VMware]
description: VMware Player不同于其专业版平台，它不存在网络设置工具相关的功能，所以如果想要使用专业版平台一样的NAT模式端口映射，需要自行修改配置文件。
keywords: VMware, Windows, NAT, Forward
---

``VMware Player``在功能完整性不如其专业版本``VMware Workstation Pro``，在``VMware Player``中不存在网络设置的相关工具和其他一些常用功能。但是，``VMware Player``是免费版本，并且具有核心的虚拟机安装和播放的功能。于是，因为穷（误……），我毅然决然地选择使用``VMware Player``。不过，由于需要在虚拟机中搭建Web服务器，如果不做NAT模式的端口映射，宿主机和同一局域网下的其他PC是无法直接访问虚拟机中的Web服务的，所以在``VMware Player``下使用NAT模式的端口映射还是一件必须的事情。

下面是修改配置文件，使``VMware Player``能够进行NAT模式的端口映射的流程:  

1. 设置虚拟机NAT模式共享的网卡为固定IP:  

    VMware虚拟机使用NAT模式时，是``VMware Network Adapter VMnet8``网卡提供的nat服务。查看``VMware Network Adapter VMnet8``网卡的网址，比如我的是: 
    ```
    IP: 192.168.150.128

    MASK: 255.255.255.0
    ```
    
    此时可以在虚拟机中将NAT的那个网卡设置固定ip地址，比如我是``Ubuntu 18.04``的虚拟机系统:  

    - 停止``Network-manager``服务:  
        ```bash
        sudo service network-manager stop
        ```
    - 设置IP地址、子网掩码和网关:  
        ```bash
        sudo ifconfig eth0 192.168.144.128 netmask 255.255.255.0 # IP可以按规则任意设置

        sudo route add default gw 192.168.144.1 # 网关一般是.1
        ```
    
    - 设置DNS，修改``/etc/resolv.conf``:  
        ```bash
        sudo su
        echo "nameserver 192.168.199.1" >> /etc/resolv.conf # 希望修改成的DNS
        ```

    - 重启网络服务:  
        ```bash
        sudo /etc/init.d/networking restart # 使网卡配置生效
        sudo /etc/init.d/resolvconf restart # 使DNS生效
        ```

    不过，需要注意的是，上面的设置一般情况下在重启虚拟机后就会失效。  

    第二种方法具有持久性，需要直接修改系统的配置文件``/etc/network/interfaces``
    
    - 修改IP地址:  
        - 打开``/etc/network/interfaces``:  
            ```bash
            sudo vim /etc/network/interfaces
            ```

        - 加入以下语句:  
            ```bash
            auto eth0 # 要设置的网卡
            iface eth0 inet static # 设置静态IP；如果是使用自动IP用dhcp，后面的不用设置，一般少用
            address xxx.xxx.xxx.xxx # IP地址
            netmask xxx.xxx.xxx.xxx # 子网掩码
            gateway xxx.xxx.xxx.xxx # 网关
            ```

    - 修改DNS:  
        - 打开``/etc/resolv.conf``:  

            ```bash
            sudo vim /etc/resolv.conf
            ```

            改为如下内容:  

            ```bash
            search localdomain # 如果本Server为DNS服务器，可以加上这一句
            nameserver 172.16.3.4 # 希望修改成的DNS
            nameserver 172.16.3.3 # 希望修改成的DNS
            ```

            上面设置的文件重启后会覆盖，如果要持久的保存，请修改``etc/resolvconf/resolv.conf.d/base``。

    - 重启服务生效:  

        先运行一次，然后在``rc.local``里加入这个重启网络配置的命令：
        ```bash
        sudo /etc/init.d/networking restart #使网卡配置生效
        sudo /etc/init.d/resolvconf restart #使DNS生效
        ```
    
    一般情况下，使用第一种方法即可。持久化的不是必须的，在同一网络下，虚拟机的IP地址等属性一般都有默认的值，直接使用默认值也是可以的。

2. 给虚拟机NAT网卡开启端口映射:  

    在``Windows XP``操作系统中，找到``VMware NAT``的配置文件在:  

    ```
    C:\Documents and Settings\All Users\Application Data\VMware\vmnetnat.conf
    ```

    在``Windows 7/10``系统中，``VMware NAT``的配置文件在:  

    ```
    C:\ProgramData\VMware\vmnetnat.conf
    ```

    如果想使用Web服务，则修改对应的配置选项，如:  
    ```
    8888 = 192.168.20.56:80
    ```

    其意思是将宿主机的8888端口映射到虚拟机的80端口,这样其它的机器只要访问这台宿主机的8888端口,就可以访问到其虚拟机的80端口了。

    最后需要在主机上重新启动一下``VMware NAT Service``服务，然后就可以正常访问了。

    该服务的查看方法可以通过在``cmd``中执行:  
    ```cmd
    sc query|find /i "vmware"
    ```

    执行后发现有如下服务名:  
    ```
    DISPLAY_NAME: VMware Authorization Service

    DISPLAY_NAME: VMware DHCP Service

    DISPLAY_NAME: VMware USB Arbitration Service

    SERVICE_NAME: VMware NAT Service

    DISPLAY_NAME: VMware NAT Service
    ```

    在``cmd``中使用如下命令可以重启服务:  
    ```
    net stop "VMware NAT Service"
    net start "VMware NAT Service"
    ```

    如果访问不了，请查看Windows防火墙是否阻止了这些端口。