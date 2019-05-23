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



