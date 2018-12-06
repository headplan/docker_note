# 使用容器

#### 镜像列表

```
# 列出镜像
docker images
# 查看虚悬镜像列表
docker images -f dangling=true
# 列出中间层镜像
docker images -a
# 列出部分镜像
docker images ubuntu
docker images ubuntu:16.04

# 显示镜像ID列表
docker images -q
# 过滤镜像
# 在mongo:3.2之后建立的镜像
docker images -f since=mongo:3.2
# 查看某个位置之前的镜像
docker images -f before=mongo:3.2
# 还可以通过label过滤镜像列表
docker images -f label=com.example.version=0.1

# 删除虚悬镜像
docker rmi (docker images -q -f dangling=true)

# Go模板自定义列表
docker images --format "{{.ID}}: {{.Repository}}"
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

#### 镜像操作

```
# 获取镜像
docker pull [选项] [Docker Registry地址]<仓库名>:<标签>
docker pull ubuntu
docker pull openresty/openresty:1.13.6.2-alpine
# 镜像搜索
docker search mysql
# 镜像删除
docker rmi mysql
docker rmi a339c66710f6
# 查看容器/镜像详细信息数据
docker inspect mysql
```

#### 容器管理

```
# 创建容器
docker create nginx:1.12
# 创建并命名容器
docker create --name my_nginx nginx:1.12
# 启动容器
docker start my_nginx
# 运行容器 - 创建并启动容器
docker run --name nginx -d nginx:1.12
# 查看运行中的容器
docker ps
# 查看所有状态的容器
docker -a
docker --all
# 停止容器
docker stop nginx
# 删除容器
docker rm nginx
# 强制删除容器
docker rm nginx --force 或 -f
# 在容器中运行指定命令
docker exec nginx more /etc/hostname
# 进入容器并进入控制台
docker exec -it nginx bash
# 衔接到容器
docker attach nginx
```

#### 网络配置

```
# 容器互联
docker run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
# 连接到同一网络
docker run -d --name webapp --link mysql webapp:latest
# 别名连接
docker run -d --name webapp --link mysql:database webapp:latest
# 开放端口
docker run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes --expose 13306 --expose 23306 mysql:5.7
```

#### 网络管理

```
# 创建虚拟子网(bridge、host、overlay、maclan、none)
docker network create -d bridge individual
# 查看已经存在的网络
docker network ls
docker network list
# 端口映射(-p或--publish)
docker run -d --name nginx -p 80:80 -p 443:443 nginx:1.12
```



