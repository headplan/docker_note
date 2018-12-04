# 从镜像仓库获得镜像

#### 镜像仓库

镜像仓库可以看做是一个类似GitHub一样的托管平台 , 只是托管的不是代码 , 而是镜像 . 主要是为了Docker镜像的分发 , 镜像仓库是一个镜像中转站 . 可以将开发环境上所使用的镜像推送至镜像仓库 , 并在测试或生产环境上拉取 .

![](/assets/pushpull.png)

#### 获取镜像

```
docker pull [选项] [Docker Registry地址]<仓库名>:<标签>
```

```
# 例如,没写Registry地址就从官方下载
docker pull ubuntu
docker pull openresty/openresty:1.13.6.2-alpine
```

* Docker Registry地址 : 地址的格式一般是`<域名/IP>[:端口号]`.默认地址是 Docker Hub

* 仓库名 : 如之前所说,这里的仓库名是两段式名称,既`<用户名>/<软件名>`.对于Docker Hub,如果不给出用户名,则默认为`library`,也就是官方镜像

下载过程可以看出前面提及的分层存储概念 , 镜像是由多层存储所构成的 . 下载过程中给出了每一层的ID前12位 . 下载结束后给出完整的sha256摘要 . 镜像在被拉取之后 , 就存放在了本地 , 接受当前这个Docker实例管理了 , 可以通过docker images查看 .

#### Docker Hub

Docker Hub 是 Docker 官方建立的中央镜像仓库 , 除了普通镜像仓库的功能外 , 它内部还有更加细致的权限管理 , 支持构建钩子和自动构建 , 并且有一套精致的 Web 操作页面 .

Docker Hub 的地址是：[https://hub.docker.com/](https://hub.docker.com/)

Docker Hub 是 Docker Engine 的默认镜像仓库 , 所以镜像足够丰富 .

**搜索镜像**

Docker Hub提供了完整的搜索界面 , 可以直接搜索关键词 . 搜索结果分类 :

* **OFFICIAL**
  代表镜像为Docker官方提供和维护 , 相对来说稳定性和安全性较高
* **STARS**
  代表镜像的关注人数 , 这类似 GitHub 的 Stars , 可以理解为热度
* **PULLS**
  代表镜像被拉取的次数 , 基本上能够表示镜像被使用的频度

还可以使用命令行搜索镜像

```
docker search mysql
```

使用方式和搜索结果类似web页 .

#### 管理镜像

列出本地Docker中的所有镜像

```
docker images
```

获取容器/镜像元数据 , 查看更详细的信息

```
docker inspect {Tab补全}
```

传参除了镜像或容器的名称 , 还可以是镜像ID或容器ID . 

删除镜像 , 将它们从本地的Docker Engine中移除

```
docker rmi
```

参数是镜像名称或ID . 

