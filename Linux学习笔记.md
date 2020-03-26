# Linux学习笔记



> ## 安装Linux系统

### 系统分区

>  主分区

最多只能有四个

>扩展分区

* 最多只能有一个
* 主分区+扩展分区最多四个
* 不能写入数据，只能包含逻辑分区

>逻辑分区

 

### 格式化（高级格式化）

格式化：指将分区格式化成不同的文件系统。
那什么是文件系统呢？
文件系统：指操作系统用于明确存储设备或分区上的文件的方法和数据结构：即在存储设备上组织文件的方法。就好比一个教室，同学们的坐的位置总是与桌子凳子排列的方式有关系。桌子，凳子怎么摆放，就导致了同学坐的位置在哪里。文件系统存放数据也是这么个道理。


又称逻辑格式化 ，指根据用户选定的文件系统（如：FAT16、FAT32等）：

按照选定的文件系统的规则，将硬盘分割成等大小的数据块（block）；

如何查找存储的block？--inode 

block和inode 



### 硬件设备文件名

硬盘文件名和分区设备文件名：

分区文件名是在硬盘文件名后加上分区编号

例如：硬盘文件名：hd***x***  ***n***(x:a,b,c;n:1,2,3)

驱动器标识符为“hdx”,其中“hd”表明分区所在设备的类型，这里是指ide硬盘了。“x”为盘号（a为基本盘，b为基本从属盘，c为辅助主盘，d为辅助从属盘）,“”代表分区，前四个分区用数字1到4表示，它们是主分区或扩展分区，从5开始就是逻辑分区。

例，hda3表示为第一个ide硬盘上的第三个主分区或扩展分区,hdb2表示为第二个ide硬盘上的第二个主分区或扩展分区。

1,2,3,4这四个分区号，只能给主分区和扩展分区使用；逻辑分区永远都是从5开始

linux中所有的硬件设备都是文件--一切皆文件

逻辑分区永远都是从5开始



### 挂载

window分配盘符

根分区和swap分区（交换分区）

前面讲过，Linux 系统中“一切皆文件”，所有文件都放置在以根目录为树根的树形目录结构中。在 Linux 看来，任何硬件设备也都是文件，它们各有自己的一套文件系统（文件目录结构）。

因此产生的问题是，当在 Linux 系统中使用这些硬件设备时，只有将Linux本身的文件目录与硬件设备的文件目录合二为一，硬件设备才能为我们所用。合二为一的过程称为“挂载”。

将设备文件系统与linux文件系统连接起来。

挂载，指的就是将设备文件中的***顶级目录***连接到 Linux 根目录下的某一目录（最好是空目录），访问此目录就等同于访问设备文件。



***fdisk***

****

```bash
fdisk -l #查看系统分区信息
m ：显示菜单和帮助信息
a ：活动分区标记/引导分区
d ：删除分区
l ：显示分区类型
n ：新建分区
p ：显示分区信息
q ：退出不保存
t ：设置分区号
v ：进行分区检查
w ：保存修改
x ：扩展应用，高级功能
```

***df***

```bash
df [file/dir] # 查看磁盘使用情况和文件系统挂载位置
The df utility displays statistics about the amount of free disk space on the specified filesystem or on the filesystem of which file is a part.  Values are displayed in 512-byte per block counts.  If neither
     a file or a filesystem operand is specified, statistics for all mounted filesystems are displayed (subject to the -t option below).

```

***parted***

```bash
parted命令是由GNU组织开发的一款功能强大的磁盘分区和分区大小调整工具
```





> ## 虚拟机网络

 桥接
nat
hostonly



***修改网卡地址***

`cd /etc/sysconfig/network-scripts/` 

***需要在配置文件里开启虚拟机网卡***

永久生效的修改往往都是要修改配置文件，命令行形式一般只在当前进程生效  



> ## 注意事项

linux是区分大小写的；
Linux中所有的内容都以文件形式保存（包括硬件，与window区分）；
Linux不依靠文件扩展名（.tar/.doc等）区分文件，依靠操作权限区分文件类型；扩展名是方便管理人员/用户对文件进行操作（如解压）；

