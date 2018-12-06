# 镜像

### 运行

运行上面pull的镜像ubuntu:14.04,然后启动里面的bash并且进行交互操作.

```
docker run -it --rm ubuntu:14.04 bash
```

参数详解

* -it : 这里是两个参数,一个是`-i`交互操作,一个是`-t`终端.放在一起的意思是交互式终端
* --rm : 这个参数是说容器退出后随之将其删除.一般情况是不会删除的,一般会手动使用`docker rm`删除.我们现在只是随便测试一下.
* ubuntu:14.04 : 意思是指用ubuntu:14.04镜像为基础来启动容器.
* bash : 放在镜像名后的是命令,bash就是一个交互式shell

查看一下当前系统版本

```
cat /etc/os-release
```

可以看到`Ubuntu 14.04.5 LTS`

最后可以通过`exit`退出

### 镜像构成

每次执行docker run的时候都会指定镜像作为容器运行基础,可以从Docker Hub上下载镜像使用,也可以自定义定制镜像.

> 镜像和容器都是多层存储,镜像在前一层的基础上修改,容器以镜像为基础.

##### 定制一个Web服务器

```
docker run --name webserver -d -p 80:80 nginx
```

这条命令会用nginx镜像启动一个容器,命名为webserver,并且映射了80端口,这样就可以浏览了.

```
http://localhost
```

想要修改默认的访问页面的文字,需要使用`exec`命令进入容器

```
docker exec -it webserver bash
# 修改一些文字
root@3729b97e8226:/# echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
root@3729b97e8226:/# exit
exit
```

经过上面的操作,我们改动了容器的文件,也就是容器的存储层.可以使用`docker diff`命令看到具体的改动.

```
doker diff webserver
```

如果现在就是定制好了我们要改变的内容,就可以保存成镜像了.

容器运行时,如果不使用卷,我们做的任何文件修改都会被记录于容器存储层里,而Docker提供了`docker commit`命令,可以将容器的存储层保存下来成为镜像.意思就是在原有镜像的基础上,再叠加上容器的存储层,构成新的镜像.

语法格式:

```
docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]
```

```
# \是换行符
docker commit \
    --author "Tao Wang <twang2218@gmail.com>" \
    --message "修改了默认网页" \
    webserver \
    nginx:v2
sha256:07e33465974800ce65751acc279adc6ed2dc5ed4e0838f8b86f0c87aa1795214
```

其中--author是指定修改的作者,而--message则是记录本次修改的内容\(和git相似\).

可以在`docker images`中看到新定制的镜像:

```
docker images nginx
# 还可以查看镜像内的历史记录
docker history nginx:v2
```

新的镜像定制好,就可以运行了

```
docker run --name web2 -d -p 81:80 nginx:v2
```

访问`localhost:81`看到记过和`webserver`一样.

##### 慎用docker commit

实际环境中并不会直接使用commit保存分层镜像.使用diff查看镜像区别

```
docker diff webserver
```

会发现除了修改的`/usr/share/nginx/html/index.html`文件外,还有很多被改动或添加的文件.使用commit会添加很多无关的文件到镜像.

使用docker commit意味着所有操作都是黑箱操作,生成的镜像也是黑箱镜像,只有镜像制作的人知道做过什么,维护非常困难.

`docker commit`可以用于被入侵后保存现场等工作,但是定制镜像,还是使用Dockerfile来完成.

### 使用Dockerfile定制镜像

Dockerfile定制镜像就是定制每一层所添加的配置,文件.把每一层修改,安装,构建,操作的命令都写入Dockerfile脚本,这样就解决了,无法重复,镜像构建透明,体积等所有问题.

Dockerfile是一个文本文件,其内包含了一条条的指令\(Instruction\),每一条指令构建一层,因此每一条指令的内容,就是描述该层应当如何构建.

新建Dockerfile文件:

```
mkdir mynginx
cd mynginx
touch Dockerfile
```

内容为:

```
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

其中有两条命令,`FROM`和`RUN`

##### FROM指定基础镜像

在`Dockerfile`中`FROM`是必备指令,并且必须是第一条指令.在Docker Hub上有非常多的高质量官方镜像.

服务镜像

* nginx
* redis
* mongo
* mysql
* httpd
* php
* tomcat

语言镜像

* node
* openjdk
* python
* ruby
* golang

系统镜像

* ubuntu
* debian
* centos
* fedora
* alpine

特殊镜像

```
FROM scratch
```

这个镜像是虚拟的概念个,并不实际存在,表示一个空白的镜像.

代表着不以任何镜像为基础,接下来的指令作为镜像第一层开始.

swarm和coreos/etcd都可以不以任何系统为基础.还有很多实用Go语言开发的应用都是以这种方式制作的镜像,Go也是特别适合容器微服务架构的语言.

##### RUN执行命令

RUN指令是用来执行命令行命令的.有两种格式:

* shell格式

```
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

* exec格式

```
RUN ["可执行文件", "参数1", "参数2"]
```

但是RUN命令也其他命令一样,没使用一条就会建立一层镜像,这是不推荐的,而且Union FS也有最大层数限制.

所以正确的书写格式应该是这样的:

```
FROM debian:jessie

RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```

> Dockerfile支持Shell类的行尾添加`\`换行,行首`#`进行注释
>
> ```
> && apt-get purge -y --auto-remove $buildDeps
> ```
>
> 最后一行清理了所有下载,展开的文件,并清理了apt缓存文件,这么做是必要的,会避免镜像的臃肿

##### 构建镜像

在`Dockerfile`文件所在目录执行

```
docker build -t nginx:v3 .
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM nginx
 ---> e43d811ce2f4
Step 2 : RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
 ---> Running in 9cdc27646c7b
 ---> 44aa4490ce2c
Removing intermediate container 9cdc27646c7b
Successfully built 44aa4490ce2c
```

上面的流程情绪的看到镜像的构建过程.

RUN指令启动了一个容器,指定了命令,并提交了这一层.随后删除了RUN那层.

构建命令

```
docker build [选项] <上下文路径/URL/->
```

`-t nginx:v3`指定最终镜像名称.

##### 镜像构建上下文



