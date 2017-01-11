# Docker安装

## Ubuntu、Debian 系列安装 Docker

### 系统要求

Docker支持以下版本的Ubuntu和Debian操作系统

* Ubuntu Xenial 16.04 \(LTS\)
* Ubuntu Trusty 14.04 \(LTS\)
* Ubuntu Precise 12.04 \(LTS\)
* Debian testing stretch \(64-bit\)
* Debian 8 Jessie \(64-bit\)
* Debian 7 Wheezy \(64-bit\)（_必须启用 backports_\)

Ubuntu推荐使用14.04 LTS 或更高的版本.

> Docker 需要安装在 64 位的 x86 平台或 ARM 平台上（如[树莓派](https://www.raspberrypi.org/)），并且要求内核版本不低于 3.10。但实际上内核越新越好，过低的内核版本可能会出现部分功能无法使用，或者不稳定。

```
uname -a # 检查内核版本
```

### 升级内核

```
# Ubuntu 12.04 LTS
sudo apt-get install -y --install-recommends linux-generic-lts-trusty

# Ubuntu 14.04 LTS
sudo apt-get install -y --install-recommends linux-generic-lts-xenial

# Debian 7 Wheezy
# 默认内核3.2,为了满足Docker的需求,应该安装backports的内核
# 添加backports源
$ echo "deb http://http.debian.net/debian wheezy-backports main" | sudo tee /etc/apt/sources.list.d/backports.list
# 升级到backports内核
$ sudo apt-get update
$ sudo apt-get -t wheezy-backports install linux-image-amd64

# Debian 8 Jessie
# 默认内核3.16
# 添加backports源
$ echo "deb http://http.debian.net/debian jessie-backports main" | sudo tee /etc/apt/sources.list.d/backports.list
# 升级到backports内核
$ sudo apt-get update
$ sudo apt-get -t jessie-backports install linux-image-amd64
```

> 需要注意的是，升级到 `backports` 的内核之后，会因为 `AUFS` 内核模块不可用，而使用默认的 `devicemapper` 驱动，并且配置为 `loop-lvm`，这是不推荐的。因此，不要忘记安装 Docker 后，配置 `overlay2` 存储层驱动。

##### 配置GRUB引导参数

出现如下警告

```
WARNING: Your kernel does not support cgroup swap limit. WARNING: Your
kernel does not support swap limit capabilities. Limitation discarded.

# 或

WARNING: No memory limit support
WARNING: No swap limit support
WARNING: No oom kill disable support
```

如果需要这些功能,就需要修改 GRUB 的配置文件 `/etc/default/grub`,在 `GRUB_CMDLINE_LINUX` 中添加内核引导参数 `cgroup_enable=memory swapaccount=1`.

然后不要忘记了更新 GRUB

```
$ sudo update-grub
$ sudo reboot
```

### 使用脚本自动安装

Ubuntu和Debian系统可以使用脚本安装

```
curl -sSL https://get.docker.com/ | sh
```

墙内脚本

```
curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -
```

DaoCloud的安装脚本

```
curl -sSL https://get.daocloud.io/docker | sh
```

### 手动安装

##### 软件包依赖

Ubuntu14.04将部分内核模块移动到了可选内核包\(linux-image-extra-\*\),AUFS就是.Docker存储驱动建议安装

```
$ sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
```

##### 12.04 LTS 图形界面

需要一些额外的软件包

```
$ sudo apt-get install xserver-xorg-lts-trusty libgl1-mesa-glx-lts-trusty
```

### 添加 APT 镜像源

系统中的软件源版本太旧.应使用官方源\(国内源一般是HTTP,不用安装传输软件和CA证书\)

```
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates
```

官方源GPG密钥\(确认下载软件包合法性\)

```
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
```

不同版本对应源

| 操作系统版本 | REPO |
| --- | --- |
| Precise 12.04 \(LTS\) | `deb https://apt.dockerproject.org/repo ubuntu-precise main` |
| Trusty 14.04 \(LTS\) | `deb https://apt.dockerproject.org/repo ubuntu-trusty main` |
| Xenial 16.04 \(LTS\) | `deb https://apt.dockerproject.org/repo ubuntu-xenial main` |
| Debian 7 Wheezy | `deb https://apt.dockerproject.org/repo debian-wheezy main` |
| Debian 8 Jessie | `deb https://apt.dockerproject.org/repo debian-jessie main` |
| Debian Stretch/Sid | `deb https://apt.dockerproject.org/repo debian-stretch main` |

添加源,更新软件包缓存

```
$ echo "<REPO>" | sudo tee /etc/apt/sources.list.d/docker.list
$ sudo apt-get update
```

### 开始手动安装Docker

软件包名称 : docker-engine

```
$ sudo apt-get install docker-engine
```

> 如果存在旧版Docker,例如lxc-docker,docker.io.会提示是否先删除,选择是即可.

##### 启动Docker引擎

##### Ubuntu 12.04/14.04、Debian 7 Wheezy

```bash
$ sudo service docker start
```

##### Ubuntu 16.04、Debian 8 Jessie/Stretch

```bash
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

##### 建立Docker用户组

默认情况下,`docker` 命令会使用 [Unix socket](https://en.wikipedia.org/wiki/Unix_domain_socket) 与 Docker 引擎通讯.安全考虑应该创建docker用户组

```
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```

#### 参考文档

* [Docker 官方 Ubuntu 安装文档](https://docs.docker.com/engine/installation/linux/ubuntulinux/)
* [Docker 官方 Debian 安装文档](https://docs.docker.com/engine/installation/linux/debian/)



