# 通过Dockerfile创建镜像

#### 关于 Dockerfile

Dockerfile 是 Docker 中用于定义镜像自动化构建流程的配置文件 , 包含了构建镜像过程中需要执行的命令和其他操作 .

Dockerfile 在网络等其他介质中传递的速度极快 , 能够更快的帮助我们实现容器迁移和集群部署 .

在 Dockerfile 中 , 拥有一套独立的指令语法 , 其用于给出镜像构建过程中所要执行的过程 .

**环境搭建与镜像构建**

在一个完整的开发、测试、部署过程中 , 程序运行环境的定义通常是由开发人员来进行的 , 因为他们更加熟悉程序运转的各个细节 . 为了方便测试和运维搭建相同的程序运行环境 , 常用的做法是由开发人员编写一套环境搭建手册 , 帮助测试人员和运维人员了解环境搭建的流程 .

而 Dockerfile 就很像一个能够完成自动构建的搭建手册 , 包含的就是一个构建容器的过程 .

#### 编写 Dockerfile

前面介绍了提交容器修改 , 再进行镜像迁移的方式 . 但是使用 Dockerfile 的优势更多 :

* Dockerfile 的体积远小于镜像包 , 更容易进行快速迁移和部署
* 环境构建流程记录了 Dockerfile 中 , 能够直观的看到镜像构建的顺序和逻辑
* 使用 Dockerfile 来构建镜像能够更轻松的实现自动部署等自动化流程
* 在修改环境搭建细节时 , 修改 Dockerfile 文件要比从新提交镜像来的轻松、简单

**看一个完整的 Dockerfile 的例子**

用于构建 Docker 官方所提供的 Redis 镜像的 Dockerfile 文件 :

```Dockerfile
FROM debian:stretch-slim

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r redis && useradd -r -g redis redis

# grab gosu for easy step-down from root
# https://github.com/tianon/gosu/releases
ENV GOSU_VERSION 1.10
RUN set -ex; \
    \
    fetchDeps=" \
        ca-certificates \
        dirmngr \
        gnupg \
        wget \
    "; \
    apt-get update; \
    apt-get install -y --no-install-recommends $fetchDeps; \
    rm -rf /var/lib/apt/lists/*; \
    \
    dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
    wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
    wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
    export GNUPGHOME="$(mktemp -d)"; \
    gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
    gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
    gpgconf --kill all; \
    rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc; \
    chmod +x /usr/local/bin/gosu; \
    gosu nobody true; \
    \
    apt-get purge -y --auto-remove $fetchDeps

ENV REDIS_VERSION 3.2.12
ENV REDIS_DOWNLOAD_URL http://download.redis.io/releases/redis-3.2.12.tar.gz
ENV REDIS_DOWNLOAD_SHA 98c4254ae1be4e452aa7884245471501c9aa657993e0318d88f048093e7f88fd

# for redis-sentinel see: http://redis.io/topics/sentinel
RUN set -ex; \
    \
    buildDeps=' \
        wget \
        \
        gcc \
        libc6-dev \
        make \
    '; \
    apt-get update; \
    apt-get install -y $buildDeps --no-install-recommends; \
    rm -rf /var/lib/apt/lists/*; \
    \
    wget -O redis.tar.gz "$REDIS_DOWNLOAD_URL"; \
    echo "$REDIS_DOWNLOAD_SHA *redis.tar.gz" | sha256sum -c -; \
    mkdir -p /usr/src/redis; \
    tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1; \
    rm redis.tar.gz; \
    \
# disable Redis protected mode [1] as it is unnecessary in context of Docker
# (ports are not automatically exposed when running inside Docker, but rather explicitly by specifying -p / -P)
# [1]: https://github.com/antirez/redis/commit/edd4d555df57dc84265fdfb4ef59a4678832f6da
    grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 1$' /usr/src/redis/src/server.h; \
    sed -ri 's!^(#define CONFIG_DEFAULT_PROTECTED_MODE) 1$!\1 0!' /usr/src/redis/src/server.h; \
    grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 0$' /usr/src/redis/src/server.h; \
# for future reference, we modify this directly in the source instead of just supplying a default configuration flag because apparently "if you specify any argument to redis-server, [it assumes] you are going to specify everything"
# see also https://github.com/docker-library/redis/issues/4#issuecomment-50780840
# (more exactly, this makes sure the default behavior of "save on SIGTERM" stays functional by default)
    \
    make -C /usr/src/redis -j "$(nproc)"; \
    make -C /usr/src/redis install; \
    \
    rm -r /usr/src/redis; \
    \
    apt-get purge -y --auto-remove $buildDeps

RUN mkdir /data && chown redis:redis /data
VOLUME /data
WORKDIR /data

COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 6379
CMD ["redis-server"]
```

**Dockerfile 的结构**

可以将 Dockerfile 理解为一个由上往下执行指令的脚本文件 , 指令简单分为五大类 :

* **基础指令** - 用于定义新镜像的基础和性质
* **控制指令** - 是指导镜像构建的核心部分 , 用于描述镜像在构建过程中需要执行的命令
* **引入指令** - 用于将外部文件直接引入到构建镜像内部
* **执行指令** - 能够为基于镜像所创建的容器 , 指定在启动时需要执行的脚本或命令
* **配置指令** - 对镜像以及基于镜像所创建的容器 , 可以通过配置指令对其网络、用户等内容进行配置

