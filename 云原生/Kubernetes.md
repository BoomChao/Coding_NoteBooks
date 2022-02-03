## 一、K8s基本概念

#### 1.基础概念

Iaas：Infrastructure as a Service		Pass：Platform as a Service		Sass：Software as a Service



**利用 Terminal 借助 SSH 远程登录到服务器**

1.SSH 分为客户端和 openssh-client 和 openssh-server

如果只是想登陆别的机器，SSH 只需要安装openssh-client（ubuntu有默认安装，如果没有则sudo apt install openssh-client），如果要使本机开放SSH服务就需要安装 openssh-server

2.查看 SSH 客户端版本

```bash
ssh -V	#查看版本
```

3.确认 ssh-server 是否启动

```bash
ps -e | grep ssh  #或者
netstat -tlp
```

如果看到 sshd 那就说明 ssh-server 已经启动了，只有 ssh-agent 就说明 ssh-server 还没有启动

4.客户端登录 SSH 

```bash
ssh username@remote_ip		# 如 ssh chao@192.178.0.1
```



scp 命令传文件

```bash
scp [源文件目录加文件名] [user@ip]:[拷贝过去的目标文件目录]
```





##### 云原生基本概念

1.为什么要使用云平台

**答：**环境统一，按需付费，即开即用，稳定性强

国外常见云平台：亚马逊 AWS，微软 Azure



公有云：购买云服务商提供的公共服务器（或者公共资源）

公有云是最常见的云计算部署类型；公有云资源（例如服务器和存储空间）是由第三方云服务提供商拥有和运营，这些资源通过 Internet 提供；在公有云中，所有硬件、软件和其他支持基础结构均为云提供商所拥有和管理

公有云优势：

1. 成本更低：无需购买硬件或者软件，仅对使用的服务付费

2. 无需维护：维护由服务提供商提供

3. 近乎无限制的缩放性：提供按需资源，可满足业务需求

4. 高可靠性：具备众多服务器，确保免受故障影响



私有云：自己搭建平台或者购买

私有云专供一个企业或者使用的云计算资源构成；私有云可在物理上位于组织的现场数据中心，也可由第三方服务商托管；但是在私有云中，服务和基础结构始终在私有网络上进行维护，硬件和软件专供组织使用

这样私有云可使组织更加方便地自定义资源，从而满足特定地 IT 需求；私有云的使用对象通常为政府机构、金融机构以及其他具备业务关键性运营并且希望对环境拥有更大控制权的中型到大型组织

私有云优势：

1. 灵活性更强：组织可定义云环境以满足特定业务需求

2. 控制力更强：资源不与其他组织共享，因此能获得更高的控制力以及更高的隐私级别

3. 可伸缩性更强：与本地基础结构相比，私有云通常具有更强的可伸缩性



没有一种云计算类型适用于所有人；多种不同的云计算模型，类型和服务已得到发展，可以满足组织快速变化的技术需求

部署云计算资源由三种不同的方法：公有云，私有云和混合云；采用的部署方法取决于业务需求

------



K8s 是一个用于容器化应用程序的**编排**工具，它负责：

1.部署镜像和容器	2.管理容器和集群的扩展	3.容器和集群的资源管理	4.服务的流量管理



#### 2.组件说明

etcd 的官方将它定义为一个可信赖的分布式键值存储服务，它能够为整个分布式集群存储一些关键数据，协助分布式集群的正常运转

api server：所有服务访问的统一入口

controller manager：维护副本期望数目

scheduler：负责介绍任务，选择合适的节点分配任务

etcd：键值对数据库，存储 K8s 集群的所有重要信息

kubelet：直接跟容器引擎交互实现容器的生命周期管理

kube - proxy：负责写入规则至 IPTABLES、IPVS 实现服务映射访问

coredns：可以为集群中的 SVC 创建一个域名 IP 的对应关系解析

dashboard：给 K8s 集群创建一个 B/S 结构访问体系

ingress controller：官方只能实现四层代理，ingress 可以提供七层代理

fedetation：提供一个可以跨集群中心多 K8s 统一管理功能

prometheus：提供 K8s 集群的监控能力

ELK：提供 K8s 集群日志统一分析介入平台



集群中的每一个应用称为一个 Pod



K8s 资源创建方式：命令行和 YAML 文件

Namespace：名称空间用来对集群资源进行隔离划分，默认只隔离资源，不隔离网络

Pod：运行中的一组容器，Pod 是 K8s 中应用的最小单位



K8s 主要安装的就是三个东西：Kubeadm，Kubelet，Kubectl

- Kubeadm：用来初始化集群的指令


- Kubelet：在集群中的每个结点上用来启动 pod 和 container 等


- Kubectl：用来与集群通信的命令行工具




k8s 中基本所有资源的一级属性都是相同的，主要包含五个部分：

apiVersion \<string> 	版本：由 k8s 内部定义，可以使用 `kubectl api-versions` 查询到

kind \<string> 			  类型：由 k8s 内部定义，可以使用 `kubectl api-resources` 查询到

metadata \<Object>    元数据：主要是资源标识和说明，常用的有 name, namespace, labels 等

spec \<Object>			描述：这是配置中最重要的部分，里面是对各种资源的详细描述

status \<Object> 		 状态信息：里面的内容不需要定义,由 k8s 自动生成



spec 参数的子属性：

