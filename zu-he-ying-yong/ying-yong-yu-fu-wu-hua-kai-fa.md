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

#### 跨主机网络

在Docker Compose的小节里 , 介绍了可以通过设置网络别名\( alias \)的方式来更轻松地连接其他容器 , 如果在服务化开发里也能这么做就能减少很多烦琐操作了 .

要实现设置网络别名的目的 , 自然要先确保所有涉及的容器位于同一个网络中 , 这时候就需要引出之前在网络小节里说到的Overlay网络了 .

![](/assets/overlay.png)

Overlay Network 能够跨越物理主机的限制 , 让多个处于不同 Docker daemon 实例中的容器连接到同一个网络 , 并且让这些容器感觉这个网络与其他类型的网络没有区别 .

**Docker Swarm**

要搭建Overlay Network网络 , 要用到Docker Swarm这个工具 . Docker Swarm 是 Docker 内置的集群工具 , 它能够帮助我们更轻松地将服务部署到 Docker daemon 的集群之中 .

![](/assets/swarm.png)Docker Swarm 最初是独立的项目 , 不过目前已经集成到了 Docker 之中 , 通过 docker CLI 的命令就能够直接操控 .

对于 Docker Swarm 来说 , 每一个 Docker daemon 的实例都可以成为集群中的一个节点 , 而在 Docker daemon 加入到集群成为其中的一员后 , 集群的管理节点就能对它进行控制 . 要搭建的 Overlay 网络正是基于这样的集群实现的 .

在任意一个 Docker 实例上都可以通过`docker swarm init`来初始化集群 :

```
sudo docker swarm init

Swarm initialized: current node (t4ydh2o5mwp5io2netepcauyl) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-4dvxvx4n7magy5zh0g0de0xoues9azekw308jlv6hlvqwpriwy-cb43z26n5jbadk024tx0cqz5r 192.168.1.5:2377
```

在集群初始化后 , 这个 Docker 实例就自动成为了集群的管理节点 , 而其他 Docker 实例可以通过运行`docker swarm join`命令来加入集群 . 加入到集群的节点默认为普通节点 , 如果要以管理节点的身份加入到集群中 , 可以通过`docker swarm join-token`命令来获得管理节点的加入命令 .

```
$ sudo docker swarm join-token manager
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-60am9y6axwot0angn1e5inxrpzrj5d6aa91gx72f8et94wztm1-7lz0dth35wywekjd1qn30jtes 192.168.1.5:2377
```

通过这些命令来建立用于服务开发的 Docker 集群 , 并将其他 Docker 加入到这个集群里 , 就完成了搭建跨主机网络的第一步 .

**建立跨主机网络**

建立 Overlay 网络

```
sudo docker network create --driver overlay --attachable mesh
```

在创建 Overlay 网络时 , 要加入`--attachable`选项以便不同机器上的 Docker 容器能够正常使用到它 . 

在创建了这个网络之后 , 可以在任何一个加入到集群的 Docker 实例上使用`docker network ls`查看一下其下的网络列表 : 

```bash
$ sudo docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
## ......
y89bt74ld9l8        mesh                overlay             swarm
## ......
```



