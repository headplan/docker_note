# 给容器配置网络

#### 容器网络

容器网络实质上也是由Docker为应用程序所创造的虚拟环境的一部分 , 它能让应用从宿主机操作系统的网络环境中独立出来 , 形成容器自有的网络设备、IP 协议栈、端口套接字、IP 路由表、防火墙等等与网络相关的模块 .

![](/assets/docker-network.png)

Docker网络三个比较核心的概念 :

* 沙盒\(Sandbox\) - 提供了容器的虚拟网络栈 , 也就是之前所提到的端口套接字、IP 路由表、防火墙等的内容 . 其实现隔离了容器网络与宿主机网络 , 形成了完全独立的容器网络环境 . 
* 网络\(Network\) - 可以理解为 Docker 内部的虚拟子网 , 网络内的参与者相互可见并能够进行通讯 . Docker 的这种虚拟网络也是于宿主机网络存在隔离关系的 , 其目的主要是形成容器间的安全通讯环境 . 
* 端点\(Endpoint\) - 位于容器或网络隔离墙之上的洞 , 其主要目的是形成一个可以控制的突破封闭的网络环境的出入口 . 当容器的端点与网络的端点形成配对后，就如同在这两者之间搭建了桥梁，便能够进行数据传输了。

三者形成了Docker网络的核心模型 , 也就是容器网络模型 \(Container Network Model\) .

#### Docker的网络实现

Docker封装了libnetwork模块 , 实现了容器网络标准对接范式 . 网络实现经过发展后的高度抽象 , 已经可以通过抽象定义实现不同的变化 .

目前官方提供了五种Docker网络驱动 :

* **Bridge Driver**
* **Host Driver**
* **Overlay Driver**
* **MacLan Driver**
* **None Driver**

这里使用频率较高的是

* Bridge Driver , 默认的网络驱动 , 网桥来实现的网络通讯
* Overlay Driver借助Docker集群模块Docker Swarm来搭建的跨Docker Daemon网络 . 可以通过它搭建跨物理主机的虚拟网络 , 进而让不同物理机中运行的容器感知不到多个物理机的存在 . 

#### 容器互联

互联网时代应用之间的通信都以网络为主 , 容器连接到另外一个容器使用`--link`参数选项进行配置 .

例如 , 创建一个MySQL容器 , 将运行Web应用的容器连接到这个MySQL容器上 , 打通两个容器间的网络 , 实现它们之间的网络互通 .

```
docker run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
docker run -d --name webapp --link mysql webapp:latest
```

容器间的网络已经打通 , 在 Web 应用中连接到 MySQL 数据库 , 只要将容器的网络命名填入到连接地址中 , 就可以访问需要连接的容器了 . 也就是容器的别名 , 映射IP的工作都由Docker完成 .

例如 , 使用JDBC连接数据库

```
String url = "jbdc:mysql://mysql:3306/webapp";
```

#### 暴露端口

容器网络打通了 , 但是只有容器自身允许的端口 , 才能被其他容器所访问 .

容器自我标记端口可被访问的过程 , 就叫暴露端口 . `docker ps`中查看到的PORTS就是暴露的端口 .

端口的暴露可以通过 Docker 镜像进行定义 , 也可以在容器创建时进行定义 . 创建时使用`--expose`选项即可 : 

```
docker run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes --expose 13306 --expose 23306 mysql:5.7
```