containers \<[]Object> 		容器列表：用于定义容器的详细信息

nodeName \<String>			根据 nodeName 的值将 Pod 调度到指定的 Node 节点上

nodeSelector \<map[]>  		根据 NodeSelector 中定义的信息选择将该 Pod 调度到包含这些 label 的 Node 上

hostNetwork \<boolean>		是否使用主机网络模式，默认为 false, 如果设置成 true, 则表示使用宿主机网络

volumes \<[]Object>			容器卷：用于定义 Pod 上挂载的存储信息

restartPolicy \<string>		重启策略：表示 Pod 在遇到故障的时候的处理策略



k8s 在主容器启动之后和停止之前提供了两个钩子函数：

- post start:  容器创建之后执行，如果失败了会重启容器
- pre stop: 容器终止之前执行，执行完成之后容器将成功终止，在其完成之前会阻塞删除容器的操作

钩子处理器支持使用以下三种方式定义动作

- Exec 命令: 在容器内执行一次命令
- TCPSocket:  在当前容器尝试访问指定的 socket
- HTTPGet: 在当前容器中向某 URL 发起 http 请求





##### Namespace

如果有多个用户或项目组使用同一个 Kubernetes Cluster，如何将他们创建的 Controller、Pod 等资源分开呢？

使用 Namespace 可以将一个物理的 Cluster 逻辑上划分成多个虚拟 Cluster，每个 Cluster 就是一个 Namespace。不同 Namespace 里的资源是完全隔离的

K8s 集群在启动后，会默认创建几个 namespace (用来对集群资源进行隔离划分，默认只隔离资源，不隔离网络)

```bash
default 			# 所有未指定Namespace的对象都会被分配到default空间
kubde-node-release	# 集群节点之间的心跳维护
kube-public			# 此命名空间下的资源所有人都可以访问(包括未认证用户)
kube-system			# 系统资源存放的命名空间
```



```bash
kubectl create ns hello		#创建命令空间
kubectl delete ns hello		#删除命名空间

kubectl get nodes			#查看集群的节点状态

kubectl get pod 			#查看默认的名称空间
kubectl get pod -A			#查看所有名称空间中的容器
kubectl get pod -owide		#对于每个Pod，k8s都为其分配了一个IP,这样每个Pod都可以用IP访问到
#使用Pod的ip+pod里面运行的容器端口,就可以访问Pod里面的容器
curl [ip]

kubectl exec -it mynginx -- /bin/bash	#进入Pod里面的容器

#启动一个pod
kubectl run mynginx --image=nginx		#这个pod利用nginx镜像

#查看pod的日志信息
kubectl desrcibe pod [pod-name] -n [namespace]

#删除Pod
kubectl delete pod [podname]
```

