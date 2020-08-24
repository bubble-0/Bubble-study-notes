### 查看用户的所有数据
```
select * from user_source
```

### 查看用户下的表
```
SELECT * FROM user_tables;
```

### 查看用户表的注释
```
SELECT * FROM user_tab_comments;
```

### 查看用户表的字段注释
```
SELECT * FROM user_col_comments
```

### 查询oracle中所有用户信息
```
select  * from dba_users;
```

### 查询当前用户信息
```
select * from dba_ustats;
```

### 修改用户密码
```
alter user 用户名 identified by 密码;
```

### 查看视图的名称
```
select view_name from user_views;
```

### 查看同义词的名称
```
select * from user_synonyms;
```

### 查看当前用户权限：
```
select * from user_role_privs;
```

### 赋予DBA权限
```
grant dba to user;
```

### 赋予建表权限
```
grant create table to st_new
```

### 查看表空间及其数据文件
```
SELECT * FROM dba_data_files;
```

### 创建临时表空间 
```
create temporary tablespace tablespace_name --表空间名称
tempfile '-------'--oracle文件路径
size 32m 
autoextend on 
next 32m maxsize 2048m 
extent management local; 
```

### 创建数据表空间 
```
create tablespace tablespace_name --test_data临时表空间名称
logging 
datafile '-------'--oracle文件路径
size 32m 
autoextend on 
next 32m maxsize 2048m 
extent management local; 
```

### 创建用户并指定表空间 
```
create user username identified by password --username用户名称  
default tablespace test_data --默认用户表空间
temporary tablespace test_temp; --默认临时表空间
```

### 改变用户的表空间
```
alter user 用户名 default tablespace 表空间名称;
```
### 创建索引并指定索引表空间 
```
create index demo_index on demo (ID) TABLESPACE  faml_idx;
```

### 给用户授予权限 
```
grant connect,resource to username; 
grant dba to username
```

### 给予用户使用表空间的权限
```
alter user 用户名 quota unlimited on 表空间;
```

### 找回误删除的表
- 查看回收站中表
```
select object_name,original_name,partition_name,type,ts_name,createtime,droptime from recyclebin;
```

- 恢复表
```
flashback table 表  名 to before drop;
flashback table "BIN$b+XkkO1RS5K10uKo9BfmuA==$0" to before drop;
```

- 开启行移动功能 
```
alter table 表名 enable row movement
```

- 恢复表数据
```
flashback table 表名 to timestamp to_timestamp(删除时间点 ,'yyyy-mm-dd hh24:mi:ss')
```

- 关闭行移动功能 ( 千万别忘记 )
```
alter table 表名 disable row movement
``` 
### 表资源被锁
```
SELECT t2.username,
       t2.sid,
       t2.serial#,
       t3.object_name,
       t2.osuser,
       t2.machine,
       t2.program,
       t2.logon_time,
       t2.command,
       t2.lockwait,
       t2.saddr,
       t2.paddr,
       t2.taddr,
       t2.sql_address,
       t1.locked_mode
  FROM v$locked_object t1, v$session t2, dba_objects t3
 WHERE t1.session_id = t2.sid
   AND t1.object_id = t3.object_id
 ORDER BY t2.logon_time;

-- 杀死被锁的session
alter system kill session 'sid,serial#';   
```

### 删除空的表空间，但是不包含物理文件
```
drop tablespace tablespace_name;
```
### 删除非空表空间，但是不包含物理文件
```
drop tablespace tablespace_name including contents;
```
### 删除空表空间，包含物理文件
```
drop tablespace tablespace_name including datafiles;
```
### 删除非空表空间，包含物理文件
```
drop tablespace tablespace_name including contents and datafiles;
```
### 如果其他表空间中的表有外键等约束关联到了本表空间中的表的字段，就要加上CASCADE CONSTRAINTS
```
drop tablespace tablespace_name including contents and datafiles CASCADE CONSTRAINTS;
```
### 说明： 删除了user，只是删除了该user下的schema objects，是不会删除相应的tablespace的。
```
drop user username cascade
```
### 启动数据库
```
STARTUP
```