Linux所有的存储设备都必须手动挂载之后用户才能使用，包括硬盘、U盘和光盘； 一些拥有图形化操作界面的Linux系统（如：Ubuntu），在存储设备接入后会自动挂载（类似window）



> ## 服务器管理和维护建议

bin：二进制文件
linux中的用户：单用户（类似于window中的安全模式），用较少的权限进行系统维护；
如：/bin下的系统命令，单用户可以使用；而/usr/bin下的系统命令，单用户是不可以使用；

普通用户；

超级用户（root）；





linux各个文件目录的作用

usr： Unix Software Resource



> ## Linux常用的命令

**.filename**在Linux中为隐藏文件



用户类别：所有者，所属组，其他人



Linux文件 没有创建文件这个概念



```bash
文件类型
- # 文件
d # 文件夹
l # 软链接

文件权限
rw- r-- r--
u   g   o
#r:读 w: 写 x:执行
```



```bash
ls -i #显示 inode （文件id）
```

ctrl + L ： 清屏

文件显示指令：more/less

head -n [num] filename 

tail 



**链接文件**

软链接（.soft）和硬链接(.hard)

```bash
ln -s [srcfile] [tarfile] # 创建软链接文件
```

 软连接：类似window的快捷方式

```bash
ln [srcfile] [tarfile] # 创建硬链接文件
```





若一个 inode 号对应多个文件名，则称这些文件为硬链接。换言之，硬链接就是同一个文件使用了多个别名。硬链接可由命令 link 或 ln 创建。如下是对文件 oldfile 创建硬链接。

由于硬链接是有着相同 inode 号仅文件名不同的文件，因此硬链接存在以下几点特性：

- 文件有相同的 inode 及 data block；
- 只能对已存在的文件进行创建；
- 不能交叉文件系统进行硬链接的创建；
- 不能对目录进行创建，只可对文件创建；
- 删除一个硬链接文件并不影响其他有相同 inode 号的文件。
- 源文件和硬链接的文件同步更改





***一个inode可以对应多个文件，一个文件只有一个inode***

***软连接可以跨分区使用，硬链接不可以跨分区使用***



