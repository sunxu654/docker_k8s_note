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

#### 一个镜像仓库只存放一个应用程序的不同版本镜像,比如nginx的不同版本
![](https://i.imgur.com/ToQhqI0.jpg)

### docker安装杂记


####清华镜像站里面可以查看镜像的内部文件 可以从centos7中发现,cent7的extras中带有一个老版本docker
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

## docker 制作镜像

![](https://i.imgur.com/lb6uT07.jpg)

### 基于容器制作镜像

```linux
[root@sx ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                    NAMES
27c586590860        centos              "/bin/bash"              About a minute ago   Up 59 seconds                                cent7
2685adbf571c        redis               "docker-entrypoint.s…"   5 hours ago          Up 5 hours          0.0.0.0:6379->6379/tcp   redis63
[root@sx ~]# docker commit cent7 sunxuxu/mycentos7:v1
sha256:fbf50e3ebc88857d42f3ee8a8c455f05e5e8526595d295f5c94f3f86d5f6add5
[root@sx ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sunxuxu/mycentos7   v1                  fbf50e3ebc88        15 seconds ago      202MB
rabbitmq            management          476495614692        11 days ago         194MB
redis               alpine              72e76053ebb7        2 weeks ago         50.9MB
redis               latest              d3e3588af517        2 weeks ago         95MB
centos              latest              9f38484d220f        2 months ago        202MB
registry            latest              f32a97de94e1        2 months ago        25.8MB
hello-world         latest              fce289e99eb9        5 months ago        1.84kB
[root@sx ~]# docker tag fbf50e3ebc88 sunxu/mycent:v2
[root@sx ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sunxuxu/mycentos7   v1                  fbf50e3ebc88        54 seconds ago      202MB
sunxu/mycent        v2                  fbf50e3ebc88        54 seconds ago      202MB
rabbitmq            management          476495614692        11 days ago         194MB
redis               alpine              72e76053ebb7        2 weeks ago         50.9MB
redis               latest              d3e3588af517        2 weeks ago         95MB
centos              latest              9f38484d220f        2 months ago        202MB
registry            latest              f32a97de94e1        2 months ago        25.8MB
hello-world         latest              fce289e99eb9        5 months ago        1.84kB
```
### docker镜像与TAG的硬链接原理

![](https://i.imgur.com/W5G8f3P.jpg)

### 将镜像push到仓库中
#### push的 账号/镜像名:TAG   应该与账号和远程仓库名对应
![](https://i.imgur.com/BaJwVuM.jpg)

### 镜像的导入导出

#### 跨过pull 镜像,在两台服务器间传递打包的文件(不常用)

![](https://i.imgur.com/i2m8Ue3.jpg)


## 容器的虚拟网络模型
#### 容器的四种虚拟网络模型

![](https://i.imgur.com/xv2oaXd.jpg)

### bridge模型
![](https://i.imgur.com/TCnBBzn.jpg)

#### 容器的虚拟网卡与docker0相连


![](https://i.imgur.com/4ruwDP8.jpg)

#### 可以看到有三个容器的网卡与docker0相连
![](https://i.imgur.com/0wu5Llz.jpg)
![](https://i.imgur.com/B0KgT18.jpg)
```
docker run -it --name containername  --network bridge  -h sunxu.com --dns 8.8.8.8 --rm image:tag
#network  指定网络模式 还有none host
#-h 指定docker容器的主机名
#dns 指定docker容器的dns服务器

#上面的参数可以在docker容器的 /etc/resolv.conf中找到
```
### docker容器桥接模式暴露端口
docker run -it --name test -p 192.168.30.70:5432:5432 redis
![](https://i.imgur.com/V11fPXQ.jpg)
![](https://i.imgur.com/rOamxv6.jpg)
#### 修改docker0(docker daemon)的ip地址
```
vim /etc/docker/daemon.json

{
"bip":"10.0.0.1/16"
}


systemctl restart docker.service

[root@sx ~]# ifconfig
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 10.0.0.1  netmask 255.255.0.0  broadcast 10.0.255.255
        ether 02:42:d8:a7:41:03  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.245.4  netmask 255.255.255.0  broadcast 192.168.245.255
        inet6 fe80::d4f2:8d5f:bf2:dbf0  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:90:e6:8d  txqueuelen 1000  (Ethernet)
        RX packets 452  bytes 43605 (42.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 317  bytes 50079 (48.9 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0



```


### docker容器共享模式
#### 容器间共享ip
```
开启container1
docker run -it --name net1 --rm busybox
开启container2 并把ip挂到net1上面
docker run -it --name net2 --network  container:net1 --rm busybox
```

#### 宿主机与容器共享ip
```
docker run -it --name net3 --network host --rm busybox
```
在network中  和 在宿主机中的 ifconfig内容相同
代表连接到了同一块网卡上
**这种方式比桥接暴露端口的好处是可以减少一次nat转换,提高访问效率**

