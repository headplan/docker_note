# 容器操作

**列出当前运行的容器**

```
docker ps

===== 扩展

# 列出所有容器
docker ps -a

# 杀死所有正在运行的容器
docker kill $(docker ps -a -q)

# 删除所有已经停止的容器
docker rm $(docker ps -a -q) 

# 列出所有镜像
docker image -a

# 删除所有镜像
docker rmi $(docker images -q) 
```

**列出当前项目运行的容器**

```
docker-compose ps

=====扩展

# 启动容器
docker-compose up -d {容器名称}

# 关闭所有容器
docker-compose stop

# 关闭某个容器
docker-compose stop {容器名称}

# 删除所有容器,使用该命令要小心,因为它会删除数据容器.
docker-compose down
```