软链接与硬链接不同，若文件用户数据块中存放的内容是另一文件的路径名的指向，则该文件就是软连接。软链接就是一个普通文件，只是数据块内容有点特殊。软链接有着自己的 inode 号以及用户数据块（见 [图 2.](https://www.ibm.com/developerworks/cn/linux/l-cn-hardandsymb-links/#fig2)）。因此软链接的创建与使用没有类似硬链接的诸多限制：

- 软链接有自己的文件属性及权限等；
- 可对不存在的文件或目录创建软链接；
- 软链接可交叉文件系统；
- 软链接可对文件或目录创建；
- 创建软链接时，链接计数 i_nlink 不会增加；
- 删除软链接并不影响被指向的文件，但若被指向的原文件被删除，则相关软连接被称为死链接（即 dangling link，若被指向路径文件被重新创建，死链接可恢复为正常的软链接）。



### 修改文件（夹）权限chmod

```bash
chomd [{ugoa}{+-=}{rwx}[文件或目录]] -R# 递归修改
# 使用数字
# r=4 w=2 x=1
chmod [mode=421] [文件或目录]
# example
chmod 777 filename -R
```

***添加/删除用户***

```bash
useradd username
password username # 或 passwd username
```

***添加组/添加组成员***

```bash
groupadd groupname # 创建新的组
```



***修改文件的所有者/所属组***

```bash
chown [owner] [file/dir]
chgrp [group name] [file/dir]
```

*缺省组/默认组*：文件在创建的时候创建者就是默认组



***查看存在的分组/用户***

```bash
cat /etc/group # 查看分组
cat /etc/passwd # 查看用户
```



***umask：查看新建文件的默认权限***

```bash
umask -S
# u=rwx,g=rx,o=rx新建文件夹默认权限
# u=rw,g=r,o=r新建文件默认权限，不具备可执行权限（x）


#直接使用umask ： 掩码取反
umsak 
# 0022 ： 第一个0是特殊权限
```





***切换用户***

```bash
su username #切换普通用户
sudo -i #超级用户
```



***rw***

对文件来说，是读写的权限

对文件夹来说，是有显示文件列表（list）和其目录下文件的增删
也就是说：
**当一个用户对一个文件夹用写的权限的时候，对一个不可写文件（无操作权限），是可以直接删除文件**



### 文件搜索指令：find/locate

* find

```bash
find [搜索范围] [匹配条件]

# example
find /etc -name init
# -name 区分大小写
# -iname 不区分大小写
# *任意个数字符 ？单个字符

# -size +/-value （+：大于，-：小于）

# -user [username]

# -type 根据文件类型查找
 
f:文档 d:文件夹 l:软连接文件


```

```bash
# 按时间搜索
find /etc -ctime -5 # 5分钟内被修改过属性的文件 +：超过
# -amin 访问时间 access 
# -cmin 文件属性 change
# -mmin 文件内容 modify
```

```bash
# -exec:对查找结果执行操作
# -ok：与exec功能相似，但会逐条询问
find /etc -name *init -exec ls {} \;
# {} 表示查询的结果；
# \ 表示执行语句的结束；
# ; 表示整一条命令的结束；

# -inum 根据i节点查找
```

```bash
# 逻辑字符： 
-a:and 
-o:or
```

一个数据块=512字节=0.5k

window下文件搜索工具：everything



* locate

是搜索一个数据库（/var/lib/locatedb），这个数据库中含有本地所有文件信息。Linux系统自动创建这个数据库，并且每天自动更新一次，所以使用locate命令查不到最新变动过的文件。为了避免这种情况，可以在使用locate之前，先使用**updatedb**命令，手动更新数据库。



locate 无法查找`/tmp/`下的文件



* which

搜索命令所在目录以及别名信息

* whereis 

搜索命令所在的目录以及帮助文档路径

帮助文档带有‘“1”--命令的帮助文档

帮助文档带有“5”--配置文件的帮助文档

* whatis

查看命令的简介

* apropos

查看配置文件的简介





### 在文件内容查找：grep

```bash
grep mysql /root/install.log
-i 不区分大小写
-v 排除指定字符 ^#:排除以‘#’开始的行
```





### 帮助命令 :man

***查看命令的帮助信息或查看配置文件的帮助信息***

 

### 用户管理：who/w

查看当前登陆的用户：

* 本地用户 tty

* 远程用户pts

uptime:查看服务器运行时间



### 压缩解压命令

***gzip/gunzip->.gz***

* 只能压缩文件，不能压缩目录；

* 不保留原文件；



***tar + gzip->.tar.gz***

```bash
## 压缩
-c #打包
-v #显示详细信息
-f #指定文件名
-z #打包并压缩 tar -cfvz rst.tar.gz srcfile/srcdir

## 解压缩
-x #
-f #
-z #
```



***zip/unzip***

```bash
zip [-r 压缩文件夹] dstfile srcfile/srcdir
```





### 网络工具

* write
  * 同一个主机的某一用户发送信息
* wall
  * 给同一个主机的所有用户发送广播信息

* ping
* ifconfig
  * 查看和设置网卡信息
  * lo网卡：回环网卡



`last`:查看登陆记录

***# traceroute 显示数据报到主机间的路径***

```bash

traceroute www.baidu.com
```



***# netstat***

```bash
     netstat [-AaLlnW] [-f address_family | -p protocol]
     netstat [-gilns] [-v] [-f address_family] [-I interface]
     netstat -i | -I interface [-w wait] [-c queue] [-abdgqRtS]
     netstat -s [-s] [-f address_family | -p protocol] [-w wait]
     netstat -i | -I interface -s [-f address_family | -p protocol]
     netstat -m [-m]
     netstat -r [-Aaln] [-f address_family]
     netstat -rs [-s]
```

### lsof

```bash
lsof -i:端口号 #查看端口占用进程
```



***关机指令***

```bash
shutdown 
-c # 取消前一个关机指令 
-h # 定时关机 
-r # 重启
## shutdown指令执行时，保存数据到硬盘
```



`sync`:手动同步内存数据到硬盘

`NFS服务`： 

```bash
init [num]
# 切换系统运行级别
0 关机
1 单用户 # 类似window安全模式，启动最小核心程序
2 不完全多用户，不含NFS
3 完全多用户
4 未分配
5 图形界面
6 重启

runlevel # 查看当前的系统运行级别
# 设置系统默认启动的 运行级别 ubuntu
cat /etc/inittab # 根据文档提示运行命令
```

```bash
# 退出登陆
logout
```



#### tc

网络流量控制工具

```bash
# https://blog.csdn.net/duanbeibei/article/details/41250029/
```







> ## Vim--文本编辑器



```bash
# 其他的编辑器

gedit
nano

```





### Vim常用操作

* 命令模式
  * 删除光标所在字符：x 或 [number key]  x
  * 删除光标所在行： dd 
  * 删除第n行：[number] dd
  * 复制粘贴：
    * yy复制 p粘贴到下一行 P粘贴到上一行
    * [number]yy复制多行
  * 剪切：dd（与删除行相同）
  * 替换：r单次替换 R持续替换
  * 恢复上一次操作： u

* 插入模式
  * a 光标所在字符后插入
  * A 光标所在字符行尾插入
  * i 光标所在字符前插入
  * I 光标所在字符行首插入
  * o 光标下插入新行
  * O 光标上插入新行

* 编辑模式
  * 添加行号：	```set nu```
  * 取消行号：```set nomu```
  * 行尾： $ 
  * 行首： 0
  * 删除： n1,n2d 
  * 替换：%s/old/new/g
  * 保存：w or w /path/filename（另存为）
  * 保存并推出：wq  or wq!(强制保存)

### vim使用技巧

```:r! <cmd>```  导入命令输出的结果到文本

```r /path/filename``` 导入文本内容

> 定义快捷操作

***map***

map ctrl+v  + 快捷键    + 执行命令

```bash
# 注释该行 ctrl + p 
:map ^p I#<ESC> 

# 添加邮箱地址
:map ^H ixxxx@xx.com 

# 删除快捷键
umap <key>
```



***替换***

```bash
:n1,n2S/<gre>/<new str>/g 
```

***定义变量：ab***

```bash

ab var value
```



***保存编辑设置内容***

```bash
vim /homw/<usr>/.vimrc #若无此文件，则需要新建
# /root/.vimrc
#将需要的编辑设置书写在该文档保存
```





> ## 软件包管理

 

***源代码包和二进制包***



> 源码包

优点：

* 开源
* 可以自由选择所需功能
* 编译安装，更适合自己的系统
* 卸载方便

缺点：

* 安装步骤繁琐
* 编译时间过长
* 编译异常新手难以解决



> 二进制包

二进制包（系统默认包）：源代码包经过编译的机器语言包 

centos：RPM包；

ubuntu：deb包；



优点：

* 包管理简单，通过命令实现安装、升级、查询和卸载的操作；
* 安装速度比源代码快；

缺点：

* 无法看源代码
* 功能选择不如源码包灵活
* 依赖性：包与包间存在依赖



***rpm包管理***

```bash 
httpd-2.2.15-15.el6.entos.1.i686.rpm
# httpd 软件包名	
# 2.2.15 软件版本
# 15 软件发布的次数
# el6.centos 适合的Linux的平台
# i686 适合的硬件平台
# rpm 包扩展名
```

***RPM包依赖性***

* 树形依赖: a>b>c
* 环形依赖：a>b>c >a
  * rpm -ivh 软件包A 软件包B ...

* 模块依赖：
  www.rpmfind.net

```bash
rpm -ihv <package name>
# -i install
# -v verbose  显示详细信息
# -h hash 显示进度
# --nodeps 不检测依赖性
```

查询

```bash
rpm -q <package name> # 查询是否安装某包
rpm -qa <package name> # 显示所有的包
```



#### yum在线管理

yum源可以是本地的

