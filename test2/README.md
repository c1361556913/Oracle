## 实验二 用户管理 - 掌握管理角色、权根、用户的能力，并在用户之间共享对象。
 *2016级软工3班*  
 *陈荣杰*  
 *201610414302*    
#### 以system登录到pdborcl，创建角色con_res_view_crj和用户new_user_crj，并授权和分配空间：  
用户名：new_user_crj
<pre>
  [oracle@deep02 ~]$ sqlplus system/123@pdborcl
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 09:22:10 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
上次成功登录时间: 星期三 10月 24 2018 09:21:33 +08:00
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
SQL> CREATE ROLE con_res_view_crj;
角色已创建。
SQL> GRANT connect,resource,CREATE VIEW TO con_res_view_crj;
授权成功。
SQL> CREATE USER new_user_crj IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
用户已创建。
SQL> ALTER USER new_user_crj QUOTA 50M ON users;
用户已更改。
SQL> GRANT con_res_view_crj TO new_user_crj;
授权成功。
SQL> exit
</pre>    

#### 新用户new_user_crj连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。
<pre>
  [oracle@deep02 ~]$ sqlplus new_user_crj/123@pdborcl
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 09:34:00 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> show user;
USER 为 "NEW_USER_CRJ"
SQL> CREATE TABLE mytable (id number,name varchar(50));
表已创建。
SQL> INSERT INTO mytable(id,name) VALUES(1,'zhang');
已创建 1 行。
SQL> INSERT INTO mytable(id,name) VALUES(2,'wang');
已创建 1 行。
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
视图已创建。
SQL> SELECT * FROM myview;
NAME
--------------------------------------------------------------------------------
zhang
wang
SQL> GRANT SELECT ON myview TO hr;
授权成功。
SQL> exit
</pre>  

#### 用户hr连接到pdborcl，查询new_user_crj授予它的视图myview
<pre>
[oracle@deep02 ~]$ sqlplus hr/123@pdborcl
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 09:43:18 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
上次成功登录时间: 星期三 10月 24 2018 09:42:49 +08:00
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> SELECT * FROM new_user_crj.myview;
NAME
--------------------------------------------------------------------------------
zhang
wang
SQL> exit
</pre>  
#### 查看数据库使用情况  
<pre>
 SQL> SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files WHERE tablespace_name='USERS';
TABLESPACE_NAME
--------------------------------------------------------------------------------
FILE_NAME
--------------------------------------------------------------------------------
        MB     MAX_MB AUTOEXTEN
---------- ---------- ---------
USERS
/home/oracle/app/oracle/oradata/orcl/pdborcl/SAMPLE_SCHEMA_users01.dbf
         5 32767.9844 YES

SQL> SELECT a.tablespace_name "表空间名",total/1024/1024 "大小MB",
  2  free/1024/1024 "剩余MB"，(total - free)/1024/1024 "使用MB"，
  3  Round((total - free )/total,4)*100 "使用率%"
  4  from (SELECT tablespace_name,Sum(bytes)free
  5  FROM dba_free_space group BY tablespace_name)a,
  6  (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
  7  group BY tablespace_name)b
  8  where a.tablespace_name = b.tablespace_name;
表空间名
--------------------------------------------------------------------------------
    大小MB     剩余MB     使用MB    使用率%
---------- ---------- ---------- ----------
SYSAUX
       630     38.875    591.125      93.83
USERS
         5      .4375     4.5625      91.25
SYSTEM
       270     3.5625   266.4375      98.68

表空间名
--------------------------------------------------------------------------------
    大小MB     剩余MB     使用MB    使用率%
---------- ---------- ---------- ----------
EXAMPLE
  1281.875      62.25   1219.625      95.14
</pre>
### 总结分析  
本次实验使用的是Git Bash连接老师的Oracle数据库来完成的。本次实验中，首次创建用户，管理角色、权限等。  
按照实验步骤要求，能够一定程度上理解实验，帮助达到实验目的，通过本次实验，自己能够简单进行用户管理，  
熟悉了管理角色、权限、用户的方法。
