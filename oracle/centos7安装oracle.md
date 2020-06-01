# centos7下安装oracle11g

## 1.依赖检查
``` 
-- 依赖检查
rpm -q make binutils gcc libaio glibc compat-libstdc++-33 elfutils-libelf elfutils-libelf-devel glibc-common glibc-devel glibc-headers gcc-c++ libaio-devel libgcc libstdc++ libstdc++-devel sysstat unixODBC unixODBC-devel ksh

-- 检查完毕之后使用yum命令安装所需要的依赖
yum install -y xxx

-- 如果没有yum则使用rpm
rpm -ivh xxx

-- 一个比较头疼的包elfutils-libelf-devel安装的时候会相互依赖,两个解决方法
-- 1.yum install elfutils-libelf-devel*
-- 2.rpm安装,加--nodeps,如果还不能装，加--force 
```

## 2.创建Oracle用户和相应的属组
``` 
groupadd oinstall
groupadd dba
useradd -g oinstall -G dba oracle
passwd oracle    #密码暂定为oracle
```

## 3.用root用户，创建目录修改属组和权限
```
mkdir -p /u01/app/oracle
chown -R oracle:dba /u01/app/oracle
chmod -R 777 /u01/app/oracle
mkdir /u01/app/oraInventory
chown -R oracle:dba /u01/app/oraInventory
chmod -R 775 /u01/app/oraInventory

cd /u01/app/oracle
mkdir tmp
chown -R oracle:dba tmp
chmod 777 tmp

--上传安装包到tmp，解压之后
chown -R oracle:dba database
```

## 4.修改主机名,配置hosts
```
-- 修改主机名
hostnamectl set-hostname xxx

-- 检查
hostnamectl

-- 添加主机名映射
vi /etc/hosts

10.8.22.102 amlweb01
```

## 5.关闭防火墙,selinux
```
-- 关闭防火墙
systemctl stop firewalld.service

-- 查看状态
systemctl status firewalld.service

-- 关闭selinux
setenforce 0

```

## 6.修改内核文件
```
vi /etc/sysctl.conf

-- 添加以下内容
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv4.conf.all.rp_filter = 1
#设置最大打开文件数
fs.file-max = 6815744
fs.aio-max-nr = 1048576
#共享内存的页数，Linux共享内存页大小为4KB，8G内存设置为：4GB/4KB=8*1024*1024*1024/4KB = 1048576
kernel.shmall = 1048576
#最大共享内存，官方文档建议是内存的1/2，例如：8G内存则设置为：4*1024*1024*1024
kernel.shmmax = 4294967296
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
#可使用的IPv4端口范围
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max= 4194304
net.core.wmem_default= 262144
net.core.wmem_max= 1048576

--执行生效
sysctl -p

```

## 7.修改用户的限制文件 
```
vi /etc/security/limits.conf

-- 添加
oracle    soft    nproc    2047
oracle    hard    nproc    16384
oracle    soft    nofile    1024
oracle    hard    nofile    65536
oracle    soft    stack    10240
```

## 8.修改/etc/pam.d/login文件
```
vi /etc/pam.d/login

-- 添加
session required  /lib64/security/pam_limits.so
session required   pam_limits.so

```

## 9.Oracle用户环境变量
```
-- 使用oracle用户登录
su - oracle

vi /home/oracle/.bash_profile

-- 添加
ORACLE_BASE=/u01/app/oracle
export ORACLE_BASE
ORACLE_HOME=$ORACLE_BASE/product/11.2.0
export ORACLE_HOME
ORACLE_SID=ora11g
export ORACLE_SID
export ORACLE_TERM=vt100
export ORACLE_OWNER=oracle
LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib:/usr/local/lib
export LD_LIBRARY_PATH
CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
CLASSPATH=$CLASSPATH:$ORACLE_HOME/network/jlib
export CLASSPATH
export NLS_LANG="SIMPLIFIED CHINESE_CHINA.ZHS16GBK"
export PATH=/usr/bin:/bin:/usr/local/bin:/usr/bin/X11:/usr/bin/X11R6
PATH=$PATH:$ORACLE_HOME/bin
export PATH

-- 执行生效
source /home/oracle/.bash_profile

exit
```