也可通过 yaml 文件创建命名空间（使用配置文件创建的还是建议使用yaml文件来进行删除）

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: hello
```

```bash
kubectl apply -f hello.yaml		#根据yaml文件创建
kubectl delete -f hello.yaml	#删除yaml文件创建的资源
```



##### Pod

Pod 能够具有多个容器，应用运行在容器里面，但是它有可能有一个或多个先于应用容器启动的 Init 容器

Init 容器与普通的容器非常像，除去以下两点

1.Init 容器总是运行到成功为止		2.每个 Init 容器 都必须在下一个 Init 容器启动之前成功完成



如果 Pod 的 Init 容器启动失败，K8s 会不断地重启该 Pod，直到 Init 容器成功为止，然而如果 Pod 对应的 restartPolicy 为 Never，则它不会重新启动

因为 Init 容器具有与应用容器分离的单独镜像，所以它们的启动相关代码具有如下优势：

1.它们可以包含并运行使用工具，但是出于安全考虑，是不建议在应用程序镜像中包含这些工具

2.他们可以包含使用和定制化代码来安装，但是不能出现在应用程序镜像中；例如，创建镜像没必要 FORM 另一个函数，只需要在安装过程中使用类似 Pod、awk、python、或者 dig 这样的工具

3.应用程序镜像可以分离出创建和部署的角色，而没有必要联合它们构建一个单独的镜像

4.Init 容器使用 Linux Namespace，所以相对来说应用程序容器来说具有不同的文件系统视图，因此，它们能具有访问 Secret 的权限，而应用程序容器则不能

5.它们必须在应用程序容器启动之前来完成，而应用程序容器是并行执行的，所以 Init 容器提供了一种简单的阻塞或延迟应用容器的启动方法，直到满足一组先决条件



每个Pod里面都可以包含一个或者多个容器，这些容器可以分为两类：

1、用户程序所在的容器，数量可多可少

2、Pause 容器，这是Pod都会有的一个根容器，它的作用有两个：	

- 可以以它为依据，评估整个Pod的状态

- 可以在根容器上设置 IP 地址，其他容器都以此 IP 来实现 Pod 内部的网络通信（Pod内部的通讯都是通过虚拟二级网路实现的）



Pod的生命周期

Pod 创建

运行初始化容器（init container）过程

运行主容器（main container）过程
- 容器启动后钩子（post start）、容器终止前钩子（pre stop）
- 容器的存活性探测（liveness probe）、就绪性探测（readiness probe）

Pod 终止



Pod 的创建过程

1. 用户通过 kubectl 或其他 API 客户端提交需要创建的 pod 信息给 apiServer

2. apiServer 开始生成 pod 对象的信息，并将信息存入 etcd，然后返回确认信息至客户端

3. apiServer 开始反映 etcd 中的 pod 对象的变化，其他组件使用 watch 机制来跟踪检查 apiServer 上的变动

4. scheduler 发现有新的 pod 对象要创建，开始为 Pod 分配主机并将结果信息更新至 apiServer

5. node 节点上的 kubelet 发现有 pod 调度过来，尝试调用 docker 启动容器，并将结果回送至 apiServer

6. apiServer 将接受到的 pod 状态信息存入 etcd 中



1.在 Pod 启动过程中，Init 容器会按顺序在网络和数据卷初始化后启动，每个容器必须在下一个容器启动之前成功退出

2.如果由于运行时或失败退出，将导致容器启动失败，它会根据 Pod 的 restartPolicy 指定的策略进行重试，然而如果 Pod 的 restartPolicy 设置为 Always，Init 容器失败时就会使用 restartPolicy  策略

3.在所有的 Init 容器没有成功之前，Pod 将不会变成 Ready 状态。Init 容器的端口将不会在 Service 中进行聚集。正在初始化中的 Pod 处于 Pending 状态，但应该会将 Initializing 状态设置为 true

4.如果 Pod 重启，所有 Init 容器必须重新执行

5.对 Init 容器 space 的修改被限制在容器 image 字段，修改其他字段都不会生效。更改 Init 容器的 image 字段，等价于重启该 Pod

6.Init 容器具有应用容器的所有字段；除了 readinessProbe，因为 Init 容器无法定义不同于完成（Completion）的就绪（readiness）之外的其他状态，这会在验证过程中强制执行

7.在 Pod 中的每个 app 和 Init 容器的名称必须唯一；与其他任何共享同一个名称，会在验证时抛出错误



Pod 的状态

- 挂起（Pending）：Pod 已被 K8s 系统接受，但有一个或者多个容器镜像尚未创建；等待时间包括调度 Pod 的时间和通过网络下载镜像的时间，这可能需要花点时间

- 运行中（Running）：该 Pod 已经绑定到一个节点上，Pod 中所有容器都已被创建。至少有一个容器正在运行，或者正处于启动的状态

- 成功（Successed）：Pod 中的所有容器都被成功终止，并且不会再重启

- 失败（Failed）：Pod 中的所有容器都被成功终止，并且不会再重启

- 未知（Unknow）：因为某些原因无法取得 Pod 的状态，通常是因为与 Pod 所在主机通信失败



Pod 是 Kubernetes 中应用的最小单位，一个 Pod 中可以运行多个容器

创建 Pod

```bash
kubectl run mynginx --image=nginx	# 创建一个Pod名为mynginx, 利用的是nginx镜像
kubectl delete pod mynginx			# 删除pod
kubectl logs myngix				    # 得到启动日志
```

利用配置文件创建 Pod（一个Pod里面有一个容器 Nginx ）

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: mynginx
  name: mynginx
spec:
  containers:
  - image: nginx
    name: mynginx
```

一个 Pod 运行多个容器（一个Pod里面有两个容器 Nginx 和 Tomcat ）

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels: 
    run: myapp
  name: myapp
spec:
  containers:
  - image: nginx
    name: nginx
  - image: tomcat:8.5.68
    name: tomcat
```



```bash
# 进入pod中的容器查看
# kubectl exec [pod名称] -n [命名空间] -it -c [容器名] /bin/sh 
kubectl exec pod-command -n dev -it -c busybox /bin/sh   #这条命令已经过时，改为如下
kubectl exec pod-command -c bysybox -n dev -- [cat /tmp/hello.txt] #"[]"接需要执行的命令 
```



Pod 调度

在默认情况下，一个 Pod 在哪个 node 节点上运行，是由 Scheduler 组件采用相应的算法计算出来的，这个过程是不受人工控制的。但是在实际使用过程中，这并不满足实际需求，因为很多情况下，我们想控制某些 Pod 到达某些节点上，K8s 提供了四大类调度方式：

- 自动调度：运行在哪个节点上完全由 Seheduler 经过一系列的算法计算得到
- 定向调度：NodeName、NodeSelector
- 亲和性调度：NodeAffinity、PodAffinity、PodAntiAffinity
- 污点(容忍)调度：Tains、Toleration

定向调度指的是利用在 Pod 上声明 nodeName 或者 NodeSelector，以此将 Pod 调度到期望的 node 节点上。这里的调度是强制的，也就意味着即使调度的目标 Node 不存在，也会向上面进行调度，只不过 Node 运行失败而已



污点和容忍

污点 (Taints)

前面的调度方式都是站在 Pod 的角度上，通过在 Pod 上添加属性，来确定 Pod 是否要调度到指定的 Node 上，其实也可以站在 Node 的角度上，通过在 Node 上添加污点属性，来决定是否允许 Pod 调度过来

Node上被设置污点之后就和Pod之间存在了一种相斥的关系，进而拒绝 Pod 调度进来，甚至可以将已存在的 Pod驱逐出去

污点的格式为：`key=value:effect` , key 和 value 是污点的标签，effect描述污点的作用，支出如下三个选项:

- PreferNoSchedule: k8s 尽量避免把Pod调度到具有该污点的 Pod 上，除非没有其他节点可调度
- NoSchedule: k8s 将不会把Pod调度到具有该污点的 Node 上，但也不会影响当前 Node 上已经存在的 Pod
- NoExecute: k8s 将不会把Pod调度到具有该污点的 Node 上，同时也会将 Node 上已存在的 Pod 隔离

```bash
kubectl taint node k8s-node-01 tag=heima:NoExecute		# 打污点
```



使用 kubeadm 搭建的集群，默认就会给 master 节点添加一个污点标记，所以 Pod 就不会调度到 master 节点上



容忍 (Toleration)

上面介绍了污点的作用，我们可以在 node 上添加污点用于拒绝 Pod 调度上来，但是如果就是想将一个 Pod 调度到一个有污点的 node 上去，就可以使用容忍

容忍的详细配置

```bash
tolerations:
- key: "tag"			# 对应着要容忍的污点的键
operator: "Equal"		# key-value的运算符，支持Equal和Exists
value: "heima"			# 对应着要容忍的污点的值
effect: "NoExecute"		# 对应污点的effect,空意味着匹配所有影响
tolerationSeconds:  	# 容忍时间,当effect为NoExecute时生效，表示Pod在Node上的停留时间
```



RC

```yaml
apiVersion: apps/v1   # 版本号
kind: ReplicaSet  # 类型
metadata: # 元数据
  name: # rs名称
  namespace:  # 所属命令空间
  labels:   # 标签
    controller: rs   
