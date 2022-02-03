## Github

### 一、基本使用

#### 1.基本理论

Git是一个免费的、开源的分布式版本控制系统，可以快速高效地处理从小型到大型的项目；版本控制是一种记录一个或者若干个文件内容变化，以便来查阅特定版本修订情况的系统

集中化版本控制系统：SVN, CVS,以及Perforce等分布式版本控制系统：Git

Git 与 SVN 的区别

SVN 是集中化版本控制系统，版本库是集中放在中央服务器上的，而工作的时候，用的都是自己的电脑，所以首先要从中央服务器得到最新的版本，然后工作，完成工作后，需要把自己的做完的活推送到中央服务器；集中式版本控制系统必须联网才能工作，对网络带宽要求比较高

Git 是分布式版本控制系统，没有中央服务器，每个人的电脑是一个完整的版本库，工作的时候就不需要联网了，因为版本都在自己的电脑上；协同的方法是这样的：比如说自己在电脑上修改了A文件，其他人也在电脑上修改了文件A，这时你们两之间只需要把各自的修改推送给对方，就可以互相看到对方的修改了

Git是目前世界上最先进的分布式版本控制工具

查看不同级别的配置文件

```bash
#查看系统config
git config --system --list

#查看当前用户(global)配置,这个文件在用户家目录下，是一个隐藏文件 .gitconfig
git config --global --list
```



github 设置免密登录

```bash
#1.生成公私钥(在家目录下的.ssh文件夹下)
ssh-keygen -t rsa -C "1245829528@qq.com"

#2.将公钥拷贝到github上的 SSH keys 下面

#3.测试是否配置成功,只要没有显示 Permission denied 就表示配置成功了
ssh -T git@github.com

#4.之后就可以进行push这些操作了
```





#### 2.本地仓库搭建

##### 1 创建本地仓库

创建本地仓库的方法有两种：1.创建一个全新的仓库	2.克隆全程仓库

1.创建全新的仓库

```Bash
#在当前目录新建一个git代码库
git init
```

执行后就可以看到仅仅在目录项多出一个 .git目录，关于版本的所有信息都在这个目录里

这种方法需要设置提交的目的远程仓库

```bash
#方法一：	
git remote rm origin		#删除之前原始的目的远程仓库
git remote add origin [url] #设置新的远程目的仓库

#方法二：把URL替换成新的URL地址
git remote set-url origin [url]

#方法三：直接修改 .git/config 配置文件
```



2.克隆全程仓库（这种方法不需要设置远程目的仓库）

```Bash
# 克隆一个项目到本地
git clone [url]
```



##### 2 .gitignore 文件

忽略文件：有时我们不想把某些文件纳入版本控制中，比如数据库文件，临时文件，设计文件等

在主目录下新建 ".gitignore"文件，此文件有如下规则：

1.忽略文件中的空行或者以 '#' 开始的行

2.可以使用 Linux 通配符

3.如果名称的最前面有一个感叹号 '!'，表示例外规则，将不被忽略

4.如果名称最前面是一个路径分隔符 '/'，表示要忽略的文件在此目录下，而子目录中的文件不忽略

```bash
#为注释
*.txt		#忽略所有的 .txt 结尾的文件，这样上传的话不会被选中
!lib.txt	#但lib.txt除外
/tmp		#忽略根目录下的tmp中的所有文件
build/		#忽略当前录下的 build 目录下的所有文件，写相对路径就行，不需要写绝对路径，不要写成 ./build/
```



git 分支操作

```bash
git branch 		#显示本地分支
git branch -r 	#显示远程分支
git branch -a	#查看本地和远程分支

git branch -d [branchname]	#删除本地分支
git push origin --delete [branch](注意这个远程分支不要加上origin/)	#删除远程分支(当前分支无法删除,删除当前分支会报错)
```



##### 3 常用 git 命令

git 本地分支关联远程分支

当 git push 的时候如果出现  git push --set-upstream origin，这就是要指定本地分支对应的远程分支

解决方法：git branch --set-upstream-to=origin/remote_branch  your_branch

其中 origin/remote_branch 是你本地分支对应的远程分支；your_branch 是你当前的本地分支



当本地分支和远程分支**同名**时则 git push 的时候就不需要加任何参数

如果本地分支和远程分支不同名则建议修改本地分支名字或者远程分支名字即可

```bash
git branch -m oldName newName		#1.本地分支重命名
git branch -m oldName newName		#2.远程分支重命名
git push --delete lorigin oldName 	#3.删除远程分支
git push origin newName				#4.上传新命名的本地分支
git branch --set-upstream-to origin/newName	#5.把修改后的的本地分支和远程分支关联
```



![image-20220103213011312](C:\Users\12458\AppData\Roaming\Typora\typora-user-images\image-20220103213011312.png)  



git branch 的用法

```bash
# 在本地创建好一个分支后，就可以设定其关联到一个同名的远程分支上，这样的话直接 git push 后面不需要接参数
# 就会默认提交到和你本地分支名同名的远程分支上去
# 建议本地分支和远程分支起相同的名字
git branch hello	#创建一个本地hello分支
git push --set-upstream origin hello	#将本地hello分支关联到远程hello分支,push时远程分支不存在会自动创建

# 使用 git branch [branch_name] 来创建一个分支
# 注意：刚开始使用git时,当 git branch -a 没有显示任何分支时，默认使用上面的命令是创建不出新分支的，前提必须是要有一个分支

git branch -m hello	#默认的分支名为main,这里将其改名为hello分支
git branch -l		#无任何分支
git add .
git commit -m "first"	#会发现生成的新分支名为hello而不是默认的main
```



