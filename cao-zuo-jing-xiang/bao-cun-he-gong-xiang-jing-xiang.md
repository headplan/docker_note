# 保存和共享镜像

通过将容器打包成镜像 , 再利用体积远小于其他虚拟化软件的 Docker 镜像 , 可以更快的将它们复制到其他的机器上 .

#### 提交容器更改

前面已经了解到 , Docker 镜像的本质是多个基于 UnionFS 的镜像层依次挂载的结果 , 而容器的文件系统则是在以只读方式挂载镜像后增加的一个可读可写的沙盒环境 .

Docker提供了将容器中的这个可读可写的沙盒环境持久化为一个镜像层的方法 . 也就是说可以将Docker镜像中的修改记录下来 , 保存为一个新的镜像 .

将容器修改的内容保存为镜像的命令 , 很像代码仓库里的修改 , 而记录容器修改的过程又像是在提交代码 , 所以这里称之为提交容器的更改 :

```
docker commit webapp
```

Docker 执行将容器内沙盒文件系统记录成镜像层的时候 , 会先暂停容器的运行 , 以保证容器内的文件系统处于一个相对稳定的状态 , 确保数据的一致性 .

提交镜像更新后 , 可以得到 Docker 创建的新镜像的ID :

```
docker images
```

和Git很像 , 提交容器更改时可以加描述 :

```
docker commit -m "Configured" webapp
```

**为镜像命名**

提交容器更新后产生的镜像并没 REPOSITORY 和 TAG 的内容 , 也就是说新的镜像还没有名字 . 为镜像取名的命令 :

```
docker tag 0bc42f7ff218 webapp:1.0
```

使用`docker tag`能够为未命名的镜像指定镜像名 , 也能够对已有的镜像创建一个新的命名 . 他们IMAGE ID都是一样的 .

#### 镜像的迁移

Docker是以集中的方式管理镜像的 , 所以在迁移之前要先从 Docker 中取出镜像 :

```
docker save webapp:1.0 > webapp-1.0.tar
```

用Linux管道方式不太友好 , 可以使用`-o`选项来指定输出文件 :

```
docker save -o ./webapp-1.0.tar webapp:1.0
```

**导入镜像**

导出的镜像可以复制到其他机器上 , 然后导入 :

```
docker load < webapp-1.0.tar
```

`docker load`命令是从输入流中读取镜像的数据 , 所以也用到了管道符 . 当然 , 也可以使用参数`-i`选项指定输入文件 :

```
docker load -i webapp-1.0.tar
```

导入后可以通过`docker images`查看了 .

**批量迁移**

通过前面的两个命令 , 接多个包就可以批量操作了 :

```
docker save -o ./images.tar webapp:1.0 nginx:1.12 mysql:5.7
```

可以打成一个包 , 然后一起导入 .

#### 导出和导入容器

这里你可能认为标题重复了 , 提交镜像修改 , 再导出镜像进行迁移可以何为一个命令 : 

```
docker export -o ./webapp.tar webapp
```

使用`docker export`导出的容器包 , 可以使用`docker import`导入 . 

这里需要注意的是 , 使用`docker import`并非直接将容器导入 , 而是将容器运行时的内容以镜像的形式导入 . 所以导入的结果其实是一个镜像 . 

在`docker import`的参数里 , 可以给这个镜像命名 . 

```
docker import ./webapp.tar webapp:1.0
```

在开发的过程中 , 使用`docker save`和`docker load` , 或者是使用`docker export`和`docker import`都可以达到迁移容器或者镜像的目的 . 