spec:   # 详细描述
  replicas: 3   # 副本数量
  selector:     # 选择器
    matchLabels:  # Labels匹配规则
      app: nginx-pod
    matchExpressions:   # Expression 匹配规则
      - {key: app, operator: In, value: {nginx-pod}}
  template:		# 模板,就是当前控制器创建Pod所使用的模板,就是Pod的定义
    metadata:
      labels:
        app: nginx-pod
    spec:
      containers:
      - name: nginx
        image: nginx:1.17.1
        ports:
        - containerPort: 80
```



```bash
kubectl edit rs pc-replicaset	# 直接修改配置文件		
kubectl scale rs pc-replicaset --replicas=2 -n dev	# 扩缩容
kubectl delete rs pc-replicaset -n dev	# 删除rs,建议直接用yaml文件删除
```





##### Deployment

Deployment 为 Pod 和 ReplicaSet 提供了一个声明式定义（declarative）方法，用来代替以前的 ReplicationController 来方便的管理应用

Deployment 控制器不直接管理 Pod, 而是通过 ReplicaSet 来间接管理 Pod, 即 Deployment 管理 ReplicaSet, ReplicaSet 管理 Pod, Deployment 的功能比 ReplicaSet 更加强大

典型的应用场景包括：

1.定义 Deployment 来创建 Pod 和 ReplicaSet		2.滚动升级和回滚应用

3.扩容和缩容		4.暂停和继续 Deployment



Deployment（控制 Pod）：使得 Pod 具有多副本，自愈，扩缩容的能力

```bash
#创建一个部署
kubectl create deployment mytomcat --image=tomcat:8.5.68

#删除部署
kubectl delete deploy mytomcat

#多副本创建（命名错误）
kubectl create deployment my-dep --image=nginx --replicas=3

#查看部署
kubectl get deploy
```

使用 yaml 文件创建多个副本

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-dep
  name: my-dep
spec:
  replicas: 3		#设置副本数
  selector:
    matchLabels:
      app: my-dep
  template:
    metadata:
      labels:
        app: my-dep
    spec:
      containers:
      - image: nginx
        name: nginx 
```

扩缩容

```bash
kubectl scale --replicas=5  deployment/my-dep
```

滚动更新

```bash
kubectl set image deploy/my-dep nginx=nginx:1.16.1 --record		#更新一般只更新镜像即可
kubectl rollout status deployment/my-dep
```

版本回退

```bash
#查看版本记录
kubectl rollout history deploy/my-dep

#回滚（回到上次）
kubectl rollout undo deploy/my-dep

#版本回退到指定版本
kubectl rollout undo deploy/my-dep --to-revision=2
```





![image-20220131235719663](C:\Users\12458\AppData\Roaming\Typora\typora-user-images\image-20220131235719663.png)

Deployment：无状态应用部署，比如微服务，提供多副本功能

Statefulset：有状态应用部署，比如 Redis，提供稳定的存储，网络等功能

DeamonSet：守护型应用部署，比如日志收集组件，在每个机器都运行一份

Job/CronJob：定时任务部署，比如垃圾清理组件，可以在指定时间运行



##### Service

service 可以选择一组 Pod，将其暴露出来，对外提供统一服务，是将一组 Pod 公开为网络服务的抽象方法

```bash
#暴露deploy里面的端口,将Pod里面的80端口暴露到外面的8000端口,创建一个service
#将一组Pod公开为网络服务的抽象方法
kubectl expose deploy my-dep --port=8000 --target-port=80
#暴露后，集群内使用 service 的 ip:port 就可以负载均衡的访问

#下面两条语句等价
kubectl expose deploy my-dep --port=8000 --target-port=80
kubectl expose deploy my-dep --port=8000 --target-port=80 --type=ClusterIP	#集群内部访问

kubectl expose deploy my-dep --port=8000 --target-port=80 --type=NodePort	#集群外部访问

#查看Pod的标签
kubectl get pod --show-labels
```



