# 运行和管理容器

#### 容器的创建和启动

Docker容器的生命周期分为五种状态

* Created : 容器已经被创建 , 容器所需的相关资源已经准备就绪 , 但容器中的程序还未处于运行状态 . 
* Running : 容器正在运行 , 也就是容器中的应用正在运行 . 
* Paused : 容器已暂停 , 表示容器中的所有程序都处于暂停状态\(不是停止\) . 
* Stopped : 容器处于停止状态 , 占用的资源和沙盒环境都依然存在 , 只是容器中的应用程序均已停止 . 
* Deleted : 容器已删除 , 相关占用的资源及存储在Docker中的管理信息也都已释放和移除 . 

**创建容器**

选择好镜像后 , 就可以创建容器了

```
docker create nginx:1.12
```

Docker根据给出的镜像创建容器 , 控制台打印出容器ID , 此时容器处于Created状态 .

--name参数配置容器名 , 让对容器的操作更方便

```
docker create --name my_nginx nginx:1.12
```

**启动容器**

现在创建的容器状态还是Created , 其内部的应用程序还没有启动 , 接下来要启动它

```
docker start nginx
```

这里直接启动nginx , 就是刚才create时命名的容器 .

**运行容器**

`docker create`和`docker start`两个命令的整合

```
docker run --name nginx -d nginx:1.12
```

这里看一下`-d`参数 , 也就是`--detach`参数 , 这个选项告诉Docker在启动后将程序与控制台分离 , 在后台运行 . 