### 关闭数据库
```
1.SHUTDOWN NORMAL
发出该命令后，任何新的连接都将再不允许连接到数据库。在数据库关闭之前，Oracle将等待目前连接的所有用户都从数据库中退出后才开始关闭数据库。
采用这种方式关闭数据库，在下一次启动时不需要进行任何的实例恢复。但需要注意的是，采用这种方式，也许关闭一个数据库需要几天时间，或者更长。
2.SHUTDOWN IMMEDIATE
这是常用的一种关闭数据库的方式，想很快地关闭数据库，但又想让数据库干净的关闭，常采用这种方式。
当前正在被Oracle处理的SQL语句立即中断，系统中任何没有提交的事务全部回滚。如果系统中存在一个很长的未提交的事务，采用这种方式关闭数据库也需要一段时间（该事务回滚时间）。
系统不等待连接到数据库的所有用户退出系统，强行回滚当前所有的活动事务，然后断开所有的连接 用户。
3.SHUTDOWN TRANSACTIONAL
该选项仅在Oracle 8i后才可以使用。该命令常用来计划关闭数据库，它使当前连接到系统且正在活动的事务执行完毕，运行该命令后，任何新的连接和事务都是不允许的。
在所有活动的事务完成后，数据库将和SHUTDOWN IMMEDIATE同样的方式关闭数据库。
4.SHUTDOWN ABORT
这是关闭数据库的最后一招，也是在没有任何办法关闭数据库的情况下才不得不采用的方式，一般不要采用。如果下列情况出现时可以考虑采用这种方式关闭数据库。
1、 数据库处于一种非正常工作状态，不能用shutdown normal或者shutdown immediate这样的命令关闭数据库;
2、 需要立即关闭数据库；
3、 在启动数据库实例时遇到问题；
所有正在运行的SQL语句都将立即中止。所有未提交的事务将不回滚。Oracle也不等待目前连接到数据库的用户退出系统。
下一次启动数据库时需要进行实例恢复，因此，下一次启动可能比平时需要更多的时间。
```
### 启动实例
```
lsnrctl start
```
### 查看实例状态
```
lsnrctl status
```
### 导出DMP包  compress=y (压缩DMP包)

- 将数据库ORACLE完全导出
```
exp 用户名/密码@实例名  file= full=y
```
- 将数据库中user1用户与,user2用户的表导出
```
exp 用户名/密码@实例名  file= owner=(user1, user2)
```
- 将数据库中的表T_USER、T_ROLE导出
```
exp 用户名/密码@实例名  file= tables=(table1, table2)
```
- 将数据库中的表table1中的字段filed1以"00"打头的数据导出
```
exp 用户名/密码@实例名 file=d:\daochu.dmp tables=(table1) -- query=\" where filed1 like '00%'\"
```
### 导入DMP包
```
imp 用户名/密码@实例名  file=  ignore=y -- 忽略已存在的表

imp 用户名/密码@实例名  file=  tables=(table1) 
```

### 查看字符集
```
select * from nls_database_parameters where parameter='NLS_CHARACTERSET';

select userenv('language') from dual;
```
### 快速生成实体类
```
SELECT '/* ' || comments    || ' **/' , 'private String ' || lower(column_name)  || ';'FROM User_Col_Comments WHERE TABLE_NAME = 'TA_FMS_FEE_AVG'
```

### Oracle对SQL将进行几个步骤的处理过程：
- 语法检查(syntax check): 检查此sql的拼写是否语法。
- 语义检查(semantic check): 诸如检查sql语句中的访问对象是否存在及该用户是否具备相应的权限。
- 对sql语句进行解析(prase): 利用内部算法对sql进行解析，生成解析树(parse tree)及执行计划(execution plan)。
- 执行sql，返回结果(execute and return)
   

