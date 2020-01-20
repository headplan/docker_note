# 安装

### CentOS

#### 安装Docker

**卸载旧版**

```
sudo yum remove docker docker-client  docker-client-latest \
docker-common docker-latest docker-latest-logrotate  docker-logrotate docker-engine
```

**更新驱动**

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

**设置存储库**

```
sudo yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

**安装主程序**

```
sudo yum install docker-ce
```

**启动**

```
sudo systemctl start docker
```

**设置开机启动**

```
sudo systemctl enable docker
```

#### 安装docker-compose

**下载程序**

    curl -L https://github.com/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

**设置权限**

```
chmod +x /usr/local/bin/docker-compose
```

**检查运行**

```
docker-compose --version
```

**配置文件**

```
cd laradock
cp env-example .env
```