这五类命令并非都会出现在一个 Dockerfile 里 , 但却对基于这个 Dockerfile 所构建镜像形成不同的影响 .

#### 常见 Dockerfile 指令

**FROM**

在 Dockerfile 里 , 可以通过 FROM 指令指定一个基础镜像 , 接下来所有的指令都是基于这个镜像所展开的 . 在镜像构建的过程中 , Docker 也会先获取到这个给出的基础镜像 , 再从这个镜像上进行构建操作 .

FROM 指令支持三种形式 , 核心逻辑就是指出能够被 Docker 识别的那个镜像 .

```Dockerfile
FROM <image> [AS <name>]
FROM <image>[:<tag>] [AS <name>]
FROM <image>[@<digest>] [AS <name>]
```

既然选择一个基础镜像是构建新镜像的根本 , 那么 Dockerfile 中的第一条指令必须是 FROM 指令 , 因为没有了基础镜像 , 一切构建过程都无法开展 .

当然 , 一个Dockerfile要以FROM指令作为开始并不意味着FROM只能是Dockerfile中的第一条指令 . 在Dockerfile中可以多次出现FROM指令 , 当FROM第二次或者之后出现时 , 表示在此刻构建时 , 要将当前指出镜像的内容合并到此刻构建镜像的内容里 . 这对于直接合并两个镜像的功能很有帮助 .

**RUN**

RUN 指令就是用于向控制台发送命令的指令 .

在 RUN 指令之后 , 直接拼接上需要执行的命令 , 在构建时 , Docker 就会执行这些命令 , 并将它们对文件系统的修改记录下来 , 形成镜像的变化 .

```Dockerfile
RUN <command>
RUN ["executable", "param1", "param2"]
```

RUN 指令是支持  换行的 , 如果单行的长度过长 , 可以对内容进行切割 , 方便阅读 .

**ENTRYPOINT 和 CMD**

基于镜像启动的容器 , 在容器启动时会根据镜像所定义的一条命令来启动容器中进程号为 1 的进程 . 通过 Dockerfile 中的 ENTRYPOINT 和 CMD 实现 .

```
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2

CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
```

ENTRYPOINT 指令和 CMD 指令的用法近似 , 都是给出需要执行的命令 , 并且它们都可以为空 , 或者说是不在 Dockerfile 里指出 .

当 ENTRYPOINT 与 CMD 同时给出时 , CMD 中的内容会作为 ENTRYPOINT 定义命令的参数 , 最终执行容器启动的还是 ENTRYPOINT 中给出的命令 .

**EXPOSE**

通过 EXPOSE 指令可以为镜像指定要暴露的端口 .

```
EXPOSE <port> [<port>/<protocol>...]
```

当通过 EXPOSE 指令配置了镜像的端口暴露定义 , 基于这个镜像所创建的容器 , 在被其他容器通过`--link`选项连接时 , 就能够直接允许来自其他容器对这些端口的访问了 .

##### VOLUME

在 Dockerfile 里 , 提供了VOLUME指令来定义基于此镜像的容器所自动建立的数据卷 .

```
VOLUME ["/data"]
```

在 VOLUME 指令中定义的目录 , 在基于新镜像创建容器时 , 会自动建立为数据卷 , 不需要再单独使用`-v`选项来配置了 .

##### COPY 和 ADD

在制作新的镜像的时候 , 可能需要将一些软件配置、程序代码、执行脚本等直接导入到镜像内的文件系统里 , 使用 COPY 或 ADD 指令能够直接从宿主机的文件系统里拷贝内容到镜像里的文件系统中 .

```
COPY [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] <src>... <dest>

COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

> COPY 与 ADD 指令的定义方式完全一样 , 需要注意的仅是当目录中存在空格时 , 可以使用后两种格式避免空格产生歧义 .

对比 COPY 与 ADD , 两者的区别主要在于 ADD 能够支持使用网络端的 URL 地址作为 src 源 , 并且在源文件被识别为压缩包时 , 自动进行解压 , 而 COPY 没有这两个能力 .

虽然看上去 COPY 能力稍弱 , 但对于那些不希望源文件被解压或没有网络请求的场景 , COPY 指令是个不错的选择 .

#### 构建镜像

在编写好 Dockerfile 之后 , 就可以构建镜像了 :

```
docker build ./webapp
```

`docker build`可以接收一个参数 , 需要特别注意的是 , 这个参数为一个目录路径\(本地路径或 URL 路径\) , 而并非 Dockerfile 文件的路径 . 在`docker build`里 , 给出的目录会作为构建的环境目录 , 很多的操作都是基于这个目录进行的 .

在默认情况下 , `docker build`也会从这个目录下寻找名为 Dockerfile 的文件 , 将它作为 Dockerfile 内容的来源 . 如果Dockerfile文件路径不在这个目录下 , 或者有另外的文件名 , 可以通过`-f`选项单独给出 Dockerfile 文件的路径 .

```
docker build -t webapp:latest -f ./webapp/a.Dockerfile ./webapp
```

这里的`-t`选项是用来指定新生成镜像的名称的 . 

