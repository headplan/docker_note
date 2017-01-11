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

