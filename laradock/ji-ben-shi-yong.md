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



