- ** cat /etc/shells  查看当前系统所支持的shell
- ** echo $SHELL 当前 Linux 的默认 Shell
- ** 现代 Linux系统在启动时会自动创建几个虚拟控制台（Virtual Console），其中一个供图形桌面程序使用，其他的保留原生控制台的样子，
- ** 按下快捷键Ctrl + Alt + Fn(n=2,3,4,5,6)可以从图形界面模式切换到控制台模式，按下Ctrl + Alt +F1可以从控制台模式再切换回图形界面模式。

## 查看当前用户
whoami

## 创建用户
useradd -m 用户账号

## 删除用户
userdel -r 用户名

## 用户密码
passwd 用户名 
超级用户可以使用passwd命令为普通用户设置或修改用户口令。
用户也可以直接使用该命令来修改自己的口令，而无需在命令后面使用用户名。

## 添加、删除组账号：
groupadd、groupdel

## 修改用户所在组
usermod -g 目标用户组 用户名

## 压缩文件
- ** zip包
zip 文件名.zip 文件1 文件2

- ** tar命令
tar -x   解压
    -c   压缩
    -z   gzip属性
    -j   bzip属性
    vf   f为必须 
    压缩后的文件名字    压缩的文件1  2  3  
tar -czvf  文件.gzip  文件
tar -xzvf  文件.gzip
tar -cjvf  文件.jzip  文件
tar -xjvf  文件.jzip


## 文件目录
每个目录下都有“.”“..”这样的两个隐藏文件，“.”表示本文件，“..”表示父目录
• -：普通文件  
• d: 目录文件
• b: 块设备
• c: 字符设备
• l: 符号链接文件  
• p: 管道文件pipe
• s: 套接字文件sock

列出非目录的文件项，然后是每一个目录中的“可显示”文件
ls
-a 显示所有文件，包含隐藏文件
-A 显示所有文件，包含隐藏文件，但不包含.及..
-l 显示为long format（长格式），列出文件的类型、权限、链接数、owner、group、大 小，时间，名字
-U 按存放顺序排序显示
-S 以文件大小排序显示，默认从大到小 -r后，从小到大

- ** 环境变量
显示所有的环境变量
env 

- ** 查看环境变量PATH值
export

- ** 使环境变量立即生效
source .bash_profile 

- ** 单独查看某环境变量 
echo $XXX XXX环境变量

- ** 设置一个新的环境变量
export 环境变量名称=XXX
环境变量名称=XXX

- ** 查看本地shell变量
set
unset 清除环境变量
unset $TEST #删除环境变量TEST 

readonly命令设置只读变量 
export HELLO=hello
readonly HELLO

## 1.Linux的变量种类 
### 按变量的生存周期来划分，Linux变量可分为两类： 
- ** 1.1 永久的：需要修改配置文件，变量永久生效。 
- ** 1.2 临时的：使用export命令声明即可，变量在关闭shell时失效。

### 2.设置变量的三种方法 
- ** 2.1 在/etc/profile文件中添加变量【对所有用户生效(永久的)】 
用VI在文件/etc/profile文件中增加变量，该变量将会对Linux下所有用户有效，并且是“永久的”。 
例如：编辑/etc/profile文件，添加CLASSPATH变量 
vi /etc/profile 
export CLASSPATH=./JAVA_HOME/lib;$JAVA_HOME/jre/lib 
注：修改文件后要想马上生效还要运行# source /etc/profile不然只能在下次重进此用户时生效。

- ** 2.2 在用户目录下的.bash_profile文件中增加变量【对单一用户生效(永久的)】 
用VI在用户目录下的.bash_profile文件中增加变量，改变量仅会对当前用户有效，并且是“永久的”。 
例如：编辑guok用户目录(/home/guok)下的.bash_profile 
$ vi /home/guok/.bash.profile 
添加如下内容： 
export CLASSPATH=./JAVA_HOME/lib;$JAVA_HOME/jre/lib 
注：修改文件后要想马上生效还要运行$ source /home/guok/.bash_profile不然只能在下次重进此用户时生效。

- ** 2.3 直接运行export命令定义变量【只对当前shell(BASH)有效(临时的)】 
在shell的命令行下直接使用[export 变量名=变量值] 定义变量，该变量只在当前的shell(BASH)或其子shell(BASH)下是有效的，
shell关闭了，变量也就失效了，再打开新shell时就没有这个变量，需要使用的话还需要重新定义。

## linux查看CPU内核信息命令：
cat /proc/cpuinfo

## 查看内核信息
- ** cat /proc/version

## 查看自带的JDK的相关信息
- ** rpm -qa | grep gcj
### 删除
- ** rpm -e --nodeps
java-1.4.2-gcj-compat-1.4.2.0-40jpp.115

## 卸载openjdk
### 1) 查看openjdk的包
- ** rpm -qa | grep openjdk
- ** rpm -qa | grep java
### 2）卸载
- ** rpm -qa| grep openjdk|xargs rpm -e --nodeps
- ** rpm -e --nodeps java-*-gcj-*.x86_64