##### Ingress

Kubernetes Ingress 的原理，简单来说：它不过是一种轻松配置 Nginx 服务器的方法，它可以将请求重定向到其他内部服务去。这为我们节省了宝贵的静态 IP 和 LoadBalancers 资源

Ingress : Service 的统一网关入口

Service 对集群之外暴露服务的主要方式有两种: NodePort 和 LoadBalancer, 但是这两种方式都有缺点:

- NodePort 方式的缺点就是会占用很多集群机器的端口，那么当集群服务变多的时候，这个缺点就愈加明显
- LB 方式的缺点是每个 service 需要一个 LB, 浪费和麻烦，并且需要 k8s 之外的设备的支持

ingress 就是 nginx 做的

```bash
#1.下载ingress,这里能下载下来,可能需要等一段时间
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.47.0/deploy/static/provider/baremetal/deploy.yaml	 

#2.将容器controller的镜像名字修改为阿里云的镜像(否则可能拉取不下来)  这个镜像地址不正确
image: registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/ingress-nginx-controller:v0.46.0

#3.部署ingress
kubectl apply -f deploy.yaml	

#4.检查安装的结果
kubectl get pod,svc -n ingress-nginx
```

这个当做 yaml 的学习文件

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dev
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-pod
  template:
    metadata:
      labels:
        app: nginx-pod
    spec:
      containers:
      - name: nginx
        image: nginx:1.17.1
        ports:
        - containerPort: 80
---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment
  namespace: dev
spec:
  replicas: 3
  selector:
    matchLabels:
      app: tomcat-pod
  template:
    metadata:
      labels:
        app: tomcat-pod
    spec:
      containers:
      - name: tomcat
        image: tomcat:8.5-jre10-slim
        ports:
        - containerPort: 8080
---

apiVersion: v1
kind: Service
metadata:
  name: tomcat-service
  namespace: dev
spec:
  selector:
    app: tomcat-pod
  clusterIP: None
  type: ClusterIP
  ports:
  - port: 8080
    targetPort: 8080
---

apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: dev
spec:
  selector:
    app: nginx-pod
  clusterIP: None
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
```

http代理

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-http
  namespace: dev
spec:
  rules:
  - host: nginx.itheima.com
    http:
      paths:
      - path: /
        backend:
          serviceName: nginx-service
          servicePort: 80
  - host: tomcat.itheima.com
    http:
      paths:
      - path: /
        backend: 
          serviceName: tomcat-service
          servicePort: 8080
```



ingress 作为 https 代理

创建证书

```bash
#生成证书
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt 
-subj "/C=CN/ST=BJ/L=BJ/O=nginx/CN=itheima.com"

#创建秘钥
kubectl create secret tls tls-secret --key tls.key --cert tls.crt
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-http
  namespace: dev
spec:
  tls:
   - hosts:
     - nginx.itheima.com
     - tomcat.itheima.com
     secretName: tls-secret		#这个名字一定要和上面创建的secret的名字相同
  rules:
  - host: nginx.itheima.com
    http:
      paths:
      - path: /
        backend:
          serviceName: nginx-service
          servicePort: 80
  - host: tomcat.itheima.com
    http:
      paths:
      - path: /
        backend:
          serviceName: tomcat-service
          servicePort: 8080
```



##### Volume 

EmptyDir 是最基础的 Volume 类型, 一个 EmptyDir 就是Host上的一个空目录

EmptyDir 是在 Pod 被分配到 Node 时创建的, 它的初始内容为空, 并且无须指定宿主机上对应的目录文件, 因为 k8s 会自动分配一个目录, 当 Pod 销毁时, EmptyDir 中的数据也会永久被删除. EmptyDir 的用途如下

- 临时空间: 例如用于某些应用程序运行时所需的目录, 且无须永久保留
- 一个容器需要从另外一个容器中获取数据的目录



HostPath

EmptyDir 中的数据不会被持久化，它会随着 Pod 的结束而销毁，如果想将简单的数据持久化到主机中，可以选择 HostPath

HostPath 就是将 Node 主机中的一个实际目录挂在 Pod 中，以供容器使用，这样的设计就可以保证即使 Pod 销毁了，但是数据依然可以存在于 Node 主机上



NFS

HostPath 可以解决数据持久化的问题，但是一旦 Node 节点故障了，Pod 如果转移到了其他的节点，就又会出现问题

NFS 是一个网路文件存储系统，可以搭建一台 NFS 服务器，然后将 Pod 中的存储直接连接到NFS系统上, 这样无论 Pod 在节点上怎么转义，只要 Node 跟 NFS 的对接没有问题，数据就可以成功访问



##### PV 和 PVC

PersistentVolume (PV) 是外部存储系统中的一块存储空间，由管理员创建和维护。与 Volume 一样，PV 具有持久性，生命周期独立于 Pod

PersistentVolumeClaim (PVC) 是对 PV 的申请 (Claim)。PVC 通常由普通用户创建和维护。需要为 Pod 分配存储资源时，用户可以创建一个 PVC，指明存储资源的容量大小和访问模式（比如只读）等信息，Kubernetes 会查找并提供满足条件的 PV

PV：持久卷 (Persistent Volume)，将应用需要持久化的数据保存到指定位置，真正存储数据的地方

