# 使用 Docker Compose 管理容器

#### 解决容器管理问题

即对容器组合进行管理 .

**Docker Compose**

开发中最常使用的多容器定义和运行软件 , Docker Compose . 如果说Dockerfile是将容器内运行环境的搭建固化下来 , 那么Docker Compose就可以理解为将多个容器运行的方式和配置固化下来 .

在Docker Compose里 , 通过一个配置文件 , 将所有与应用系统相关的软件及它们对应的容器进行配置 , 之后使用Docker Compose提供的命令进行启动 , 就能让Docker Compose将刚才我们所提到的那些复杂问题解决掉 .

#### 安装Docker Composer

Docker Compose 目前也是由Docker官方主要维护 , 但其却不属于 Docker Engine 的一部分 , 而是一个独立的软件 . Docker Compose 是一个由 Python 编写的软件 , 在拥有 Python 运行环境的机器上 , 可以直接运行它 . 

可以通过下面的命令下载 Docker Compose 到应用执行目录 , 并附上运行权限 , 这样 Docker Compose 就可以在机器中使用了 . 

```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
$
$ sudo docker-compose version
docker-compose version 1.21.2, build a133471
docker-py version: 3.3.0
CPython version: 3.6.5
OpenSSL version: OpenSSL 1.0.1t  3 May 2016
```

也可以通过 Python 的包管理工具 pip 来安装 Docker Compose . 

```
$ sudo pip install docker-compose
```

**在 Windows 和 macOS 中的 Docker Compose**

在常用的Windows和macOS中 , 使用Docker Compose会来得更加方便 . 不论是使用Docker for Win还是Docker for Mac , 亦或是 Docker Toolbox来搭建Docker运行环境 , 都可以直接使用`docker-compose`这个命令 . 这三款软件都已经将Docker Compose内置在其中 . 

#### Docker Compose 的基本使用逻辑

将使用 Docker Compose 的步骤简化来说 , 可以分成三步 : 

* 如果需要的话 , 编写容器所需镜像的 Dockerfile\(也可以使用现有的镜像\)
* 编写用于配置容器的docker-compose.yml
* 使用docker-compose命令启动应用

**编写 Docker Compose 配置**

配置文件是Docker Compose的核心部分 , 正是通过它去定义组成应用服务容器群的各项配置 , 而编写配置文件 , 则是使用 Docker Compose 过程中最核心的一个步骤 . 

Docker Compose 的配置文件是一个基于YAML格式的文件 . 与 Dockerfile 采用 Dockerfile 这个名字作为镜像构建定义的默认文件名一样 . Docker Compose 的配置文件也有一个缺省的文件名 , 就是 docker-compose.yml , 如非必要 , 建议直接使用这个文件名来做 Docker Compose项目的定义 . 

看一个简单的 Docker Compose 配置文件内容 : 

```yaml
version: '3'

services:

  webapp:
    build: ./image/webapp
    ports:
      - "5000:5000"
    volumes:
      - ./code:/code
      - logvolume:/var/log
    links:
      - mysql
      - redis

  redis:
    image: redis:3.2
  
  mysql:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=my-secret-pw

volumes:
  logvolume: {}
```



