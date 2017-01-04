# 简介

* Docker 最初是在 Ubuntu 12.04 上开发实现的
* Red Hat 则从 RHEL 6.5 开始对 Docker 进行支持

Docker 使用 Google 公司推出的 [Go 语言](https://golang.org/) 进行开发实现，基于 Linux 内核的 [cgroup](https://zh.wikipedia.org/wiki/Cgroups)，[namespace](https://en.wikipedia.org/wiki/Linux_namespaces)，以及 [AUFS](https://en.wikipedia.org/wiki/Aufs) 类的 [Union FS](https://en.wikipedia.org/wiki/Union_mount) 等技术，对进程进行封装隔离，属于[操作系统层面的虚拟化技术](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)。

最初实现是基于 [LXC](https://linuxcontainers.org/lxc/introduction/)，从 0.7 以后开始去除 LXC，转而使用自行开发的 [libcontainer](https://github.com/docker/libcontainer)，从 1.11 开始，则进一步演进为使用 [runC](http://runc.io/) 和 [containerd](https://containerd.tools/)。

### 优势

**更高效的利用系统资源**

容器不需要进行硬件虚拟以及运行完整操作系统等额外开销.

**更快速的启动时间**

Docker容器应用直接运行于数组内核,无需启动完整的操作系统.

**一致的运行环境**

Docker镜像提供了除内核外完整的运行时环境,确保了应用运行环境的一致性.

**持续交付和部署**

使用Docker通过定制应用镜像来实现持续集成,持续交付,部署.

* 开发人员可以通过Dockerfile来进行镜像构建,并结合持续集成系统进行集成测试.
* 运维人员可以直接在生产环境中快速部署该镜像,甚至可以结合持续部署系统进行自动部署.

**更轻松的迁移**

多平台的支持,Docker确保了执行环境的一致性,使得应用的迁移更加容易.

**更轻松的维护和扩展**

分层存储以及镜像技术,使得应用重复部分的复用更为容易,基于镜像进一步扩展也非常简单.官方维护的官方镜像,也可以在生产环境使用,还可以进一步定制,降低成本.

**对比传统虚拟机总结**

* 启动 - 秒级
* 硬盘使用 - MB
* 性能 - 接近原生
* 系统支持量 - 单机上千容器