### exists用法
- 判断另外的表中是否包含某个表的外键记录，使用外键相关联
- exists是判断exits后面的sql语句是否为真,若为真则整个sql句子成立,否则没有任何记录。
   
### regexp_like用法
- and REGEXP_LIKE(字段名, '(匹配串1|匹配串2|...)') //全模糊匹配
- and REGEXP_LIKE(字段名, '^(匹配串1|匹配串2|...)') ;//右模糊匹配
- and REGEXP_LIKE(字段名, '(匹配串1|匹配串2|...)$') ;//左模糊匹配


### over函数用法
```
SELECT  BUSI_DATE, count(*) OVER(PARTITION BY BUSI_DATE)  FROM    TA_CONFIRM
```

### 调用带有out的存储过程
```
declare
  status  INTEGER;
  message VARCHAR2(50);
begin
  p_print_org_info('1', '1', '1', status, message);
end;
```

### 使用for循环遍历游标
```
CREATE OR REPLACE PROCEDURE p_print_org_info(IC_CENDAT  VARCHAR2,
                                             IC_JOBID   VARCHAR2,
                                             IC_SEQ     VARCHAR2,
                                             IC_STATUS  out INTEGER,
                                             IC_MESSAGE out VARCHAR2) IS

  V_INFO VARCHAR2(200); --程序执行提示信息
  CURSOR V_ORG IS --声明显式游标  
    SELECT * FROM tp_pim_org;
  V_COL V_ORG%rowtype;

BEGIN

  FOR V_COL IN V_ORG LOOP
    DBMS_OUTPUT.PUT_LINE(V_COL.Orgidt || ' -- ' || V_COL.orgnam);
  END LOOP;

END p_print_org_info;
```

### 批量修改
```
update aml_customer_accout aca
     set fdac =
         (select fdac
            from TMP_AC_ACCOUNT_CHANGE
           where scac = aca.scac
             and rownum = 1),
         sfin =
         (select sfin
            from TMP_AC_ACCOUNT_CHANGE
           where scac = aca.scac
             and rownum = 1),
         stac =
         (select stac
            from TMP_AC_ACCOUNT_CHANGE
           where scac = aca.scac
             and rownum = 1)
   where aca.scac in (select distinct scac from TMP_AC_ACCOUNT_CHANGE);
```
### 索引相关 
- 重建索引
```
/**
索引本身有长度限制。小于（blocksize-192）*80%
在8k的表空间中：被索引列的长度不可以超过  （8192-192）*0.8=6400
在16k的表空间中：被索引列的长度不可以超过  （16384-192）*0.8=12593.6
rebuild online的过程中，oracle会创建索引组织表IOT
**/
CREATE OR REPLACE PROCEDURE CRS_INDEX_UPDATE(
 T_NAME VARCHAR2, --表名
 T_TYPE VARCHAR2, --任务类型
 T_OWNER VARCHAR2
) AS

CURSOR V_NAME IS
SELECT * FROM ALL_INDEXES a WHERE TABLE_NAME=UPPER(T_NAME) AND OWNER=UPPER(T_OWNER)
AND NOT EXISTS(SELECT 1 FROM ALL_OBJECTS b WHERE a.OWNER=b.OWNER and a.TABLE_NAME = b.OBJECT_NAME and b.TEMPORARY = 'Y');

V_SQL VARCHAR2(1000);
V_TYPE VARCHAR2(1000);
V_SNO NUMBER;
V_SQLCODE VARCHAR2(4000);
V_SQLERRM VARCHAR2(4000);
V_INDEX_LEN NUMBER;
V_OL_INDEX_LIMIT_LEN NUMBER DEFAULT 3000;
V_ONLINE VARCHAR2(10);

BEGIN
     FOR I IN V_NAME
     LOOP
       SELECT NVL(MAX(SNO),0)+1 INTO V_SNO FROM CRS_INDEX_LOG;
         INSERT INTO CRS_INDEX_LOG(SNO,TABLE_NAME,INDEX_NAME,BEGINTIME)
         VALUES(V_SNO,T_NAME,I.INDEX_NAME,SYSDATE);
         COMMIT;

         V_INDEX_LEN := 0;
         V_ONLINE := 'ONLINE';

         SELECT SUM(A.COLUMN_LENGTH)
           INTO V_INDEX_LEN
           FROM USER_IND_COLUMNS A
          WHERE A.INDEX_NAME = I.INDEX_NAME;

         IF V_INDEX_LEN >= V_OL_INDEX_LIMIT_LEN THEN
           V_ONLINE := '';
         END IF;

         IF T_TYPE = 0 THEN --关闭索引
            V_SQL :='ALTER INDEX ' || I.INDEX_NAME || ' UNUSABLE';
            V_TYPE :='关闭';
         ELSE
            V_SQL :='ALTER INDEX ' || I.INDEX_NAME || ' REBUILD ' || V_ONLINE; --开启索引
            V_TYPE :='开启';
          END IF;

      EXECUTE IMMEDIATE V_SQL;
         UPDATE CRS_INDEX_LOG SET TYPE=V_TYPE,ENDTIME=SYSDATE WHERE SNO=V_SNO;
       COMMIT;
     END LOOP;


EXCEPTION WHEN OTHERS THEN
    V_SQLCODE :=SQLCODE;
    V_SQLERRM :=SQLERRM;
    SELECT NVL(MAX(SNO),0)+1 INTO V_SNO FROM CRS_INDEX_LOG;
    INSERT INTO CRS_INDEX_LOG(SNO,TABLE_NAME,BEGINTIME,ENDTIME)
    VALUES(V_SNO,'索引维护出现异常'||V_SQLCODE||V_SQLERRM||V_SQL,SYSDATE,SYSDATE);
  COMMIT;

/*grant all privileges to new_crs*/

END CRS_INDEX_UPDATE;
```

