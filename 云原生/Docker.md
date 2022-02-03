## Docker

### 一、基础知识

前提知识：熟悉 Linux 和 git 操作



Docker 为什么会出现？

**答：**Docker 解决了开发人员和运维人员协调不一致的问题，就是解决了 ”在我这行，但是在你那不行“ 的问题

开发人员利用 Docker 可以来消除协作编码时 ”在我的机器上可正常工作“ 的问题，就是将代码和环境一起打包成一个镜像



Docker 理念：一次封装，到处运行

Docker 就是解决了运行环境和配置问题的软件容器，方便做持续集成并有助于整体发布的容器虚拟化技术



虚拟机就是带环境安装的一种解决方案

它可以在一种操作系统里面运行另外一种操作系统，比如 Windows 底下运行 Linux  系统；应用程序对此毫无感知，因为虚拟机看上去就和真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要就删除掉，对其他部分毫无影响；这类虚拟机完美的运行了另外一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变

虚拟机缺点：占用资源多，步骤多，启动慢



1.Docker 为什么比 VM虚拟机快?

**答：**1.Docker 有着比虚拟机更少的抽象层，由于 docker 不需要 Hpyervisor 实现硬件资源的虚拟化，运行在 docker容器 上的程序直接使用的都是实际物理机的硬件资源。因此在 CPU，内存利用率上 docker 会在效率上有明显优势

2.Docker 利用的是虚拟机的内核，VM 利用的是 Guest OS，所以说新建一个容器的时候，Docker 不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载 Guest OS，分钟级别，而 docker 直接利用的是宿主机的操作系统



查看内核版本：uname  -r

Docker 三大组成：镜像 (image)，容器 (container)，仓库 (repository)

1、Docker 镜像就是一个只读的模板，镜像可以用来创建 Docker 容器，一个镜像 可以创建很多容器

2、Docker 利用容器 (container) 独立运行一个或者一组应用，容器是用镜像创建的运行实例，它可以被启动、开始、停止、删除；每个容器都是相互隔离的、保证安全的平台

可以把容器看作是一个简化版的 Linux 环境和运行在其中的应用程序，容器的定义几乎和镜像一摸一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的







### 二、Docker容器命令

##### 1.启动，重启和暂停 docker

```bash
systemctl start docker		#启动docker服务
systemctl restart docket 或者 service docker restart 	#重启docker服务
systemctl stop docker 或者 service docker stop		#关闭docker服务

docker restart [容器ID]	#启动容器
docker ps -l			 #显示最近创建的容器
docker attach [容器ID]	#重新进入已启动的容器  
```



##### 2.运行容器

**有镜像才能创建容器，这是根本前提**

```bash
docker run [options] IMAGE [command] [ARG...]  # (这个command一般不写，因为镜像一般有默认命令)
```

options 参数：有些是一个减号，有些是两个减号

--name = "容器新名字"：为容器指定一个名称

-d：后台运行容器ID，也即启动守护式容器

-i：以交互模式运行容器，通常与 -t 同时使用		-t：为容器重新分配一个伪输入终端，通常与 i 同时使用

-P：随机端口映射

-p：指定端口映射，有以下四种模式	ip:hostPort:containerPort	ip::containerPort	hostPort:containerPort	containerPort

```bash
docker run -d -it -p 主机端口:容器端口 --name="mytomcat" tomcat	 #运行Docker里面的tomcat，并重命名为mytomcat，指定为后台运行，不在前台输出日志
```

docker rm(i)		加上 i 代表删除镜像（image），不加代表删除容器



查看 docker 里面正在运行的容器

```bash
docker ps [options]
```

options参数：-a 列出当前所有正在运行的容器 + 历史上运行过的

-l 显示最近创建的容器	-n  显示最近 n 个创建的容器	-q 静默模式	-no-trunc 不截断输出



退出容器：

```bash
exit  			#容器停止退出
Ctrl + P + Q	#容器不停止退出
```



启动容器：docker start [容器ID或容器名]		重启容器 restart		停止容器 stop 	删除容器 rm 

强制停止容器 docker kill [容器ID]

启动守护式容器：docker  run  -d  [容器ID]，注意守护式容器**没有后台执行命令的话那么执行完就关闭了**，所以可能 docker ps 查不出来后台有容器在运行

