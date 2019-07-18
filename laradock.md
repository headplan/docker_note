# Laradock

**官网** - [http://laradock.io/](http://laradock.io/)

**Github** - [https://github.com/laradock/laradock](https://github.com/laradock/laradock)

---

#### 介绍

LaraDock - 一个Docker驱动的完整的PHP开发环境 .

**快速启动**

```
# 克隆仓库
git clone https://github.com/Laradock/laradock.git
# 创建.env
cp env-example .env
# 启动容器
docker-compose up -d nginx mysql redis beanstalkd
# 编辑.env文件
DB_HOST=mysql
REDIS_HOST=redis
QUEUE_HOST=beanstalkd
# 访问
http://localhost
# bingo
```

#### 支持的软件

* **Web Servers\(Web服务器\):**
* * NGINX
  * Apache2
  * Caddy
* **Load Balancers\(负载均衡器\):**
  * HAProxy
  * Traefik
* **PHP Compilers\(PHP编译器\):**
  * PHP FPM
  * HHVM
* **Database Management Systems\(数据库管理系统\):**
  * MySQL
  * PostgreSQL
    * PostGIS
  * MariaDB
  * Percona
  * MSSQL
  * MongoDB
    * MongoDB Web UI
  * Neo4j
  * CouchDB
  * RethinkDB
* **Database Management Apps\(数据库管理应用\):**
  * PhpMyAdmin
  * Adminer
  * PgAdmin
* **Cache Engines\(缓存引擎\):**
  * Redis
    * Redis Web UI
    * Redis Cluster
  * Memcached
  * Aerospike
  * Varnish
* **Message Brokers\(消息队列\):**
  * RabbitMQ
    * RabbitMQ Admin Console
  * Beanstalkd
    * Beanstalkd Admin Console
  * Eclipse Mosquitto
  * PHP Worker
  * Laravel Horizon
* **Mail Servers\(邮件服务器\):**
  * Mailu
  * Mailhog
  * MailDev
* **Log Management:**
  * GrayLog
* **Testing:**
  * Selenium
* **Monitoring:**
  * Grafana
  * NetData
* **Search Engines:**
  * ElasticSearch
  * Apache Solr
  * Manticore Search
* **IDE’s**
  * ICE Coder
  * Theia
  * Web IDE
* **Miscellaneous:**
  * Workspace:
    _\(Laradock container that includes a rich set of pre-configured useful tools\)_
    * `PHP CLI`
    * `Composer`
    * `Git`
    * `Vim`
    * `xDebug`
    * `Linuxbrew`
    * `Node`
    * `V8JS`
    * `Gulp`
    * `SQLite`
    * `Laravel Envoy`
    * `Deployer`
    * `Yarn`
    * `SOAP`
    * `Drush`
    * `Wordpress CLI`
  * Apache ZooKeeper
    _\(Centralized service for distributed systems to a hierarchical key-value store\)_
  * Kibana
    _\(Visualize your Elasticsearch data and navigate the Elastic Stack\)_
  * LogStash
    _\(Server-side data processing pipeline that ingests data from a multitude of sources simultaneously\)_
  * Jenkins
    _\(automation server, that provides plugins to support building, deploying and automating any project\)_
  * Certbot
    _\(Automatically enable HTTPS on your website\)_
  * Swoole
    _\(Production-Grade Async programming Framework for PHP\)_
  * SonarQube
    _\(continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs and more\)_
  * Gitlab
    _\(A single application for the entire software development lifecycle\)_
  * PostGIS
    _\(Database extender for PostgreSQL. It adds support for geographic objects allowing location queries to be run in SQL\)_
  * Blackfire
    _\(Empowers all PHP developers and IT/Ops to continuously verify and improve their app’s performance\)_
  * Laravel Echo
    _\(Bring the power of WebSockets to your Laravel applications\)_
  * Phalcon
    _\(A PHP web framework based on the model–view–controller pattern\)_
  * Minio
    _\(Cloud storage server released under Apache License v2, compatible with Amazon S3\)_
  * AWS EB CLI
    _\(CLI that helps you deploy and manage your AWS Elastic Beanstalk applications and environments\)_
  * Thumbor
    _\(Photo thumbnail service\)_
  * IPython
    _\(Provides a rich architecture for interactive computing\)_
  * Jupyter Hub
    _\(Jupyter notebook for multiple users\)_
  * Portainer
    _\(Build and manage your Docker environments with ease\)_
  * Docker Registry
    _\(The Docker Registry implementation for storing and distributing Docker images\)_
  * Docker Web UI
    _\(A browser-based solution for browsing and modifying a private Docker registry\)_



