### 一、shell

##### 1.设置环境变量

需要写入环境变量配置文件才能在一系列shell中生效

```bash
/etc/profile	#在这个文件写入会对所有用户生效
~/.bashrc		#在这个文件写入只会对当前用户生效
```



##### 2.为网卡添加临时IP

```bash
# 为网卡添加临时IP
ifconfig ens33:1 192.168.109.201 netmask 255.255.255.0 		# 方法一
ip addr add 192.168.109.201/24 dev ens33:1 					# 方法二

# 删除方法
ip addr delete 192.168.109.201 dev ens33:1
```



##### 3.curl 命令

curl 是一种命令行工具，作用是发出网络请求，然后获取数据，显示在"标准输出"（stdout）上面

```bash
# curl -O 下载文件
curl -O http://a.b.c/a.tar 把a.tar下载到本地了
curl -o newName.tar http://a.b.c/a.tar 把a.tar下载下来顺便改名为newName.tar

# 有的网址是自动跳转的。使用 -L 参数，curl 就会跳转到新的网址
curl -L www.sina.com
```



##### 4.grep命令和xargs命令的区别 

```bash
echo '--help' | cat		# 输出 --help
# 这里就相当于echo命令的输出通过管道重定向到cat的输入了,然后cat从标准输入中读取待处理的文本

echo '--help' | xargs cat  #等价于 cat --help
# 这里xargs将接受字符串'--help'作为xargs的一个命令参数来运行cat命令
# xargs就是用来产生某个命令的参数
```

Linux命令可以从两个地方读取要处理的内容，一个是通过命令行参数，一个是通过标准输入





### 二、用户管理

用户配置文件

chown 改变文件的所有者和所属组		chown  [选项]  [所有者]\[:][所属组]  [文件名]

```bash
chown chao:chao /usr/test.txt		#将test.txt文件所有者和所属组改变为chao和chao
```









### 三、虚拟机管理

VMware在默认安装完成之后，会创建三个虚拟的网络环境：VMnet0、VMnet1 和 VMnet8 (就是 vmware 提供的三种不同的虚拟交换机)；

**其类型分别为：桥接网络，Host-only 和 NAT**

桥接：表示你的虚拟机与本地主机是同一级别，因此你的虚拟机与主机必须在同一个网段，虚拟机才能上网。虚拟机可以上外网，虚拟机之间也可以通信。如果需要使用 ftp,ssh，就需要使用这种网络环境

NAT：网络地址转换（NET Address Transform），这种情况，虚拟机与主机就不是一个级别的，虚拟机相当于寄宿在了主机中。这种情况虚拟机之间可以通信，虚拟机也可以访问局域网内的其他主机。但是局域网内的其他主机就不能访问虚拟机，而虚拟机可以通过主机来访问公网

Host-only：此时的虚拟机与主机存在一个与外界完全封闭虚拟网络中，虚拟机也只能与主机通信。如果虚拟机需要一个绝对安全的内网环境，就可以使用这种




动态获取IP命令：`sudo dhclient`



### 四、服务搭建

##### 1.nfs 服务器的搭建

1.服务端  创建共享目录，修改配置文件，重启服务

```bash
sudo apt install nfs-kernel-server
 
vim /etc/exports 
#/ 10.3.0.0/16(rw,sync,insecure,no_subtree_check,no_root_squash)
#服务器端共项目录  -权限   -network   客户端ip
#/share -rw (-network 10.3.45.37)这个括号中内容可以不加
```

2.客户端 挂载服务器共享目录

```bash
apt-get install nfs-common 	#下载nfs命令

sudo mount 10.7.20.132:/home/chao/Documents/nfs /mnt 	#将服务器上的共享目录挂在到我客户机的/mnt目录
tree /mnt					#就可以查看到共享的文件
umount /mnt					#取消挂载
```