### 查询表空间使用情况
```
SELECT UPPER(F.TABLESPACE_NAME) "表空间名",
       D.TOT_GROOTTE_MB "表空间大小(M)",
       D.TOT_GROOTTE_MB - F.TOTAL_BYTES "已使用空间(M)",
       TO_CHAR(ROUND((D.TOT_GROOTTE_MB - F.TOTAL_BYTES) / D.TOT_GROOTTE_MB * 100,
                     2),
               '990.99') "使用比",
       F.TOTAL_BYTES "空闲空间(M)",
       F.MAX_BYTES "最大块(M)"
  FROM (SELECT TABLESPACE_NAME,
               ROUND(SUM(BYTES) / (1024 * 1024), 2) TOTAL_BYTES,
               ROUND(MAX(BYTES) / (1024 * 1024), 2) MAX_BYTES
          FROM SYS.DBA_FREE_SPACE
         GROUP BY TABLESPACE_NAME) F,
       (SELECT DD.TABLESPACE_NAME,
               ROUND(SUM(DD.BYTES) / (1024 * 1024), 2) TOT_GROOTTE_MB
          FROM SYS.DBA_DATA_FILES DD
         GROUP BY DD.TABLESPACE_NAME) D
 WHERE D.TABLESPACE_NAME = F.TABLESPACE_NAME
 ORDER BY 4 DESC;
```

### 新增数据文件，并且允许数据文件自动增长
```
ALTER TABLESPACE app_data ADD DATAFILE
'D:\ORACLE\PRODUCT\10.2.0\ORADATA\EDWTEST\APP04.DBF' SIZE 50M
AUTOEXTEND ON NEXT 5M MAXSIZE 100M;
```
### 手工改变已存在数据文件的大小
```
ALTER DATABASE DATAFILE 'D:\ORACLE\PRODUCT\10.2.0\ORADATA\EDWTEST\APP02.DBF'  
RESIZE 100M;  
```




