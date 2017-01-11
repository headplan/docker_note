# CentOS和MacOS安装Docker

### 系统要求

Docker 最低支持 CentOS 7,64位平台,内核不低于3.10.

> 内核较低部分功能将无法使用,例如overlay2存储层驱动

### 使用脚本自动安装

##### 官方脚本

```
curl -sSL https://get.docker.com/ | sh
```

##### 墙内脚本

```
curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -
```

##### DaoCloud脚本\(也是墙内的\)

```
curl -sSL https://get.daocloud.io/docker | sh
```

### 手动安装

**添加内核参数和yum源**

如果出现下列警告

```
WARNING: bridge-nf-call-iptables is disabled
WARNING: bridge-nf-call-ip6tables is disabled
```

添加内核配置参数启用这些功能,然后重新加载即可.

```
$ sudo tee -a /etc/sysctl.conf <<-EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

# 重新加载
$ sudo sysctl -p
```

添加yum源,因为CentOS自带的比较陈旧,所以重新添加yum源

```
$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
```

### 开始手动安装Docker

更新yum软件源缓存,并安装docker-engine

```
$ sudo yum update
$ sudo yum install docker-engine
```

##### 启动Docker引擎

```
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

##### 建立docker用户组

默认情况下,`docker` 命令会使用 [Unix socket](https://en.wikipedia.org/wiki/Unix_domain_socket) 与 Docker 引擎通讯.安全考虑应该创建docker用户组

```
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```

#### 参考文档

参见 [Docker 官方 CentOS 安装文档](https://docs.docker.com/engine/installation/linux/centos/)

---

### 系统要求

最低需求MacOS10.10.3 Yosemite,或者2010年以后的机型,带Intel MMU虚拟化的.系统不满足,可以考虑Docker Toolbox.

#### 使用Homebrew安装

```
brew cask install docker
```

#### 手动下载安装

链接下载：

[https://download.docker.com/mac/stable/Docker.dmg](https://download.docker.com/mac/stable/Docker.dmg)

##### 运行

运行和正常运行Mac工具一样.

> _在国内使用 Docker 的话，需要配置加速器，在菜单中点击_`Preferences...`_，然后查看_`Advanced`_标签，在其中的_`Registry mirrors`_部分里可以点击加号来添加加速器地址。_

**查看Docker版本**

```
$ docker --version
Docker version 1.12.3, build 6b644ec
$ docker-compose --version
docker-compose version 1.8.1, build 878cff1
$ docker-machine --version
docker-machine version 0.8.2, build e18a919
```

如果`docker version`,`docker info`都正常的话,可以运行一个Nginx服务器

```
$ docker run -d -p 80:80 --name webserver nginx
```

服务运行后,可以访问[http://localhost](http://localhost),如果看到了 "Welcome to nginx!",就说明 Docker for Mac 安装成功了.

要停止Nginx服务器并删除执行下面的命令

```
$ docker stop webserver
$ docker rm webserver
```



