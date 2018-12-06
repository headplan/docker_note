# 管理和存储数据

#### 数据管理实现方式

Docker容器中文件问题

* 沙盒文件系统是跟随容器生命周期所创建和移除的 , 数据无法直接被持久化存储 . 
* 由于容器隔离 , 很难从容器外部获得或操作容器内部文件中的数据 . 

Docker文件系统基于UnionFS , 支持挂在不同类型的文件系统到统一的目录结构中 , 互通容器内外的文件 , 很好的解决了数据持久化和操作容器内文件的问题 .

> UnionFS带来的读写性能损失可以忽略不计 .

#### 挂载方式

Docker提供了三种适用于不同场景的文件系统挂载方式

![](/assets/wenjianxitongguazai.png)

* **Bind Mount** - 能够直接将宿主操作系统中的目录和文件挂载到容器内的文件系统中 , 通过指定容器外的路径和容器内的路径 , 就可以形成挂载映射关系 , 在容器内外对文件的读写 , 都是相互可见的 . 
* **Volume** - 也是从宿主操作系统中挂载目录到容器内 , 只不过这个挂载的目录由Docker进行管理 , 只需要指定容器内的目录 , 不需要关心具体挂载到了宿主操作系统中的哪里 .
* **Tmpfs Mount** - 支持挂载系统内存中的一部分到容器的文件系统里 , 不过由于内存和容器的特征 , 它的存储并不是持久的 , 其中的内容会随着容器的停止而消失 . 

#### 挂载文件到容器

使用`-v`或`--volume`来挂载宿主操作系统目录\(或文件\) .

```
--v <host-path>:<container-path>
--volume <host-path>:<container-path>
```

> host-path 和 container-path 分别代表宿主操作系统中的目录和容器中的目录 . 为了避免混淆 , Docker 这里强制定义目录时必须使用绝对路径 , 不能使用相对路径 .

```
docker run -d --name nginx -v /webapp/html:/usr/share/nginx/html nginx:1.12
```

挂载了目录的容器启动后 , 可以运行查看

```
docker exec nginx ls /usr/share/nginx/html
```

在docker inspect的结果里 , 可以看到有关容器数据挂载相关的信息

```
docker inspect nginx
[
    {
## ......
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/webapp/html",
                "Destination": "/usr/share/nginx/html",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
## ......
    }
]
```

信息中返回的RW为true , 表示挂载目录或文件的读写性 . 实际操作中 , Docker还支持以只读的方式挂载 , 例如

```
docker run -d --name nginx -v /webapp/html:/usr/share/nginx/html:ro nginx:1.12
```

使用Bind Mount方式常见场景 :

当需要从宿主操作系统共享配置的时候 . 对于一些配置项 , 可以直接从容器外部挂载到容器中 , 这利于保证容器中的配置为我们所确认的值 , 也方便对配置进行监控 . 例如 , 遇到容器中时区不正确的时候，可以直接将操作系统的时区配置 , 也就是 /etc/timezone 这个文件挂载并覆盖容器中的时区配置 .

当使用 Docker 进行开发的时候 . 虽然在 Docker 中 , 推崇直接将代码和配置打包进镜像 , 以便快速部署和快速重建 . 但开发过程中非常不方便 , 可以直接把代码挂载进入容器 , 这样每次对代码修改都可以直接在容器外部进行 .

#### 挂载临时文件目录

Tmpfs Mount 是一种特殊的挂载方式 , 它主要利用内存来存储数据 . 由于内存不是持久性存储设备 , 所以其带给 Tmpfs Mount 的特征就是临时性挂载 . 

通过--tmpfs选项来完成 , 只需传递挂载到容器内的目录即可

```
docker run -d --name webapp --tmpfs /webapp/cache webapp:latest
```

通过`docker inspect`命令查看

```
[
    {
## ......
         "Tmpfs": {
            "/webapp/cache": ""
        },
## ......
    }
]
```

挂载临时文件说明它不是持久存储的 , 常见应用场景是

* 应用中使用到 , 但不需要进行持久保存的敏感数据 , 可以借助内存的非持久性和程序隔离性进行一定的安全保障
* 读写速度要求较高 , 数据变化量大 , 但不需要持久保存的数据 , 可以借助内存的高读写速度减少操作的时间

#### 使用数据卷

前面提到的目录挂载类似其他虚拟机工具 , Docker还创造了数据卷概念 , 就是Volume . 其本质依然是宿主操作系统上的一个目录 , 只不过这个目录存放在Docker内部 , 接受Docker管理 . 

Volume的挂载依然使用`-v`或`--volume`选项来定义数据卷的挂载

```
docker run -d --name webapp -v /webapp/storage webapp:latest
```

查看容器中数据卷挂载的信息

```
[
    {
## ......
        "Mounts": [
            {
                "Type": "volume",
                "Name": "2bbd2719b81fbe030e6f446243386d763ef25879ec82bb60c9be7ef7f3a25336",
                "Source": "/var/lib/docker/volumes/2bbd2719b81fbe030e6f446243386d763ef25879ec82bb60c9be7ef7f3a25336/_data",
                "Destination": "/webapp/storage",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
## ......
    }
]
```



