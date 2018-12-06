# 使用容器

#### images相关命令

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