PVC：持久卷声明 (Persistent Volume Claim)，申明需要使用的持久卷规格 

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211220211109911.png) 

环境准备

1.所有节点安装

```bash
sudo apt install nfs-common		# 安装nfs工具
```

2.主节点

```bash
apt install nfs-kernel-server
echo "/nfs/data/" *(insecure,rw,sync,no_root_squash) > /etc/exports	#修改exports文件
mkdir -p /nfs/data						#创建共享文件
systemctl enable rpcbind --now			#启动远程rpc服务
service nfs-kernel-server restart		#重启服务	

#查看暴露的目录
exportfs
```

3.从节点

```bash
#查看远程机器的挂载目录
showmount -e 10.7.20.132

#执行以下命令挂载 nfs 服务器上的共享目录到本机路径 /nfs/data
mkdir -p /nfs/data

#挂载(将远程机上的/nfs/data挂载到本机的/nfs/data)
mount 10.7.20.132:/nfs/data /nfs/data	
```

挂载目录使用 pv 和 pvc，挂载配置文件使用 configureMap



Secret 对象类型用来保存敏感信息，例如密码，OAth令牌和 SSH 密钥



使用了 PV 和 PVC 之后, 工作可以得到进一步的细分

- 存储: 存储工程师维护
- PV: k8s 管理员维护
- PVC: k8s 用户维护

PV 资源清单

```yaml
apiVersion: v1
kind: PersistentVolume		#PV是一种全局资源，注意没有namespace
metadata:
  name: pv2
spec:
  nfs:  #存储类型
  capacity:
    storage: 2Gi
  accessModes:    #访问模式
  storageClassName:
  persistentVolumeReclaimPolicy:  #回收策略
```

PV 的配置参数说明:

存储类型: 底层实际存储的类型, 如 NFS, CIFS

存储能力: 目前只支持存储空间的设置 (storage = 1Gi)

访问模式: 用于描述用户对于存储资源的访问权限, 包括以下几种方式

- ReadWriteOnce (RWO): 读写权限，**但是只能被单个节点挂载**
- ReadOnlyMany (ROX): 只读权限，可以被多个节点挂载
- ReadWriteMany (RWX): 读写权限，可以被多个节点挂载

注意：底层不同的存储类型可以支持的访问模式不同

回收策略: 当 PV 不再被使用之后，对其的处理方式. 目前支持三种策略:

- Retain 保留数据
- Recycle 回收：清除 PV 中的数据，效果相当于执行 rm -rf /volume/*
- Delete 删除: 与 PV 相连的后端存储完成 volume 的删除操作

存储类别: PV 可以通过 storageClassName 参数指定一个存储类别

- 具有特定类别的PV只能与请求了该类别的PVC进行绑定
- 未设定类别的PV则只能与不请求任何类型的PVC进行绑定

状态: 

- Available (可用)：表明可用状态，还未与任何 PVC 绑定
- Bound (已绑定)：表示 PV 已经被 PVC 绑定
- Released (已释放)：表示PVC被删除，但是资源还未被集群重新声明
- Failed (失败)：表示该PV的自动回收失败



PVC 是对资源的申请, 用来声明对存储空间， 访问模式，存储类别需求信息

```yaml
apiVersion: v1
kind: PersistenVolumeClaim
metadata:
  name: pvc
  namespace: dev
spec:
  accessModes:    #访问模式
  selector:       #采用标签对PV选择
  storageClassName: #存储类别
  resources:      #请求空间
    requests:
      storage: 5Gi
```



##### Liveness 和 Readness 

用户通过 Liveness 探测可以告诉 Kubernetes 什么时候通过重启容器实现自愈；

Readiness 探测则是告诉 Kubernetes 什么时候可以将容器加入到 Service 负载均衡池中，对外提供服务



下面对 Liveness 探测和 Readiness 探测做个比较：

1、Liveness 探测和 Readiness 探测是两种 Health Check 机制，如果不特意配置，Kubernetes 将对两种探测采取相同的默认行为，即通过判断容器启动进程的返回值是否为零来判断探测是否成功。

2、两种探测的配置方法完全一样，支持的配置参数也一样。不同之处在于探测失败后的行为：Liveness 探测是重启容器；Readiness 探测则是将容器设置为不可用，不接收 Service 转发的请求。

3、Liveness 探测和 Readiness 探测是独立执行的，二者之间没有依赖，所以可以单独使用，也可以同时使用。用 Liveness 探测判断容器是否需要重启以实现自愈；用 Readiness 探测判断容器是否已经准备好对外提供服务。



##### Helm

Helm 帮助 Kubernetes 成为微服务架构应用理想的部署平台（安装查阅官方网站 https://helm.sh/）

Helm 让我们能够像 apt 管理 deb 包那样安装、部署、升级和删除容器化应用

Helm 由客户端和 Tiller 服务器组成。客户端负责管理 chart，服务器负责管理 release

chart 是 Helm 的应用打包格式，它由一组文件和目录构成。其中最重要的是模板，模板中定义了 Kubernetes 各类资源的配置信息，Helm 在部署时通过 values.yaml 实例化模板

Helm 允许用户开发自己的 chart，并为用户提供了调试工具。用户可以搭建自己的 chart 仓库，在团队中共享 chart

Helm 帮助用户在 Kubernetes 上高效地运行和管理微服务架构应用



部署 Helm 客户端和 Tiller 服务器 (安装直接去官网查看官方的安装手册)

```bash
# 安装Helm3
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

# 查看version
helm version
```

helm 修改 repo 源

```bash
helm repo remove stable
helm repo add stable https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts  #阿里云镜像没有更新
helm repo update
helm search
```

```bash
helm search hub mysql					# 查看公开可用的charts
helm install my-relrease bitnami/mysql	# 安装一个helm包
helm uninstall bitnami/mysql			# 卸载
```



##### HPA

Horizontal Pod Autoscaling 可以根据 CPU 利用率自动伸缩一个 Replication Controller、Deployment 或者 Repulica Set 中的 Pod 数量







## 二、安装 K8s

#### 1.安装过程

1.安装预备环境

```bash
#1.修改主机名便于区分
hostnamectl set-hostname k8s-master
hostnamectl set-hostname k8s-node-01
hostnamectl set-hostname k8s-node-02

#2.关闭防火墙
ufw disable

#3.关闭 swap 分区
swapoff -a		#临时关闭
sed -i 's/.*swap.*/#&/' /etc/fstab	#永久关闭

