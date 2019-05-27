# 应用于服务化开发

#### 服务开发环境

先设定一个场景 , 假定我们处于一个Dubbo治理下的微服务系统 , 而工作是开发系统中某一项微服务 .

在微服务开发中 , 所开发的功能都不是完整的系统 , 很多功能需要与其他服务之间配合才能正常运转 , 而开发所使用的机器时常无法满足在一台机器上将这些相关服务同时运行起来 . 我们仅仅是开发某一部分服务的内容 , 既对其他服务的运转机制不太了解 , 又完全没有必要在自己的机器上运行其他的服务 .

所以最佳的实践自然就是让参与系统中服务开发的同事 , 各自维护自己开发服务的环境 , 而直接提供对应的连接地址使用服务即可 . 

**搭建本地环境**

在开发机器上 , 只需要运行正在开发的服务 , 这个过程依然可以使用 Docker Compose 来完成 . 这里给出了一个简单的例子 , 表示一个简单的小服务运行环境 : 

```yaml
version: "3"

networks:
  backend:
  mesh:

services:

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

  app:
    build: ./spring
    networks:
      - mesh
      - backend
    volumes:
      - ../app:/app
    depends_on:
      - mysql
```



