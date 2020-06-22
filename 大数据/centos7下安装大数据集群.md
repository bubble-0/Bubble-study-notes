# 环境准备
## 操作系统环境准备
### 设置主机名
- vi /etc/sysconfig/network  
- hostnamectl set-hostname XXX  
  修改对应的配置  
  主节点 hmaster  
  子节点 hslave1  
  ...

### 设置主机ip
- vi /etc/sysconfig/network-scripts/ifcfg-ens33  
  BOOTPROTO=static   网络桥接改为static  
  IPADDR=192.168.111.101   设置IP  
  NETMASK=255.255.255.0   子网掩码   
  GATEWAY=192.168.1.1   默认网关  
  DNS1=192.168.111.1   首选DNS服务器  
  ONBOOT=yes   开机自启动
- systemctl restart network  
  重启生效

### 设置hosts映射
- vi /etc/hosts  
  192.168.111.101 hmaster  
  192.168.111.102 hslave1

### 永久关闭防火墙
- systemctl disable firewalld

### 关闭selinux
- vi /etc/selinux/config  
  修改selinux 为disabled
  重启系统 reboot

## 安装文件准备
### CM5.12
- http://archive.cloudera.com/cm5/cm/5

### CDH5.12
- http://archive.cloudera.com/cdh5/parcels/5.7.0/

### spark2
- http://archive.cloudera.com/spark2/parcels/2.1.0.cloudera1/

### 其他
- https://pkgs.org/

## 程序依赖包安装
### 安装运行环境
-  rpm -ivh openssh-clients-7.4p1-16.el7.x86_64.rpm
-  rpm -ivh autogen-libopts-5.18-5.el7.x86_64.rpm
-  rpm -ivh ntp-4.2.6p5-28.el7.centos.x86_64.rpm  
-  rpm -ivh ntpdate-4.2.6p5-28.el7.centos.x86_64.rpm  
-  rpm -ivh  perl-5.16.3-293.el7.x86_64.rpm perl-libs-5.16.3-293.el7.x86_64.rpm perl-Module-Pluggable-4.8-3.el7.noarch.rpm perl-Pod-Escapes-1.04-293.el7.noarch.rpm perl-Pod-Simple-3.28-4.el7.noarch.rpm perl-version-0.99.07-3.el7.x86_64.rpm --force --nodeps
-  rpm -ivh net-tools-2.0-0.24.20131004git.el7.x86_64.rpm
-  rpm -ivh libtirpc-0.2.4-0.15.el7.x86_64.rpm
-  rpm -ivh rpcbind-0.2.0-47.el7.x86_64.rpm 
-  rpm -ivh psmisc-22.20-15.el7.x86_64.rpm

### 配置SSH
- ssh-keygen -t rsa  
  一路回车  
  ls /root/.ssh  可以看到刚才生成的文件
- ssh-copy-id -i ~/.ssh/id_rsa.pub 主机名  
  复制刚才生成的文件  
  ssh 主机名就可以不需要密码登录了，主节点子节点都要配置，记得还要给自己复制一次

### NTP时间同步
- cat /etc/passwd  
  检查NTP用户是否存在如果不存在手动创建NTP用户  
  groupadd ntp  
  useradd -g ntp ntp
- vi /etc/ntp.conf  
  注释掉所有restrict和server
- **主节点增加如下两行**  
  server 127.127.1.0  
  fudge 127.127.1.0 stratum 10  
  **保存之后重启**  
  systemctl  restart ntpd
- 子节点增加  
  server XXX (主节点主机名)  
  关闭NTP服务  
  systemctl  stop  ntpd  
  手动同步时间  
  ntpdate hmaster
- systemctl  start  ntpd  
  所有节点打开NTP服务
- chkconfig ntpd on  
  所有节点设置NTP服务自启动
- ntpstat
  子节点运行检查,直到子节点显示同步(大约五分钟)

### 安装JDK
- 查找系统自带的安装包  
  rpm -qa | grep openjdk  
  rpm -qa | grep java
- rpm -e --nodeps 上面查找到的包名
- 删除可能存在的注册表信息  
  alternatives --display libjavaplugin.so.x86_64  
  alternatives --remove libjavaplugin.so.x86_64 /usr/lib64/IcedTeaPlugin.so
- tar -xzvf jdk-8u201-linux-x64.tar.gz  
  解压文件
- mv jdk1.8.0_201/ ../jdk8  
  重命名并移动
- 设置环境变量  
  vi /etc/profile  
  添加以下信息  
```
  export JAVA_HOME=/opt/jdk8  
  export PATH=$JAVA_HOME/bin:$PATH  
```
  :wq 保存退出
- 执行生效  
  source /etc/profile
- 验证  
  java -version 

