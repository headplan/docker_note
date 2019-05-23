# 编写Docker Compose项目

#### 设计项目的目录结构

```
./
├── bin/
├── composer/
├── mysql/
├── nginx/
├── phpfpm/
├── redis/
└── website/
```

第一类是Docker定义目录 , 也就是compose这个目录 . 在这个目录里 , 包含了docker-compose.yml这个用于定义Docker Compose项目的配置文件 . 此外 , 还包含了用于构建自定义镜像的内容 .

第二类是程序文件目录 , 在这个项目里是mysql、nginx、phpfpm、redis这四个目录 . 这些目录分别对应着Docker Compose中定义的服务 , 在其中主要存放对应程序的配置 , 产生的数据或日志等内容 .

第三类是代码目录 , 在这个项目中就是存放 Web 程序的website目录 . 将代码统一放在这个目录中 , 方便在容器中挂载 .

第四类是工具命令目录 , 这里指 bin 这个目录 . 在这里存放一些自己编写的命令脚本 , 通过这些脚本可以更简洁地操作整个项目 .

#### 编写 Docker Compose 配置文件

接下来就要编写docker-compose.yml文件来定义组成这个环境的所有Docker容器以及与它们相关的内容了 .

```yaml
version: "3"

networks:
  frontend:
  backend:

services:

  redis:
    image: redis:3.2
    networks:
      - backend
    volumes:
      - ../redis/redis.conf:/etc/redis/redis.conf:ro
      - ../redis/data:/data
    command: ["redis-server", "/etc/redis/redis.conf"]
    ports:
      - "6379:6379"

  mysql:
    image: mysql:5.7
    networks:
      - backend
    volumes:
      - ../mysql/my.cnf:/etc/mysql/my.cnf:ro
      - ../mysql/data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: my-secret-pw
    ports:
      - "3306:3306"

  phpfpm:
    build: ./phpfpm
    networks:
      - frontend
      - backend
    volumes:
      - ../phpfpm/php.ini:/usr/local/etc/php/php.ini:ro
      - ../phpfpm/php-fpm.conf:/usr/local/etc/php-fpm.conf:ro
      - ../phpfpm/php-fpm.d:/usr/local/etc/php-fpm.d:ro
      - ../phpfpm/crontab:/etc/crontab:ro
      - ../website:/website
    depends_on:
      - redis
      - mysql

  nginx:
    image: nginx:1.12
    networks:
      - frontend
    volumes:
      - ../nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ../nginx/conf.d:/etc/nginx/conf.d:ro
      - ../website:/website
    depends_on:
      - phpfpm
    ports:
      - "80:80"
```

项目中直接采用了MySQL、Redis和Nginx三个官方镜像 , 而对于PHP-FPM的镜像 , 需要增加一些功能 , 所以通过Dockerfile构建的方式来生成 .

对于MySQL来说 , 需要为其设置密码 . 原则上是需要对其进行改造并生成新的镜像来使用的 , 而由于MySQL镜像可以通过之前在镜像使用方法一节所提到的环境变量配置的方式 , 来直接指定MySQL的密码及其他一些关键性内容 , 所以就无须单独构建镜像 , 可以直接采用官方镜像并配合使用环境变量来达到目的 .

对于 Redis 来说 , 出于安全考虑 , 一样需要设置密码 . Redis设置密码的方法是通过配置文件来完成的 , 所以需要修改Redis的配置文件并挂载到Redis容器中 . 这个过程也相对简单 , 不过需要注意的是 , 在官方提供的Redis镜像里 , 默认的启动命令是redis-server , 其并没有指定加载配置文件 . 所以在定义Redis容器时 , 要使用command配置修改容器的启动命令 , 使其读取挂载到容器的配置文件 .

#### 自定义镜像

相比较于MySQL、Redis这样可以通过简单配置即可直接使用的镜像不同 , PHP的镜像中缺乏了一些程序中必要的元素 , 而这些部分还是推荐使用自定义镜像的方式将它们加入其中 .

比如例子中 , 因为需要让 PHP 连接到 MySQL 数据库中 , 所以要为镜像中的 PHP 程序安装和开启 pdo\_mysql 扩展 . 在Docker Hub 中php的镜像简介页面 , 可以找到 PHP 镜像中已经准备好的扩展的安装和启用命令 .

编写构建 PHP 镜像的 Dockerfile 文件

```Dockerfile
FROM php:7.2-fpm

MAINTAINER YuAn <YuAn@lartisan.cn>

RUN apt-get update \
 && apt-get install -y --no-install-recommends cron

RUN docker-php-ext-install pdo_mysql

COPY docker-entrypoint.sh /usr/local/bin/

RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["docker-entrypoint.sh"]

CMD ["php-fpm"]
```

由于 Docker 官方所提供的镜像比较精简 , 这里添加了cron定时任务 . 还拷入到镜像中一个脚本文件 , 并将其作为 ENTRYPOINT 启动入口 . 这个文件的作用主要是为了启动 cron 服务 , 以便在容器中可以正常使用它 .

**docker-entrypoint.sh**

```
#!/bin/bash

service cron start

exec "$@"
```

在docker-entrypoint.sh里 , 除了启动cron服务的命令外 , 在脚本的最后看到的是`exec "$@"`这行命令 .

`$@`是 shell 脚本获取参数的符号 , 这里获得的是所有传入脚本的参数 , 而exec是执行命令 , 直接执行这些参数 . 就是之前操作镜像中提到的把CMD参数当做命令代理到脚本中执行 .

#### 目录挂载

例子中会把项目中的一些目录或文件挂载到容器里 , 这样的挂载主要有三种目的 :

* 将程序的配置通过挂载的方式覆盖容器中对应的文件 , 可以直接在容器外修改程序的配置 , 并通过直接重启容器就能应用这些配置
* 把目录挂载到容器中应用数据的输出目录 , 就可以让容器中的程序直接将数据输出到容器外 , 对于MySQL、Redis中的数据 , 程序的日志等内容 , 可以使用这种方法来持久保存它们
* 把代码或者编译后的程序挂载到容器中 , 让它们在容器中可以直接运行 , 这就避免了在开发中反复构建镜像带来的麻烦 , 节省出大量宝贵的开发时间 . 



