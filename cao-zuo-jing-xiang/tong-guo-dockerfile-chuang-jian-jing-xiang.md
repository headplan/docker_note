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


