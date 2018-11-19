# 基本概念

Docker的四大组成对象 :

* 镜像\(Image\)
* 容器\(Container\)
* 网络\(Network\)
* 数据卷\(Volume\)

### **Docker镜像**

所谓镜像 , 可以理解为一个只读的文件包 , 其中包含了**虚拟环境运行最原始文件系统的内容** . 

操作系统分为内核和用户空间 . Linux内核启动后,会挂载root文件系统为其提供用户空间支持 . Docker镜像就相当于是一个root文件系统 . 但是Docker镜像比较特殊 , 除了提供容器运行所需程序 , 库 , 资源 , 配置等文件 , 还包含了一些为运行时准备的配置参数 , 例如匿名卷 , 环境变量 , 用户等等 . 不包含任何动态数据 , 构建之后不会改变 . 

#### 分层存储概念

镜像包含了操作系统完整的root文件系统,但并非像是一个ISO那样的打包文件,实际并非由一个文件组成,而是由一组文件组成,或者说是由多层文件系统联合组成.\(充分利用了Union FS技术\)

镜像构建时,会一层层构建,前一层是后一层的基础.每一层构建完就不会再发生改变,后一层上的任何改变只发生在自己这一层.

> 比如,删除前一层文件的操作,实际不是真的删除前一层的文件,而是仅在当前层标记为该文件已经删除.在容器运行时,是看不到到该文件的,但实际上类似隐藏掉了该文件.

分层存储的特征还使得镜像的复用,定制变的更为容易.甚至可以用之前构建好的镜像作为基础层,然后一步步的添加新的层,定制自己需要的内容,构建新的镜像.

### Docker容器

镜像与容器的关系,就像是面向对象程序设计中类与实力的关系一样.镜像是静态的定义,容器是镜像运行时的实体.容器实体可以被创建,启动,停止,删除或暂停等.

容器的实质是进程,但与直接在宿主执行的进程不同,容器进程运行于属于自己的独立的[命名空间](https://en.wikipedia.org/wiki/Linux_namespaces).因此容器可以拥有自己的`root`文件系统、自己的网络配置、自己的进程空间,甚至自己的用户ID空间.容器内的进程是运行在一个隔离的环境里,使用起来,就好像是在一个独立于宿主的系统下操作一样,但是容器并非虚拟机.

容器同样适用了分层存储技术,每一个容器运行时,是以镜像为基础层,在其上创建一个为当前容器运行时读写而准备的存储层,**容器存储层**.容器存储层随容器消亡而消亡.所以不应该向其写入任何数据,保持无状态化.

> 所有文件的写入操作,都应该使用数据卷或绑定宿主目录,跳过容器存储层.

### Docker仓库

仓库的概念和Git的仓库概念相似,Docker Registry就是一个集中的存储,分发镜像的服务.一个Docker Registry中包含多个仓库,每个仓库包含多个标签\(Tag\),每个标签对应一个镜像.

> 可以通过&lt;仓库名&gt;:&lt;标签&gt;的格式来指定具体版本镜像.

#### Docker Registry公开服务

类似Github服务.

官方公开服务:[https://hub.docker.com/](https://hub.docker.com/)

CoreOS:[https://quay.io/](https://quay.io/)

> CoreOS是一个基于Linux内核的支持Docker的发行版系统,但是后来CoreOS又发布了自己的容器引擎Rocket

Google:[https://cloud.google.com/container-registry/](https://cloud.google.com/container-registry/)

> [http://kubernetes.io/的镜像使用的就是这个服务](http://kubernetes.io/的镜像使用的就是这个服务)

##### Docker Hub的镜像加速器

阿里云加速器:[https://cr.console.aliyun.com/\#/accelerator](https://cr.console.aliyun.com/#/accelerator)

DaoCloud加速器:[https://www.daocloud.io/mirror\#accelerator-doc](https://www.daocloud.io/mirror#accelerator-doc)

灵雀云加速器:[http://docs.alauda.cn/feature/accelerator.html](http://docs.alauda.cn/feature/accelerator.html)

##### 国内Docker Registry公开服务

时速云镜像仓库:[https://hub.tenxcloud.com/](https://hub.tenxcloud.com/)

网易云镜像服务:[https://c.163.com/hub\#/m/library/](https://c.163.com/hub#/m/library/)

DaoCloud镜像市场:[https://hub.daocloud.io/](https://hub.daocloud.io/)

阿里云镜像库:[https://cr.console.aliyun.com](https://cr.console.aliyun.com)

#### 私有Docker Registry

官方提供的Docker Registry镜像,可以直接使用作为私有的Registry服务:

[https://hub.docker.com/\_/registry/](https://hub.docker.com/_/registry/)

镜像API只提供了服务器端实现,但足以支持Docker命令,不影响使用.其他的图形界面,镜像维护,用户管理等高级功能由官方的商业化版本提供:

[https://docs.docker.com/datacenter/dtr/2.0/](https://docs.docker.com/datacenter/dtr/2.0/)

当然还有第三方软件实现了Docker Registry API,提供了一些用户界面和高级功能:

[http://vmware.github.io/harbor/index\_cn.html](http://vmware.github.io/harbor/index_cn.html)

[https://www.sonatype.com/docker](https://www.sonatype.com/docker)

