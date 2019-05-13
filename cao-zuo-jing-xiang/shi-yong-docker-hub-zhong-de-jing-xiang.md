# 使用Docker Hub中的镜像

#### 选择镜像与程序版本

由于 Docker 的容器设计是程序即容器的 , 所以组成服务系统的多个程序一般会搭建在多个容器里 , 互相之间协作提供服务 . 常见的镜像在Docker Hub都能找到直接使用 , 接下来就是根据需要 , 选择对应程序版本的镜像 .

举例说明 , 看一下 Docker 官方提供的 OpenJDK 镜像的说明页面 .

[https://hub.docker.com/\_/openjdk](https://hub.docker.com/_/openjdk)

镜像的维护者会在镜像介绍中展示出镜像所有的 Tag , 如果没有可以从页面上的 Tags 导航里进入到镜像标签列表页面 . 在 OpenJDK 镜像的 Tag 列表里 , 可以看到同样版本号的镜像就存在多种标签 . 在这些不同的标签上 , 除了定义 OpenJDK 的版本 , 还有操作系统 , 软件提供者等信息 .

镜像维护者提供这么多的标签进行选择 , 其实方便了在不同场景下选择不同环境实现细节时 , 都能直接用到这个镜像 , 而不需要再单独编写Dockerfile并构建 .

#### Alpine 镜像

镜像标签中的Alpine其实指的是这个镜像内的文件系统内容 , 是基于 Alpine Linux 这个操作系统的 . Alpine Linux是一个相当精简的操作系统 , 而基于它的Docker镜像可以仅有数MB的尺寸 . 如果软件基于这样的系统镜像之上构建而得 , 可以想象新的镜像也是十分小巧的 .

Alpine系统的镜像大小

![](/assets/alpine.png)

Alpine 系统镜像的尺寸要远小于其他常见的系统镜像 .

![](/assets/alpinepython.png)

由于基于 Alpine 系统建立的软件镜像远远小于基于其他系统的软件镜像 , 它在网络传输上的优势尤为明显 . 当然 , 有优点也会有缺点 , Alpine 镜像的缺点就在于它实在过于精简 , 缺少很多常见的工具和类库 , 二次构建搭建的过程相当烦琐 .

#### 对容器进行配置

以 MySQL 为例 , 自己安装过 MySQL 的朋友一定知道 , 搭建 MySQL 最麻烦的地方并不是安装的过程 , 而是安装后进行初始化配置的过程 . 就拿更改 root 账号的密码来说 , 在初始的 MySQL 里就要耗费不少工作量 .

拿到MySQL镜像后 , 面对的就是复杂的初始化过程 . 镜像作者都会准备一些自动化脚本 , 方便快速的初始化 .

对于 MySQL 镜像来说 , 进行软件配置的方法是通过环境变量的方式来实现的 \( 在其他的镜像里 , 还有通过启动命令、挂载等方式来实现的 \) . 

可以通过下面的命令来直接建立 MySQL 中的用户和数据库 : 

```
docker run --name mysql -e MYSQL_DATABASE=webapp -e MYSQL_USER=www -e MYSQL_PASSWORD=my-secret-pw -d mysql:5.7
```

如果深究 MySQL 是如何实现这样复杂的功能的 , 可以在 MySQL 镜像的 Dockerfile 源码库里 , 找到[docker-entrypoint.sh](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fdocker-library%2Fmysql%2Fblob%2Fmaster%2F5.7%2Fdocker-entrypoint.sh)脚本 . 

