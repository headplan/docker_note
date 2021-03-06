# 搭建运行环境

#### Docker Engine 的版本

对于 Docker Engine 来说 , 其主要分为两个系列 :

* 社区版 \( CE, Community Edition \)
* 企业版 \( EE, Enterprise Edition \)

社区版 \( Docker Engine CE \) 主要提供了 Docker 中的容器管理等基础功能 , 主要针对开发者和小型团队进行开发和试验 . 而企业版 \( Docker Engine EE \) 则在社区版的基础上增加了诸如容器管理、镜像管理、插件、安全等额外服务与功能 , 为容器的稳定运行提供了支持 , 适合于中大型项目的线上运行 .

社区版和企业版的另一区别就是免费与收费了 . 对于我们开发者来说 , 社区版已经提供了 Docker 所有核心的功能 , 足够满足我们在开发、测试中的需求 , 所以我们直接选择使用社区版进行开发即可 .

Docker Engine的迭代版本分为稳定版和预览版 , 以发布的年月来命名版本号 . 稳定版固定为没三个月更新一次 , 预览版则每月更新 . 主要版本之外 , 以解决Bug为主要目的次要版本不定期发布 , 以主版本和发布需要组成 , 例如 : Docker version 18.09.0

#### Docker的依赖环境

基于 Linux kernel 3.10 以上版本的 Linux 系统来安装 Docker .

| 操作系统 | 支持的系统版本 |
| :--- | :--- |
| CentOS | CentOS 7 |
| Debian | Debian Wheezy 7.7 \(LTS\) |
|  | Debian Jessie 8 \(LTS\) |
|  | Debian Stretch 9 |
|  | Debian Buster 10 |
| Fedora | Fedora 26 |
|  | Fedora 27 |
| Ubuntu | Ubuntu Trusty 14.04 |
|  | Ubuntu Xenial 16.04 |
|  | Ubuntu Artful 17.10 |