```bash
docker run -d --name "myUbuntu" ubuntu 	#由于容器在后台启动成功后，执行 COMMAND 命令就直接关闭了
docker ps								#所以 docker ps 查不出来信息

docker run -d --name "myUbuntu" ubuntu tail -f /dev/null	#在docker run -d 后增加一个驻留在进程中长期运行的命令就可以保证容器不关闭了
```



查看容器日志：docker logs -f -t --tail [容器ID]

-t 是加入时间戳		-f 是跟随最新的日志打印		--tail 数字：显示最后多少条



进入当前正在运行的容器：exec 在宿主机外面就可以与容器进行交互

从容器内拷贝文件到宿主机上：docker  cp  [容器ID]:[容器内路径]	 [宿主机路径]



**docker run 和 docker exec 以及 docker attach 的区别**

docker run：创建和启动一个新的容器实例，操作对象是镜像，选项较多，如果你要创建和启动一个容器，只能用 run；

docker exec：在已运行的容器中，执行命令，操作对象是容器，如果你要进入已运行的容器，并且执行命令，用 exec；**进入之后是一个新的终端**

```bash
docker exec -it [imageID] /bin/bash		//"/bin/bash"会进入容器
docker exec -it [imageID] ip addr		//不会进入容器
```

docker attach：同样操作的是已运行的容器，可以将本机标准输入（键盘输入）输到容器中，也可以将容器的输出显示在本机的屏幕上，如果你想查看容器运行过程中产生的标准输入输出，用 attach；**进入容器正在运行的终端，不会启动一个新的终端**

```bash
docker attach [imageId]
```



docker commit 提交容器副本使之成为一个新的镜像

docker commit  -m="提交的描述信息"  -a = "作者"  容器ID  要创建的目标镜像名:[标签名]

```bash
docker commit -m "tomcat without docs" -a "Chao" [容器ID] chao:1.2	#这个1.2就是TAG标签名, 这个标签前面的仓库名一定要小写
```



##### 3.docker 网络

Docker 网络技术  ( 容器互联 )

1.每启动一个容器, docker 会给 docker 容器分配一个 ip, 我们只要安装了 docker, 就会有一个网卡 docker01

所有的容器在不指定网路的情况下,都是 docker0 路由的, docker 会为我们的容器分配一个默认的可用 IP

Docker 中的所有的网络接口都是虚拟的, 虚拟的转发效率高!

只要容器删除, 对应网桥对就没了



网络模式:

bridge: 桥接 docker (默认的网络模式)

none: 不配置网络

host: 和主机共享网络

container: 容器网络联通 (不建议使用)

```bash
# 我们直接启动的命令, --net bridge, 这个就是 docker0
docker run -it -d --name "busybox-01" busybox
docker run -it -d --name "busybox-01" --network bridge busybox

# 创建一个自定义网络,网络名字为 "mynet"
# --driver bridge 表示创建新的bridge网络
# --subnet 192.168.0.0/16
# --gateway 192.168.0.1
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet

# 将容器置于创建的子网中
docker run -itd --name "busybox-01" --network mynet busybox
docker run -itd --name "busybox-02" --network mynet busybox
```



