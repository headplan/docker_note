# 使用容器

**相关命令**

```
# 列出镜像
docker images
```

### 列出镜像

列出已经下载的镜像

```
docker images
```

列表包含了仓库名,标签,镜像ID,创建时间,占用空间.

##### 镜像体积

Docker Hub上显示的是镜像压缩后的体积.使用命令docker images列表中的镜像体积也并非硬盘实际消耗的体积,因为Docker镜像是多层存储的,里面有很多继承,复用的关系.所以比实际的要小很多.

##### 虚悬镜像

镜像列表中即没有仓库名,也没有标签名的镜像,是镜像发布了新版本后,重新pull后,新旧镜像同名,旧镜像名称被取消产生的&lt;none&gt;镜像.这种无标签的镜像就是虚悬镜像\(dangling image\).

> docker pull 和 docker build都会导致新旧镜像替换产生的虚悬镜像

```
# 查看虚悬镜像列表
docker images -f dangling=true
```

一般情况下,虚悬镜像已经失去了存在的价值,可以随意删除

```
docker rmi (docker images -q -f dangling=true)
```

##### 中间层镜像

为了加速惊现构建和重复利用资源,Docker会利用中间层镜像作为依赖镜像.使用参数`-a`列出包括所有镜像包括中间层

```
docker images -a
```

这时会列出很多无标签镜像,但有别于虚悬镜像,删除会因为依赖丢失而出错.中间层镜像会随依赖删除而删除.

##### 列出部分镜像

根据仓库名列出镜像

```
docker images ubuntu
```

列出特定的某个镜像\(指定仓库名和标签\)

```
docker images ubuntu:16.04
```

使用过滤器参数`-f(--filter)`过滤镜像

```
# 在mongo:3.2之后建立的镜像
docker images -f since=mongo:3.2
# 查看某个位置之前的镜像
docker images -f before=mongo:3.2
# 还可以通过label过滤镜像列表
docker images -f label=com.example.version=0.1
```

##### 以特定格式显示

显示镜像ID列表

```
docker images -q
```

还可以配合上面提到的`--filter`过滤ID列表

应用Go的模板语法使用`--format`参数自定义列表

```
docker images --format "{{.ID}}: {{.Repository}}"
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```



