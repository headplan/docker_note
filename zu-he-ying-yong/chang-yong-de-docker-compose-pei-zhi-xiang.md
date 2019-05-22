# 常用的 Docker Compose 配置项

#### 定义服务

理解在开发中常用的 Docker Compose 配置 :

```yaml
version: "3"

services:

  redis:
    image: redis:3.2
    networks:
      - backend
    volumes:
      - ./redis/redis.conf:/etc/redis.conf:ro
    ports:
      - "6379:6379"
    command: ["redis-server", "/etc/redis.conf"]

  database:
    image: mysql:5.7
    networks:
      - backend
    volumes:
      - ./mysql/my.cnf:/etc/mysql/my.cnf:ro
      - mysql-data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=my-secret-pw
    ports:
      - "3306:3306"

  webapp:
    build: ./webapp
    networks:
      - frontend
      - backend
    volumes:
      - ./webapp:/webapp
    depends_on:
      - redis
      - database

  nginx:
    image: nginx:1.12
    networks:
      - frontend
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./webapp/html:/webapp/html
    depends_on:
      - webapp
    ports:
      - "80:80"
      - "443:443"

networks:
  frontend:
  backend:

volumes:
  mysql-data:
```

Docker Compose 中的服务 , 是对一组相同容器集群统一配置的定义 , 所以在 Docker Compose 里 , 主要的内容也是对容器配置的定义 . 上面的配置文件中services的篇幅也最大 .

在 Docker Compose 的配置文件里 , 对服务的定义与之前的创建和启动容器中的选项非常相似 , 或者说 Docker Compose 就是从配置文件中读取出这些内容 , 代我们创建和管理这些容器的 .

上面的例子中 , redis、database、webapp、nginx 就是服务的名称 .

**指定镜像**

**依赖声明**

#### 文件挂载

#### 网络配置

网络也是容器间互相访问的桥梁 , 所以网络的配置对于多个容器组成的应用系统来说也是非常重要的 . 在 Docker Compose 里 , 可以为整个应用系统设置一个或多个网络 .

声明网络的配置同样独立于 services 存在 , 是位于根配置下的 networks 配置 . 上面的例子里 , 可以看到声明了 frontend 和 backend 这两个网络最简单的方式 .

简单的声明 , Docker Compose 会自动按默认形式完成网络配置外 . 也可以自定义网络参数 :

```yaml
networks:
  frontend:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 10.10.1.0/24
```

这里定义了网络驱动的类型 , 并指定了子网的网段 .

**使用网络别名**

可以为服务单独设置网络别名 , 在其他容器里 , 将这个别名作为网络地址进行访问 .

```yaml
## ......
  database:
    networks:
      backend:
        aliases:
          - backend.database
## ......
  webapp:
    networks:
      backend:
        aliases:
          - backend.webapp
      frontend:
        aliases:
          - frontend.webapp
## ......
```

**端口映射**

在 Docker Compose 的每个服务配置里 , 还看到了 ports 这个配置项 , 它是用来定义端口映射的 . 

可以利用它进行宿主机与容器端口的映射 , 这个配置与 docker CLI 中`-p`选项的使用方法是近似的 . 

> 需要注意的是 , 由于 YAML 格式对 xx:yy 这种格式的解析有特殊性 , 在设置小于 60 的值时 , 会被当成时间而不是字符串来处理 , 所以最好使用引号将端口映射的定义包裹起来 , 避免歧义 .



