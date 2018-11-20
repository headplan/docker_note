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

因为Docker daemon使用 RESTful Api 作为控制方式 , 所以Docker Desktop只要实现Windows和MacOS中的客户端 , 就能够直接利用Hypervisor的网络支持与虚拟Linux系统中的docker daemon进行通讯 , 并对它进行控制 .

#### 主机文件挂载

Docker 容器中能够通过数据卷的方式挂载宿主操作系统中的文件或目录 , 这里在宿主操作系统上的Linux是虚拟的系统 . 先将目录挂载到虚拟 Linux 系统上 , 再利用 Docker 挂载到容器之中 , Docker Desktop简化了这个流程 .

![](/assets/guazai.png)

#### 配置Docker Desktop

菜单中选择 Settings \( Windows \) 或 Preferences \( macOS \) .

**文件系统挂载配置**

在 Docker for Windows 的 Shared Drivers 面板 , 以及在 Docker for Mac 中的 File Sharing 面板中 , 包含了之前提到的将本机目录挂载到 Hypervisor 里 Linux 系统中的配置 .

**资源控制配置**

在 Advanced 面板中 , 可以调整 Docker 最大占用的本机资源 . 其实是在调整虚拟 Linux 环境所能占用的资源 , 是通过这个方式影响 Docker 所能占用的最大资源 .

**网络配置**

在 Docker for Mac 中的 Advanced 面板中 , 配置 Docker 内部默认网络的子网等 .

**Docker daemon配置**

在 Daemon 面板里 , 我们可以直接配置对 docker daemon 的运行配置进行调整 . 

Basic里是图形化的Insecure registries 和 Registry mirrors 两个配置 , 分别用来配置未认证镜像仓库地址和镜像源地址 . 

Advanced中可以直接配置daemon.json文件 . 