除了 [none, host](http://mp.weixin.qq.com/s?__biz=MzIwMTM5MjUwMg==&mid=2653587677&idx=1&sn=f4b65107a7f19a6530014bf49a04c7d1&chksm=8d3080c4ba4709d21902b08986585bf42d79c0b8bfb771276292a2d46ab02dd955ca9bc29f44&scene=21#wechat_redirect), [bridge](http://mp.weixin.qq.com/s?__biz=MzIwMTM5MjUwMg==&mid=2653587681&idx=1&sn=de0a60f143d4d8a1f814ce61027acff0&chksm=8d3080f8ba4709eebcf2887dfbd4efeae1e6e120e2c2a8c55740071c51e6a52be61f4a280323&scene=21#wechat_redirect) 这三个自动创建的网络，用户也可以根据业务需要创建 user-defined 网络。

Docker 提供三种 user-defined 网络驱动：bridge, overlay 和 macvlan；overlay 和 macvlan 用于创建跨主机的网络



**1.容器网络怎么和外界通信**

1、busybox 发送 ping 包：172.17.0.2 > www.bing.com。

2、docker0 收到包，发现是发送到外网的，交给 NAT 处理。

3、NAT 将源地址换成 enp0s3 (主机host的网卡) 的 IP：10.0.2.15 > www.bing.com。

4、ping 包从 enp0s3 发送出去，到达 www.bing.com。

通过 NAT，docker 实现了容器对外网的访问 

<img src="https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211209145317336.png" alt="image-20211209145317336" style="zoom: 67%;" /> 



**2.外界怎么和容器内部网络通信**

使用端口映射

docker 可将容器对外提供服务的端口映射到 host 的某个端口，外网通过该端口访问容器。容器启动时通过`-p`参数映射端口

```bash
docker run -it -p 8080:80 httpd   #将容器的80端口映射到本机的8080
```

每一个映射的端口，host 都会启动一个 `docker-proxy` 进程来处理访问容器的流量

以 0.0.0.0:32773->80/tcp 为例分析整个过程如下：

<img src="https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211207093809443.png" alt="image-20211207093809443" style="zoom: 50%;" /> 

1.docker-proxy 监听 host 的 32773 端口。

2.当 curl 访问 10.0.2.15:32773 时，docker-proxy 转发给容器 172.17.0.2:80。

3.httpd 容器响应请求并返回结果。





Docker 网络技术  ( 容器互联 )

1.每启动一个容器, docker 会给 docker 容器分配一个 ip, 我们只要安装了 docker, 就会有一个网卡 docker01

所有的容器在不指定网路的情况下,都是 docker0 路由的, docker 会为我们的容器分配一个默认的可用 IP

Docker 中的所有的网络接口都是虚拟的, 虚拟的转发效率高!

只要容器删除, 对应网桥对就没了



### 三、镜像原理

##### 1.镜像原理

镜像就是一种轻量级、可执行的软件包，用来打包软件运行环境和基于运行环境开发的软件，它**包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件**

UnionFS（联合文件系统）：Union 文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交**来一层层地叠加**，同时可以将不同目录挂载到同一个虚拟文件系统下（unite several directories into a single virtual filesystem）Union 文件系统是 Docker 镜像的基础，镜像可以通过分层来进行继承，基于基础镜像，可以制作各种应用镜像

特征：一次同时加载多个文件系统，但从外面来看，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录



**Docker 镜像加载原理：**

docker 的镜像实际上由一层一层的文件系统组成，这种层级的文件系统 UnionFS

bootfs (boot file system) 主要包含 bootloader 和 kernel，bootloader 主要是引导加载 kernel，Linux 刚启动时会加载 bootfs 文件系统，在 Docker 镜像的最底层是 bootfs；这一层与我们典型的 Linux/Unix 系统是一样的，包含 boot 加载器和内核；当 boot 加载完成之后整个内核就都在内存中，此时内存的使用权已由 bootfs 转交给内核，此时系统也会卸载 bootfs

rootfs（root file system），在 bootfs 之上；包含的就是典型的 Linux 系统的 /dev，/proc，/bin，/etc 等标准目录和文件；rootfs 就是各种不同的操作系统的发行版

对于一个精简的 OS，rootfs 可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用 Host 的 Kernel，自己只需要提供 rootfs 就行了；由此可见对于不同的 Linux 发行版，bootfs 基本是一致的，rootfs 会由差别，因此不同的发行版可以共用 rootfs

docker 镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部，这一层通常被称作 “容器层”，“容器层” 之下的都叫 “镜像层”



**Docker 理念**

1.将运用与运行的环境打包成容器运行，运行可以伴随着容器，但是我们对数据的要求希望是持久的

2.容器之间希望有可能共享数据

Docker 容器产生的数据，如果不通过 docker commit 生成新的镜像，使得数据作为镜像的一部分保存下来，那么当容器删除后，数据自然也就没有了

为了能保存数据在 Docker 中我们使用卷



##### 2.容器数据卷

将其等价理解为 Redis 中的 AOF 和 RDB 文件

卷就是目录或文件，存在于一个或多个容器中，由 Docker 挂载到容器，但不属于联合文件系统，因此能绕过 Union File System 提供一些持续存储或共享数据的特性：卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此 Docker 不会在容器删除时删除其挂载的数据卷

特点：1.数据卷可在容器之间共享或者重用数据		2.卷中的更改可直接生效

3.数据卷中的更改不会包含在镜像的更新中		4.数据卷的声明周期一直持续到没有容器使用它为止



数据卷添加：

1.直接命令添加

```bash
docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名		#添加对应关系
docker run -it -v /宿主机绝对路径目录:/容器内目录:ro 镜像名	#设定只读权限，也就是容器中指定的目录下的文件只有可读权限，不能创建文件和写文件
#注意这里一定要写绝对路径

docker inspect [容器ID]		#查看数据卷是否添加成功
```

2.通过 Dockerfile 添加



##### 3.DockFile 

先回顾一下通过 Dockerfile 构建镜像的过程：

1、从 base 镜像运行一个容器。

2、执行一条指令，对容器做修改。

3、执行类似 docker commit 的操作，生成一个新的镜像层。

4、Docker 再基于刚刚提交的镜像运行一个新容器。

5、重复 2-4 步，直到 Dockerfile 中的所有指令执行完毕



```
FROM：基础镜像，当前的镜像是基于哪个基础镜像

MAINTAINER：镜像维护者的姓名和邮箱地址

RUN：容器构建时需要运行的命令

EXPOSE：容器对外暴露的端口号

WORKDIR：指定容器在创建后，终端默认登陆进来的工作目录

ENV：用来在构建镜像过程中设置环境变量

ADD：将宿主机目录下的文件拷贝进镜像并且 ADD 命令会自动处理 URL 和解压 tar 压缩包，也即解压缩后再拷贝

COPY：类似 ADD，拷贝文件和目录到镜像中；将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置	

VOLUME：容器数据卷，用于数据保存和持久化工作

CMD：指定一个容器启动时要运行的命令，Dockerfile 中可以有多个 CMD 指令，但只有最后一个生效，CMD 会被 docker run 之后的参数替代

ENTRYPOINT：指定一个容器启动时要运行的命令，其目的和 CMD 一样，都是在指定容器启动程序及参数;
Dockerfile 中可以有多个 ENTRYPOINT 指令，但只有最后一个生效; CMD 或 docker run 之后的参数会被当做参数传递给 ENTRYPOINT

ONBUILD：当构建一个被继承的 Dockerfile 时运行命令，父镜像在被子镜像继承后，父镜像的 onbuild 被触发
```



```bash
# COPY 的两种写法
COPY src dest			#shell脚本写法
COPY ["src","dest"]		#Json字符串写法
```



```bash
#CMD 容器启动命令
CMD 指令的格式和 RUN 相似，也是两种格式
shell 格式：CMD <命令>
exec 格式：CMD ["可执行文件", "参数1", "参数2"]
参数列表格式：CMD ["参数1", "参数2" ... ]; 在制定了 ENTRYPOINT 命令后，用 CMD 指定具体的参数
```



Base 镜像（scratch）：Docker Hub 中百分之 99 的镜像都是通过 base 镜像中安装和配置需要的软件构建出来的

1.编写 Dockerfile

2.构建：`docker build -t [新镜像名字]:TAG .(这个 ”.“ 一定要)`



curl 命令可以用来执行下载、发送各种 HTTP 请求，指定 HTTP 头部等操作，curl 是将下载文件输出到 stdout







### 四、Docker 安装

总体步骤：搜索镜像 ->  拉取镜像 ->  查看镜像  ->  启动镜像  ->  停止容器  ->  移除容器



##### 1.安装报错处理

![image-20211209150410293](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211209150410293.png)

docker 守护进程启动的时候，会默认赋予名字为 docker 的用户组读写 Unix  socket的权限，因此只要创建 docker 用户组，并将当前用户加入到 docker 用户组中，那么当前用户就有权限访问 Unix  socket 了，进而也就可以执行 docker 相关命令

```bash
sudo groupadd docker    	 	 #添加docker用户组
sudo gpasswd -a $USER docker     #将登陆用户加入到docker用户组中
newgrp docker     				 #更新用户组
docker ps    					 #测试docker命令是否可以使用sudo正常使用
```





### 五、疑难总结

##### 1.容器标签

tag 常用于描述镜像的版本信息，比如 httpd 镜像： 

当然 tag 可以是任意字符串，比如 ubuntu 镜像：

![图片](https://raw.githubusercontent.com/BoomChao/Figure/main/640) 

千万别被 latest tag 给误导了。latest 其实并没有什么特殊的含义。当没指明镜像 tag 时，Docker 会使用默认值 latest，仅此而已

虽然 Docker Hub 上很多 repository 将 latest 作为最新稳定版本的别名，但这只是一种约定，而不是强制规定。

所以我们在使用镜像时最好还是避免使用 latest，明确指定某个 tag，比如 httpd:2.3，ubuntu:xenial



search 让我们无需打开浏览器，在命令行中就可以搜索 Docker Hub 中的镜像；但是如果想知道镜像都有哪些 tag，还是得访问 Docker Hub











