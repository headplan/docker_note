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



