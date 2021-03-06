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

#### 管理容器

罗列出处于运行中的容器

```
docker ps
```

添加参数`-a`或`--all`列出所有状态的容器 .

展示的列表中的分类都比较容易理解 , 其中**COMMAND**表示容器中主程序的启动命令 , 这条命令是在镜像内定义的 , 容器的启动实质就是启动这条命令 .

还有**STATUS**表示容器状态 , 和前面提到的有所区别 , 因为还记录了一些其他信息 , **STATUS**常见有三种状态 :

* Created 此时容器已创建 , 但还没有被启动过 . 
* Up \[ Time \] 这时候容器处于正在运行状态 , 而这里的 Time 表示容器从开始运行到查看时的时间 .
* Exited \(\[ Code \]\) \[ Time \] 容器已经结束运行 , 这里的 Code 表示容器结束运行时 , 主程序返回的程序退出码 , 而 Time 则表示容器结束到查看时的时间 . 

#### 停止和删除容器

将正在运行的容器停止

```
docker stop nginx
```

容器停止后 , 其维持的文件系统沙盒环境还是存在的 , 内部被修改的内容也都会保留 , 所以还可以通过`docker start`命令将这个容器再次启动 .

完全删除容器

```
docker rm nginx
```

容器的删除需要先停止 , 正在运行的容器是不能删除的 , 但是可以使用强制删除 , 虽然强制不太好 .

```
docker rm nginx --force 或 -f
```

#### 随手删除容器

删除需要注意两个问题 , 如何保存容器中的修改 , 还有就是容器服务产生的数据 .

Docker的快读打包镜像和数据卷完美的解决了这两个问题 .

#### 进入容器

让容器运行给出的命令

```
docker exec nginx more /etc/hostname
```

`docker exec`命令能在正在运行的容器中运行指定命令 . 所以 , 我们就可以使用sh或bash运行命令或控制台了 .

```
docker exec -it nginx bash
```

这里的`-it`是利用了简写合并机制 , 分别为

* `-i` 或 `--interactive` 表示保持输入流 , 即保证控制台程序能够正确识别输入的命令
* `-t` 或 `-tty` 启用一个伪终端 , 形成与bash的交互 , 查看bash内部的执行结果

#### 衔接到容器

用于将当前的输入输出流连接到指定的容器上 , 可以理解为与`docker run`中`-d`选项相反的意思

```
docker attach nginx
```

由于输入输出流衔接到了容器的主程序上 , 所以输入输出的操作也直接针对这个程序 , 所以使用Ctrl+C发送停止信号也会让程序停止 , 容器也会随之停止 . 

