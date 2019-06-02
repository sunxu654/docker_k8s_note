>原创笔记  转载请先征求本人同意 

# docker_k8s_note

# docker的原理与使用

## 容器技术的发展史


### 传统的容器技术是虚拟机技术,它的虚拟化方式有两种

1. 在宿主机上虚拟出多个内核,在内核上分别创建虚拟机;
2. 直接在硬件资源上创建内核(跳过宿主机系统),在内核上创建虚拟机;


**这样会造成严重的资源浪费,哪怕只想运行一个web服务,都要虚拟出一个主机**


### 新的容器技术是利用命名空间,对内核进行虚拟化,减少了资源浪费

命名空间的分类如图:
![](https://i.imgur.com/GyorsNf.jpg)
因为用户命名空间在linux的支持版本较晚,也据定了centos6的内核版本不适合用docker,即使高版本内核可用也很不稳定;

因为虚拟机技术的资源浪费,由此诞生了第一种使用同一个内核,而不需要再分离出多个内核(节省资源)的容器技术Linux Container
LXC技术是利用自己创建的模板,对内核进行命名空间的划分,然后生成一个容器,在容器内运行多个进程,但这样有一个缺点是生成的容器内的数据很难迁移,模板生成也十分复杂;

所以就诞生了docker技术,docker技术的底层其实就是LXC,它其实是对LXC技术的封装;

### docker的一些优点

它相对于LXC的一大优点是可以很轻松的创建和自定义镜像,存储在云仓库中,在使用的时候拉取运行即可;

docker在**一个容器中只运行一个进程**,进程之间通过容器的方式进行通信,比如tomcat和Nginx;

**docker可以将容器打包,它不再依赖内核的命名空间,而是拥有自己独立的命名空间,就使得自己的运行环境完全独立于平台,就实现了分发镜像的极大便利;**

docker的联合挂载机制(极大的优点)
![](https://i.imgur.com/cggenCn.jpg)

### docker杂谈

google早在十年前就秘密使用了docker技术
后来docker公司为了盈利,发布了容器编排工具(实现容器的资源管理),但此时google也有对应的开源技术k8s公布于世,因为google丰富的容器使用经验,使得k8s碾压docker的容器编排工具

docker公司为了盈利,就把docker分为了社区版(更名moby)和企业版(docker) 为的往企业版docker引流,获取投资和利润;而相比之下,google成立CNCF委员会来脱离出google引导k8s的健康发展; 使得世界对k8s的支持呼声极高

docker在发展壮大起来后,开发了新的引擎libcontainer,抛弃了原来的LXC;又在社区版和企业版上吃相难看,其过河拆桥之行为与世界技术的发展背道而驰

容器技术的使用需要制定行业标准,CNCF完全有能力制定标准,而且为了保证不被docker一家公司控制,CNCF将docker排除在外,但是也给了docker一个改过自新的机会,让docker把自己开发的新引擎libcontainner更名为runC作为**容器技术引擎的标准**

## docker的基本使用

### docker的架构组成
![](https://i.imgur.com/bfIaGOg.jpg)

daemon:守护进程(后台进程)  用来拉取镜像和运行容器(容器是镜像的一个动态实例)
docker client : docker的客户终端
docker register: docker仓库 (用来存放镜像) + 用户认证功能


### 镜像仓库的存放准则

一个镜像仓库只存放一个应用程序的不同版本镜像,比如nginx的不同版本

如图所示:
![](https://i.imgur.com/ToQhqI0.jpg)

### docker安装杂记


清华镜像站里面可以查看镜像的内部文件
可以从centos7中发现,cent7的extras中带有一个老版本docker
![](https://i.imgur.com/7OcTbKR.jpg) 


也可以从清华镜像站中,找到docker-ce镜像:
![](https://i.imgur.com/3CUOmzz.jpg)

### docker 常用命令:

![](https://i.imgur.com/e0L05uf.jpg)

### 可以选择的镜像标签:

![](https://i.imgur.com/9q30KdP.jpg)

alpine:是轻量版,缺少测试工具,不建议使用

### docker的虚拟网络

![](https://i.imgur.com/mCmgWe9.jpg)
![](https://i.imgur.com/amxF17N.jpg)

### docker的基本使用

![](https://i.imgur.com/jBYZSbH.jpg )
### docker镜像的底层原理

![](https://i.imgur.com/ZfHdJ3R.jpg)
![](https://i.imgur.com/ZGruNMu.jpg)