#4.允许 iptables 检查桥接流量(这个可以先不设置)
cat << EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat << EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

#5.让以上的配置生效
sudo sysctl --system 	
```

附：swap 分区指的是虚拟内存分布，它的作用就是在物理内存使用完之后，将磁盘空间虚拟成内存来使用

启用 swap 设备会对系统的性能产生非常负面的影响，因此 k8s 要求每个节点都要禁用 swap 分区



2.安装 kubelet, kubectl, kubeadm

这一步主从节点都需要安装

```bash
# 修改 apt 镜像源为阿里云
# vim /etc/apt/sources.list
# deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main
# 上面个两行命令合并为下面一行, 这里cat后面一定要接 ">>" 表示追加
cat >>  /etc/apt/sources.list << EOF	
deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main
EOF

apt update	#更新软件源

# 安装k8s三大件，不加版本号则默认安装最新版本,不要安装太高版本，防止对应的镜像找不到
apt-get install -y kubelet=1.20.9-00 kubeadm=1.20.9-00 kubectl=1.20.9-00

# 查看需要安装的镜像文件
kubeadm config images list --kubernetes-version v1.20.9 #这个版本信息需要从上一步安装时候查看，上一步安装的是哪个版本，这个就用哪个版本

# 显示结果如下
k8s.gcr.io/kube-apiserver:v1.20.9
k8s.gcr.io/kube-controller-manager:v1.20.9
k8s.gcr.io/kube-scheduler:v1.20.9
k8s.gcr.io/kube-proxy:v1.20.9
k8s.gcr.io/pause:3.2
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns:1.7.0
```

写脚本下载各个机器需要的镜像文件并改名

```bash
#注意这里第一个 EOF 使用 '' 否则下面的命令在命令行中可能被执行，也不是以文本形式保存到 image.sh 中
sudo tee image.sh << 'EOF'		
#! /bin/bash
images=(
k8s.gcr.io/kube-apiserver:v1.20.9
k8s.gcr.io/kube-controller-manager:v1.20.9
k8s.gcr.io/kube-scheduler:v1.20.9
k8s.gcr.io/kube-proxy:v1.20.9
k8s.gcr.io/pause:3.2
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns:1.7.0
)

#下载镜像并直接改名,删除原有镜像
for imageName in ${images[@]}; do
# 阿里云的镜像名没有前面的 "k8s.gcr.io/"
aliyunName=${imageName:11}			
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/$aliyunName
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/$aliyunName $imageName
docker rmi registry.cn-hangzhou.aliyuncs.com/google_containers/$aliyunName
done
EOF

source image.sh
```



2.1.主节点操作

注意：kubeadm init 时这个 pod-network-cidr 一定要改成192.168.0.0，因为这是下面的网络插件 calico 的默认IP

```bash
#---------------下面的只有主节点才能操作，从节点等主节点初始化之后直接加入即可
#镜像下载完成后就可以启动master节点了(主节点初始化)，  注意这个 pod-network-cidr 一定要改成192.168.0.0,因为这是下面的网络插件calico的默认IP
kubeadm init --kubernetes-version=v1.20.9 --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=[本机IP]

#附：kubeadm init 就是初始化K8s集群，建议这个命令之前都先 kubeadm reset 一下
#在安装部署Kubenetes的过程中出现的任何报错，尝试解决报错问题后，最好是先执行一次 kubeadm reset -f 来清除一下kubeadm的信息，再进行验证错误是否得到解决，不然可能上个报错没有解决又出现以下或者新的报错

#当收到kubeadm join [masterip]:6443 --token hagd9m.ohpm4iyvu4odmcqk  --discovery-token-ca-cert-hash sha256:855cb9d1a3131a18f657259aad123217151a05e5fcca8b894287dc101eae353c，说明成功了，保存这条消息，其他节点加入时需要使用

#设置配置文件如下 (根据上一步的安装提示来进行配置)
mkdir -p /root/.kube
cp -i /etc/kubernetes/admin.conf /root/.kube/config 
chown root:root /root/.kube/config

#下载网络插件Calico
curl https://docs.projectcalico.org/manifests/calico-typha.yaml -o calico.yaml
kubectl apply -f calico.yaml
#下面这种方法也可以，网络插件好有多种，这是使用 kube-flannel
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

