# windows下mysql安装教程
## 安装包下载
- 下载地址：
```
https://dev.mysql.com/downloads/mysql/ 
```
选择对应版本下载即可

## 安装教程
### 配置环境变量
- 变量名:MYSQL_HOME 变量值:安装包解压的路径

### 生成data文件
- 以管理员身份运行cmd
- 进入%MYSQL_HOME%\bin 目录下执行命令
```
mysqld --initialize-insecure --user=mysql
```
执行完毕之后会在%MYSQL_HOME%下生成data目录

### 安装mysql(安装mysql的windows服务)
- 在刚才的目录下执行命令
```
mysqld -install
```
执行完毕会提示
> Service successfully installed.

### 启动服务
- 在刚才的目录下执行命令启动mysql
```
net start MySQL
```
执行完毕会提示
> MySQL 服务正在启动  
> MySQL 服务已经启动成功

- 假如要关闭mysql
```
net stop mysql
```

### 登录MySQL(之前没有设置密码,直接回车登录即可)
- 执行命令
```
mysql -u root -p
```

### 查询用户密码
```
select host,user,authentication_string from mysql.user;
```

### 设置或者修改mysql密码
```
use mysql;

ALTER USER 'root'@'localhost' IDENTIFIED BY '1234qwer';

flush privileges;
```

### 一些常见的命令
```
show databases;               --查看所有的数据库
flush privileges;             --刷新权限,很重要!
use <数据库名>;               --使用某个数据库
create database <数据库名>;   --创建数据库 
net start mysql               --创建数据库
net stop mysql                --关闭数据库
mysql -u root -p;             --使用密码登录数据库
drop database <数据库名>;     --删除某个数据库
commit;                       --提交事务
show tables;                  --查看数据库中有多少的表
desc <tablename>              --查看表结构
source <文件路径>             --导入.sql文件
select user();                --查看用户
```

