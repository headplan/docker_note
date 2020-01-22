# 安装Docker

> 内核升级部分不再记录 , 直接使用符合需求的Linux版本

```
uname -a # 检查内核版本
```

#### CentOS

```
$ sudo yum install yum-utils device-mapper-persistent-data lvm2
$
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
$ sudo yum install docker-ce
$
$ sudo systemctl enable docker # 开机自启动
$ sudo systemctl start docker
```

#### Debian

```
$ sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common
$
$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce
$
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

#### Fedora

```
$ sudo dnf -y install dnf-plugins-core
$
$ sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
$ sudo dnf install docker-ce
$
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

#### Ubuntu

```
$ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
$
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce
$
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

默认情况下 ,`docker` 命令会使用 [Unix socket](https://en.wikipedia.org/wiki/Unix_domain_socket) 与 Docker 引擎通讯 . 安全考虑应该创建docker用户组 .

```
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```

退出当前用户登陆状态 , 然后重新登录 , 以便让权限生效或重启docker-daemon

```
sudo systemctl restart docker
```

---

#### 上手使用

通过软件包的形式安装 Docker Engine 时 , 安装包已经为我们在 Linux 系统中注册了一个 Docker 服务 , 所以我们不需要直接启动 docker daemon 对应的 dockerd 这个程序 , 而是直接启动 Docker 服务即可 . 就是前面提到的命令 .

尝试几个命令 :

```
docker version # 查看Client和Server两端的版本信息
```

服务端和客户端通过RESTful接口进行了解耦 , 所以可以修改配置用于其他机器上运行服务端 .

```
docker info # 查看Docker Engine相关信息
```

可以看到正在运行的 Docker Engine 实例中运行的容器数量 , 存储的引擎等等 .

#### 配置国内镜像

因为Docker提供的中央镜像仓库太慢 , 这里配置使用Docker提供的国内镜像 , 国内镜像仓库也有很多其他的 , 比如 , DaoCloud和阿里云等 .

修改json文件 , 不存在直接创建 .

```
/etc/docker/daemon.json
```

```
{
    "registry-mirrors": [
        "https://mirror.ccs.tencentyun.com"
    ]
}
```

重启后配置生效

```
sudo systemctl restart docker
```

使用docker info查看Registry Mirrors是否存在 .