#查看状态，看到状态是 ready 就表示安装成功了
kubectl get nodes
```



2.2.从节点直接加入即可

```bash
#(1)按照上一步主节点安装成功后的 kubeadm join ... 提示保存下来直接加入即可

#(2)如果忘记了当初的 kubeadm join 命令(也就是令牌过期了)，则执行下面的操作
#在master节点上
kubeadm token create --print-join-command


#下面这是分步骤创建(可以不看)
#1.获取token
kubeadm token create
#假如结果如下
q1p35e.pdiiofa81j		

#2.运行获取 cert-hash
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
#假如结果如下
7d2ccc10eeca00bd774c00f4a1587eb15ae

#在从节点上加入即可，端口号默认是 6643端口
kubeadm join [masterip]:6443 --token q1p35e.pdiiofa81j --discovery-token-ca-cert-hash sha256:7d2ccc10eeca00bd774c00f4a1587eb15ae
#注：如果这一步不小心安装错误，则一定要先执行 kubeadm reset 命令清除上一次的操作内容
```



3.安装 K8s 的 Web 可视化界面：dashboard

```bash
#1.下载yaml文件
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.4.0/aio/deploy/recommended.yaml

#2.找到type更改为 type:ClusterIP 为 type:NodePort
edit svc kubernetes-dashboard -n kubernetes-dashboard

#3.运行(这一步相当于暴露端口给用户访问)
kubectl get svc -A | grep kubernetes-dashboard

#4.生成令牌(token),相当于给K8s创建一个访问者身份

# 创建一个 dash.yaml 文件，内容如下
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user	# 用户名
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard

#执行上面的 dash.yaml
kubectl apply -f dash.yaml

#5.获取访问令牌
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"

#结果为 (这是本机的dashboard的令牌)
#eyJhbGciOiJSUzI1NiIsImtpZCI6IjlXLXRid3hOMUp6MFFUcWd5SXNQWWpsOUVKMWMwNzBhY2h1ZGtaT0N1LUkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWR4NTVxIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJmODQxNzU2Yy00OTU3LTQ2NTMtYjk0ZS05MjU5MTJiYWVhNmEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.VY-S5WxTQ7JW2xo3ktBOu04JwWvJDas5cSK8RTo1I5s-xRpfJBoBTdjLFWmt22FEWuFHIV3jyc6TTKbS5-s_PyhKUb1GqLKuFovF5b3fK-a7ndbhfHx63Nd-N3As0hxyZir-SS6AxBv1EAYeAucJcsHaVNVsjpRMMtRuSFoYy8pcrsv4QZtm0UovUWbYolJPB1ua0005Hf-IgbjfThLqpWXC8aWlUDBsQOMrijtUeotgwsHKLsqFY2PRVb6dk0jgOx5B-vUVJQM-90AfvutE-Dvyxtka6ts4EJKtzyvEvhje7hgReI7IU0Q1AUAXryQ4Jmaiiz8fMZHffYA9fq_BmA
```





##### 1.1.安装报错处理

1.报错解决：如果安装三大件过程中出现以下错误

![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211125154040613.png) 

**解决方法：**

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FEEA9169307EA071 8B57C5C2836F4BEB  #添加对应的PUBKEY
sudo apt update #更新一下即可
```

2.如果某个节点一直处于 Not Ready 状态，尝试重启一下 kubelet

```bash
systemctl restart kubelet
```





#### 2.Yaml 文件

介绍：YAML仍是一种标记语言。为了强调这种语言以数据作为中心，而不是以标记语言为重点，YAML是一个可读性高，用来表达数据序列的格式

k8s集群中对资源管理和资源对象编排部署都可以通过声明样式（YAML）文件来解决，也就是可以把需要对资源对象的操作编辑到YAML格式文件中，我们把这种文件叫做资源清单文件，通过kubectl命令直接使用资源清单文件就可以实现对大量的资源对象进行编排部署

```bash
kubectl create -f xxx.yaml		# 以yaml文件创建
kubectl deploy -f xxx.yaml		# 重新部署yaml文件
```



![](https://raw.githubusercontent.com/BoomChao/Figure/main/image-20211204111154531.png) 



## 三、K8s 源码解析

k8s.io 在 vendor 下的件夹底下







## 四、边缘计算

边缘计算系统的管理一般可分为集群管理和应用管理

1.集群管理的资源一般不需要用户对其进行操作，修改或者删除，只是在用户需要时对其进行查看

2.应用管理主要是对应用相关的资源进行管理，这些资源包括 Deployment、ReplicaSet、Pod、Service Endpoint、Service Account、Secret ，对这些资源管理操作的相关命令必须掌握





## 五、疑难总结

k8s 中的镜像不建议使用 latest标签，这样每次使用的时候都会尝试到仓库去下载最新的，但是时刻不同最新的版本也肯定是不同的，所以以后镜像能不用 latest 就不要使用 latest 的



Kubectl 也可以在 node 节点上运行：

Kubectl 的运行是需要配置的，它的配置文件是在 `$HOME/.kube` ，如果想要在node节点上运行 kubectl 命令，将 mater 上的 .kube 文件夹整个拷贝到 node 节点即可

```bash
scp -r $HOME/.kube [node1]:$HOME/
```



















