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

