# 

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