### MYSQL安装(仅主节点)
- rpm -ivh numactl-2.0.9-7.el7.x86_64.rpm 
- groupadd mysql
- useradd -g mysql mysql
- 删除系统自带的mysql、mysql-libs、 mariadb-libs  
  rpm -qa | grep mysql  
  rpm -qa | grep mysql-libs  
  rpm -qa | grep mariadb-libs  
  rpm -e --nodeps 上面查找到的包名
- rpm -ivh   mysql-community-common-5.7.28-1.el7.x86_64.rpm  mysql-community-libs-5.7.28-1.el7.x86_64.rpm mysql-community-client-5.7.28-1.el7.x86_64.rpm   mysql-community-server-5.7.28-1.el7.x86_64.rpm  
==***如果上面mysql是用rpm一个个安装的话，会有问题
如果启动报错，可能要修改目录权限==  
chown -R mysql:mysql /var/lib/mysql/*  
- 手工初始化  
  mysqld --initialize --user=mysql
- cat /var/log/mysqld.log  
  查看刚才初始化的密码
- systemctl start mysqld  
  启动mysql
- **修改初始化的密码**  
  **1.使用刚才初始化的密码登录**  
  mysql -u root -p  
  输入之前的密码登录之后  
  use mysql;  
  update user set authentication_string=password("你的新密码") where user="root";  
  ==**mysql新版本的用户表的密码字段为authentication_string之前是password**==  
  flush privileges;  
  刷新权限  
  exit;  
  > **2.这一步如果之前安装了mysql卸载从新安装或者初始化失败的情况下执行mysqld --initialize --user=mysql 可能会报错，需要从新初始化**  
  1.查看mysql配置文件,删除配置目录  
  vi /etc/my.cnf  
  2.删除datadir的目录后,重新执行  
  mysqld --initialize --user=mysql  
  3.重启mysql  
  systemctl  restart  mysqld  
  4.输入密码登录，可能会出现密码不正确的情况，此时需要跳过mysql验证  
  vi /etc/my.cnf  
  5.在[mysqld]加入  
  skip-grant-tables  
  6.重新启动mysql  
  systemctl  restart  mysqld  
  7.登录之后按照上方步骤修改root用户密码
- 修改mysql配置文件  
  vi /etc/my.cnf  
- 在[mysqld]中增加:  
  skip-name-resolve  
  port=3306  
  default-storage-engine=InnoDB   
  lower_case_table_names=1   
  max-connections=3000  
  character_set_server=utf8  
- 在[mysql]中增加([mysql] 不存在的话手工添加,一定要添加到最下方！)  
  default-character-set=utf8
- 重启数据库  
  systemctl  restart  mysqld  
- mysql自启
  systemctl  enable  mysqld  
- 允许远程登录，并创建CM、hive数据库  
  mysql -u root -p  
- GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '1234qwer' WITH GRANT OPTION;  
  允许远程连接  
  ==**如果提示需要重新设置密码的话:  
  SET PASSWORD = PASSWORD('1234qwer');  
  或者  
  ALTER USER 'root'@'localhost' IDENTIFIED BY '1234qwer' PASSWORD EXPIRE NEVER;  
  然后  
  flush privileges;  
  重新登陆**==  
- FLUSH PRIVILEGES;
- create database cm;
- create database hive;
- exit;  
#### 截止到mysql安装完毕之后请打虚拟机快照保存

### 安装CM
#### 解压安装包
- tar -zxvf cloudera-manager-centos7-cm5.15.2_x86_64.tar.gz -C  ../

#### 创建cm用户
- useradd --system --home=/opt/cm-5.15.2/run/cloudera-scm-server/ --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm 

#### 复制mysql驱动
- mkdir -p /usr/share/java
- cp mysql-connector-java.jar /usr/share/java

#### 主节点初始化mysql数据库
- /opt/cm-5.15.2/share/cmf/schema/scm_prepare_database.sh mysql cm -h localhost root 1234qwer --scm-host localhost cm cm cm

#### 修改agent的配置
- vi /opt/cm-5.15.2/etc/cloudera-scm-agent/config.ini  
  将第三行server_host的值修改为主节点的主机名

#### 准备CDH文件，放到/opt/cloudera/parcel-repo/目录下面
- cp -r /opt/installcentos7/CDH/*  /opt/cloudera/parcel-repo

#### 修改验证文件名称
- cd /opt/cloudera/parcel-repo/
- mv CDH-5.15.2-1.cdh5.15.2.p0.3-el7.parcel.sha1   CDH-5.15.2-1.cdh5.15.2.p0.3-el7.parcel.sha

#### 创建agent目录
- mkdir -p /opt/cm-5.15.2/run/cloudera-scm-agent

#### 启动cm，主节点启动server、agent，子节点只要agent	
- /opt/cm-5.15.2/etc/init.d/cloudera-scm-server start 
- /opt/cm-5.15.2/etc/init.d/cloudera-scm-agent start 

#### 打开浏览器访问 192.168.111.102:7180 进入管理页面  
  按照提示一直下一步
  

  

 





  
    


