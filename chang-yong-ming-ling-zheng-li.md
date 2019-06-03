# 常用命令整理

**查看Client和Server两端的版本信息**

```
docker version
```

**查看Docker Engine相关信息**

```
docker info
```

可以看到正在运行的 Docker Engine 实例中运行的容器数量 , 存储的引擎等等 .

#### 查看镜像列表

**列出镜像**

```
docker images
docker image ls
```

**查看虚悬镜像\(无名镜像\)列表**_\(镜像没有仓库名或没有标签查询显示虚悬镜像\)_

```
docker images -f dangling=true
# 删除所有虚悬镜像
docker rmi $(docker images -q -f dangling=true)
```

**列出中间层镜像\(所有镜像,包括中间层\)**_\(为了加速镜像构建,重复利用资源,docker会利用中间层镜像\)_

```
docker images -a
```

**列出部分镜像**

```
docker images ubuntu
docker images ubuntu:16.04
```

**显示镜像ID列表**

```
docker images -q
```

**过滤镜像**

```
# 在mongo:3.2之后建立的镜像
docker images -f since=mongo:3.2
# 查看某个位置之前的镜像
docker images -f before=mongo:3.2
# 还可以通过label过滤镜像列表
docker images -f label=com.example.version=0.1
```

**Go模板自定义列表**

```
docker images --format "{{.ID}}: {{.Repository}}"
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```



