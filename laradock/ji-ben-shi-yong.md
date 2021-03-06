# 基本使用

#### 依赖

* Git
* Docker &gt;= 17.12

#### 安装

**单个项目的设置**

> 如果希望每个项目都有单独的Docker环境

a\) 已经有项目存在

```
git submodule add https://github.com/Laradock/laradock.git
# tree
project-a
-- laradock-a
project-b
-- laradock-b
```

b\) 还没有创建项目

```
git clone https://github.com/laradock/laradock.git
# tree
laradock
project-dev
```

**多个项目的设置**

> 如果希望每个项目都使用同一个Docker环境

```
laradock
project-1
project-2
# 添加nginx配置到NGINX_SITES_PATH文件夹中
# 方法和添加hosts一样,只要注意路径都是在APPLICATION下即可
```

#### 使用

**初始化laradock**

使用laradock的第一步肯定是要新建.env文件,配置laradock

```
cp env-example .env
```

可以直接编辑.env文件 , 配置应用 , 这些env中的变量都会在docker-compose.yml文件中使用 .

根据操作系统不同或项目需求不同 , 还需要配置env的一些值 :

    ###########################################################
    ###################### General Setup ######################
    ###########################################################

    ### Paths #################################################

    # Point to the path of your applications code on your host
    # 指向主机上应用程序代码的路径,本地代码路径,这里的意思是laradock的上级目录
    APP_CODE_PATH_HOST=../

    # Point to where the `APP_CODE_PATH_HOST` should be in the container
    # 即代码目录与容器中/var/www的映射目录
    APP_CODE_PATH_CONTAINER=/var/www

    # You may add flags to the path `:cached`, `:delegated`. When using Docker Sync add `:nocopy`
    APP_CODE_CONTAINER_FLAG=:cached

    # Choose storage path on your machine. For all storage systems
    DATA_PATH_HOST=~/.laradock/data

    ### Drivers ################################################

    # All volumes driver
    # 所有的数据卷驱动:本地
    VOLUMES_DRIVER=local

    # All Networks driver
    # 所有的网络驱动:本地
    NETWORKS_DRIVER=bridge

    ### Docker compose files ##################################

    # Select which docker-compose files to include. If using docker-sync append `:docker-compose.sync.yml` at the end
    COMPOSE_FILE=docker-compose.yml

    # Change the separator from : to ; on Windows
    # Windows系统需要修改分隔符为;分号
    COMPOSE_PATH_SEPARATOR=:

    # Define the prefix of container names. This is useful if you have multiple projects that use laradock to have seperate containers per project.
    # 定义容器名称前缀,如果跑了多个laradock的话,这个很有用
    COMPOSE_PROJECT_NAME=laradock

    ### PHP Version ###########################################

    # Select a PHP version of the Workspace and PHP-FPM containers (Does not apply to HHVM). Accepted values: 7.3 - 7.2 - 7.1 - 7.0 - 5.6
    # 选择工作区和PHP-FPM容器的PHP版本,不包括HHVM的.支持7.3,7.2,7.1,7.0,5.6
    PHP_VERSION=7.2

    ### Phalcon Version ###########################################

    # Select a Phalcon version of the Workspace and PHP-FPM containers (Does not apply to HHVM). Accepted values: 3.4.0+
    # 选择工作区和PHP-FPM容器的Phalcon版本,不包括HHVM的.支持3.4.0+
    PHALCON_VERSION=3.4.1

    ### PHP Interpreter #######################################

    # Select the PHP Interpreter. Accepted values: hhvm - php-fpm
    # 选择PHP解释器.支持hhvm,php-fpm
    PHP_INTERPRETER=php-fpm

    ### Docker Host IP ########################################

    # Enter your Docker Host IP (will be appended to /etc/hosts). Default is `10.0.75.1`
    # 设置Docker主机的IP(会追加到/etc/hosts).默认10.0.75.1
    DOCKER_HOST_IP=10.0.75.1

    ### Remote Interpreter ####################################

    # Choose a Remote Interpreter entry matching name. Default is `laradock`
    # 选择与名称匹配的远程解释器.默认laradock
    PHP_IDE_CONFIG=serverName=laradock

    ### Windows Path ##########################################

    # A fix for Windows users, to ensure the application path works
    # Windows用户的修复程序,用来确保应用程序路径正常工作
    COMPOSE_CONVERT_WINDOWS_PATHS=1

    ### Environment ###########################################

    # If you need to change the sources (i.e. to China), set CHANGE_SOURCE to true
    # 如果需要更改来源(即,中国),请设置为true
    CHANGE_SOURCE=false

    ### Docker Sync ###########################################

    # If you are using Docker Sync. For `osx` use 'native_osx', for `windows` use 'unison', for `linux` docker-sync is not required
    # 如果使用的是Docker Sync.OSX用native_osx,Windows用unison,Linux不变
    DOCKER_SYNC_STRATEGY=native_osx

**执行命令创建环境**

```
docker-compose up -d nginx mysql redis
```



