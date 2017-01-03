# 简介

* Docker 最初是在 Ubuntu 12.04 上开发实现的
* Red Hat 则从 RHEL 6.5 开始对 Docker 进行支持

Docker 使用 Google 公司推出的 [Go 语言](https://golang.org/) 进行开发实现，基于 Linux 内核的 [cgroup](https://zh.wikipedia.org/wiki/Cgroups)，[namespace](https://en.wikipedia.org/wiki/Linux_namespaces)，以及 [AUFS](https://en.wikipedia.org/wiki/Aufs) 类的 [Union FS](https://en.wikipedia.org/wiki/Union_mount) 等技术，对进程进行封装隔离，属于[操作系统层面的虚拟化技术](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)。

最初实现是基于 [LXC](https://linuxcontainers.org/lxc/introduction/)，从 0.7 以后开始去除 LXC，转而使用自行开发的 [libcontainer](https://github.com/docker/libcontainer)，从 1.11 开始，则进一步演进为使用 [runC](http://runc.io/) 和 [containerd](https://containerd.tools/)。