## 修改系统版本设置
``` 
vi /database/stage/cvu/cv/admin/cvu_config

CV_ASSUME_DISTID=OEL7
```

## 复制安装参数文件（包含3个，分别是数据库安装，监听安装，实例安装）
```
cp -R /u01/app/oracle/tmp/database/response/ /home/oracle/

vi /home/oracle/response/db_install.rsp

-- 找到下方参数进行修改
oracle.install.option=INSTALL_DB_SWONLY --装装数据库软件
ORACLE_HOSTNAME=localhost --主机名 可硬通过 hostname 查看
UNIX_GROUP_NAME=dba  --指定oracle inventory 目录的所有者，通常会是oinstall 或者dba
INVENTORY_LOCATION=/u01/app/oraInventory --指定产品清单oracle inventory 目录的路径
SELECTED_LANGUAGES=en,zh_CN  --指定数据库语言，可以选择多个，用逗号隔开
ORACLE_HOME=/u01/app/oracle/product/11.2.0 --设置ORALCE_HOME 的路径
ORACLE_BASE=/u01/app/oracle --设置ORALCE_BASE 的路径
oracle.install.db.InstallEdition=EE --安装数据库软件的版本（企业版（EE），标准版(SE)，标准版(SEONE) ）
oracle.install.db.DBA_GROUP=dba
oracle.install.db.OPER_GROUP=dba
DECLINE_SECURITY_UPDATES=true

```

## 开始安装数据库软件
```
su oracle
cd /u01/app/oracle/tmp/database/

./runInstaller -silent -responseFile /home/oracle/response/db_install.rsp -ignorePrereq

--安装如果提示交换空间不够，需加下交换空间
--按照提示用root用户执行
sh /u01/app/oracle/product/11.2.0/root.sh 
sh /u01/app/oraInventory/orainstRoot.sh

-- 根据提示的日志文件查看安装日志
tail -f xxx 

```

## 安装监听
```
netca /silent /responseFile /home/oracle/response/netca.rsp

-- 检查
netstat -tnulp | grep 1521
```

## 配置安装实例文件,安装实例
```
vi /home/oracle/response/dbca.rsp

GDBNAME= "ora11g"
SID ="ora11g"
SYSPASSWORD= "password"
SYSTEMPASSWORD= "password"
SYSMANPASSWORD= "password"
DBSNMPPASSWORD= "password"
#DATAFILEDESTINATION=$ORACLE_BASE/oradata #该参数配置注释掉，不修改，使用默认的，否则建库会报错。
RECOVERYAREADESTINATION=$ORACLE_BASE/flash_recovery_area
CHARACTERSET= "ZHS16GBK"  --这个字符集要设置好，不然后面导入数据时发现乱码，还要改 ZHS16GBK  AL32UTF8
TOTALMEMORY= "6553"

-- 安装实例
dbca -silent -responseFile /home/oracle/response/dbca.rsp
```

## 开机自启动
```
-- 使用oracle用户即可
vi  /etc/oratab

-- 修改
ora11g:/u01/app/oracle/product/11.2.0/dbhome_1:Y

-- oracle用户可能没有权限,如果没有则使用root用户
vi /etc/rc.d/rc.local

-- 添加
su oracle -lc "/u01/app/oracle/product/11.2.0/bin/lsnrctl start"
su oracle -lc "/u01/app/oracle/product/11.2.0/bin/dbstart /u01/app/oracle/product/11.2.0/db_1"

```

## 检查,启动数据库
```
-- 查看实例状态
lsnrctl status

-- 连接数据库
sqlplus /nolog

connect /as sysdba

-- 连接之后启动数据库
startup
```