## 配置jdk
/etc/profile
JAVA_HOME=/usr/share/jdk1.5.0_05
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME
export PATH
export CLASSPATH
重新登录即可。

## 查看磁盘空间

## 关机
- ** shutdown -h now  #立刻关机重启，工作中常用
- ** shutdown -h +1    #1分钟后关机

## 重启
- ** reboot　　　　　　#工作中常用
- ** shutdown -r now      #工作中常用

## 日期相关
- ** date命令：查看当前时间
- ** date -s  完整日期时间（YYYY-MM-DD hh:mm[:ss]）：修改日期、时间
- ** hwclock -w 将时间写入bios避免重启失效。当我们进行完 Linux 时间的校时后，还需要以 hwclock 来更新 BIOS 的时间，因为每次重新启动的时候，系统会重新由 BIOS 将时间读出来，所以， BIOS 才是重要的时间依据吶。

## 修改主机名
- ** hostnamectl set-hostname xxx

## 查看防火墙状态
- ** systemctl status firewalld
## 关闭防火墙
- ** systemctl stop firewalld
## 打开防火墙
- ** systemctl start firewalld
## 永久关闭防火墙
- ** systemctl disable firewalld
## 查看已经开放的端口：
- ** firewall-cmd --list-ports  
## 开启端口
- ** firewall-cmd --zone=public --add-port=80/tcp --permanent  
命令含义：
–zone #作用域
–add-port=80/tcp #添加端口，格式为：端口/通讯协议
–permanent #永久生效，没有此参数重启后失效
## 关闭端口
-** firewall-cmd --remove-port=3306/udp --permanent

## 重新加载防火墙
firewall-cmd --reload

 
## 关闭防火墙及自启动
service iptables stop
chkconfig iptables off

## ssh免密登录
### 在主节点上生成无密码的密钥对
- ** ssh-keygen -t rsa
- ** 一路回车
### 查看系统生成的公钥私钥对
- **ls /root/.ssh
- **可以看到如下的文件
.  ..  id_rsa  id_rsa.pub
### 将生成的公钥私钥对id_rsa.pub发送到其他的节点上，需要输入yes，再输入其他节点的密码。
- ** ssh-copy-id -i ~/.ssh/id_rsa.pub 192.168.30.201
### 测试：
- ** 在主节点上sshhslave1，正常情况下，不需要密码就能直接登陆进去了。

## 关于SSH
- ** SSH(远程连接工具)连接原理：ssh服务是一个守护进程(demon)，系统后台监听客户端的连接，ssh服务端的进程名为sshd,负责实时监听客户端的请求(IP 22端口)，包括公共秘钥等交换等信息。
- ** ssh服务端由2部分组成： openssh(提供ssh服务)    openssl(提供加密的程序)
### SSH的工作机制
- ** 服务器启动的时候自己产生一个密钥(768bit公钥)，本地的ssh客户端发送连接请求到ssh服务器，服务器检查连接点客户端发送的数据和IP地址，确认合法后发送密钥(768bits)给客户端，此时客户端将本地私钥(256bit)和服务器的公钥(768bit)结合成密钥对key(1024bit),发回给服务器端，建立连接通过key-pair数据传输。 
- ** 

## 如果rpm安装软件时提示
- ** 警告：MySQL-server-5.6.45-1.el6.x86_64.rpm: 头V3 DSA/SHA1 Signature, 密钥 ID 5072e1f5: NOKEY
- ** 这是由于yum安装了旧版本的GPG keys造成的 
- ** 在命令后面安装 --force --nodeps  

## RPM
- ** rpm -e 卸载    -e --allmatches --nodeps 强制卸载
- ** rpm -qa 列出所有安装过的包
- **  --force --nodeps  忽略报错强制安装

## centos7 查看占用端口进程
netstat -lnp|grep 8080

## 添加软连接
- ** ln -s [源文件] [目标文件]
- ** rm -rf ./链接名 删除的是软链接
- ** rm -rf ./链接名/ 删除的则是文件和你的软链接

## vi命令常用参数
- ** Ctrl+f：向文件尾翻一屏 
- ** Ctrl＋b；向文件首翻一屏 
- ** gg 跳到第一行，
- ** G 跳到最后一行；
- ** dG 清除文件；
- ** :s/要替换的字符/要替换成的字符  替换当前行的字符
- ** :%s/要替换的字符/要替换成的字符 替换当前行的字符

## echo命令
- ** 覆盖文件内容 echo "Raspberry" > test.txt
- ** 追加文件内容 echo "Intel Galileo" >> test.txt

## linux中的&&和&，|和||
- **  &  表示任务在后台执行，如要在后台运行redis-server,则有  redis-server &

- ** && 表示前一条命令执行成功时，才执行后一条命令 ，如 echo ‘1‘ && echo ‘2’    

- ** | 表示管道，上一条命令的输出，作为下一条命令参数，如 echo ‘yes’ | wc -l

- ** || 表示上一条命令执行失败后，才执行下一条命令，如 cat nofile || echo “fail”    





