# Docker Desktop

Docker Desktop 实现容器化与 Docker Engine 是一致的 , 这就保证了我们在 Windows和macOS中开发所使用的环境可以很轻松的转移到其他的 Docker 实例中 , 不论这个Docker实例是运行在Windows、macOS 亦或是 Linux .

#### 安装 Docker Desktop

**Windows**

* 必须使用 Windows 10 Pro \( 专业版 \)
* 必须使用 64 bit 版本的 Windows

**MacOS**

* Mac 硬件必须为 2010 年以后的型号
* 必须使用 macOS El Capitan 10.11 及以后的版本

> VirtualBox与Docker Desktop的兼容性不太好 , 尽量别同时用 .

下载 :

* Windows - [https://store.docker.com/editions/community/docker-ce-desktop-windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* MacOS - [https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)

按照正常的方式安装即可 .

启动时图标鲸鱼上的集装箱会闪动 , 说明正在部署docker daemon所需要的一些环境并启动它 . 启动之后 , 可以在命令行中 , 试试`docker version`

#### Docker Desktop 的实现原理

就是在系统本身再虚拟化一个系统  , 在Windows中 , 通过 Hyper-V 实现虚拟化 . 在 macOS中 , 通过 HyperKit 实现虚拟化 . 在搭建的虚拟Linux系统上安装和运行docker daemon . 

![](/assets/desktop.png)