git push 的用法

```bash
git push 					#什么参数都不加，默认本地分支和远程分支同名
git push origin main		#将本地的main分支推送到同名的远程分支上
git push origin test:main	#将本质的test分支推送到远程的main分支上

git push --set-upstream origin test	#设定本地test分支对应远程的test分支，之后提交可以直接 git push
```



git pull 的用法

```bash
git pull origin main 		#拉取远程分支到本地

#1.如果拉取过程中显示
refusing to merge unrelated histories
原因是两个分支是两个不同的版本，具有不同的提交历史,提交时加上加一句 --allow-unrelated-histories 即可

#2.如果拉取过程中显示conflict, 多半原因是本地文件和远程文件有冲突,解决完冲突直接push即可
```







### 二、报错处理

1.报错 You‘ve successfully authenticated, but GitHub does not provide shell access

弹出这个信息说明系统保存的密码和用户名是正确的，可以连接上；之所有有错误是因为一开始的设置，git clone 时使用的是 https 的网址，应该复制 ssh 的网址；打开项目下的隐藏文件 ./.git，修改 config 文件，将 origin 的https改为ssh网址即可，

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415230749233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTY2MDQxNA==,size_16,color_FFFFFF,t_70) 

替换url为SSH方式

```Bash
url = git@github.com:beyondverage0908/MyMD.git
```

最后保存退出即可

附：查看Github的ssh是否正常连接，使用

```Bash
ssh -T git@github.com
```





2.目录下的 .gitignore 文件不生效的问题

先删除缓存（将所有文件变成 untracked 状态）之后再进行 git 的提交

```bash
git rm -r --cached .	#清除本地缓存
git add .				#将文件提交至暂存区
git commit -m "清除git缓存, 解决gitignore问题"
git push				#提交到远程仓库
```



3.当我们在 github 版本库中发现一个问题后，你在 github 上对它进行了在线的修改；或者你直接在 github 上的某个库中添加 readme 文件或者其他什么文件，但是没有对本地库进行同步。这个时候当你再次有 commit 想要从本地库提交到远程的 github 库中时就会出现push失败的问题。报错如下：

error：failed to push some refs to

解决方法：这个问题是因为远程库与本地库不一致造成的，那么我们把远程库同步到本地库就可以了

```bash
git pull --rebase origin main		
#这条指令的意思是把远程库中的更新合并到本地库中
#rebase的作用是取消掉本地库中刚刚的commit,并把他们接到更新后的版本库中

git push origin main	
#将两个库关联起来即可,直接 git push 也可以
```



附：git pull的作用是从一个仓库或者一个本地的分支拉取并且整合代码

```bash
git pull [<options>] [<repository> [<refspec>]]
```

git pull 相当于 git fetch 跟着一个 git merge FETCH_HEAD；\<repository>是仓库的名字，\<refspec>是分支的名字，如果都不写则会有一个默认值

使用方式

```bash
git pull 				# 按照git branch 设置的默认跟踪的服务器的分支来拉取
git pull origin main	# 拉取远程服务器 origin 的 master 分支
```



4.下午在Git提交的时候发现一个很大问题，我这个提交的文件有的大，超过了100M，git 显示报错超出文件大小限制，我以为把这个文件删除掉就能解决，发现删除了本地文件也没啥用处

![](https://raw.githubusercontent.com/BoomChao/Figure/master/20211026151813488.png)

网上找了好多方法，发现用下面这种方法

```bash
git rm -r --cached . #不起作用
git rm <file_name>   #显示找不到文件，(确实，我这个本地文件已经被删除掉了)
```

思来想去想了好久

出现这种情况是因为提交到了本地仓库但是没有提交到远程仓库，如果一直不先把本地仓库的这个大文件消除，则直接 push 会不断报错，因为这个大文件始终在这个本地仓库中

**解决方案：使用版本回退**

```bash
git reset --soft HEAD^	#soft回退到上一个版本，只回退commit的信息，如果还要提交则直接 commit 即可
git reset --hard HEAD^  #hard彻底回退到某个版本，本地的源码也会变为上一个版本的内容
```



### 三、技术细节

##### 1.git merge 和 git rebase 的区别

**答：**两者都是用来合并分支，准确来说 merge 是用来合并分支，而 rebase 是用来衍合分支

merge 操作会生成一个新的节点，与之前的提交分开显示；而 rebase 操作不会生成新的节点，是将两个分支融合到一个线性的提交

如果想要一个干净的，没有 merge commit 的线性历史树，就选择 rebase

如果想要保留完整的历史记录，并且想要避免重写 commit history 的风险，就选择 merge

比如以下两个分支：

```bash
	 D--E	test
	/
A--B--C--F	master

#使用 git merge test 命令
	 D----E	
	/	   \
A--D--C--F--G	test,master

#使用 git rebase test 命令
A--B--D--E--C--F'			#将两个分支融合成一个线性的提交
```



##### 2.git revert 和 git reset 的区别

**答：**git revert 是用一次新的提交来回滚之前的commit，也就是用一个新提交来消除之前的一个历史提交所作的任何修改；而 git reset 是直接删除指定的 commit

git revert 是让HEAD继续前进，而 git reset 是把 HEAD 向后移动了一下

应用场景：用 git 提交代码的时候，如果发现这一次的 commit 是错误的，那么就有以上两种处理方法























