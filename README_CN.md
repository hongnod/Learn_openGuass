# 在Debian 10上极简安装 openGauss_2.0.0 



OpenGauss 最近发布了openGauss 2.0, 提供了极简安装版，但官方仅支持Centos 和 OpenEuler. 我在Debian 10上尝试安装OpenEuler版成功，记录如下:
```
1下载 opengauss并解压到/opt/opengauss;
2 设置环境变量，在/etc/profile.d下创建openguass.sh，并添加如下内容：
    export GAUSSHOME=/opt/opengauss
    export PATH=$PATH:$GAUSSHOME/bin:$GAUSSHOME/lib
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$GAUSSHOME/lib;
3 运行 gs_initdb 测试缺少哪些库并安装；
4 运行gs_initdb 以初始化数据库;
5 运行 gs_ctl 启动数据库;
6 运行 gsql 创建并授权登录用户 (注：opengauss 不允许初始化用户登录数据库)
7 测试并配置远程登录等.
8 创建自启动服务: 
  (1) 在/etc/ld.so.conf.d/  创建 opengauss.conf, 并输入 /opt/opengauss/lib
   (2)下载本库的 "opengauss.service" 放入 /usr/lib/systemd/system/ 
        运行 : sudo systemctl enable opengauss
        和 : sudo systemctl start opengauss
  
  
```
以下是安装过程输出:
```
user@debian:~$ echo $PATH
/home/user/bin:/opt/opengauss/bin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
user@debian:~$ echo $LD_LIBRARY_PATH
/opt/opengauss/lib:
user@debian:~$ cd $GAUSSHOME
user@debian:/opt/opengauss$ pwd
/opt/opengauss
user@debian:/opt/opengauss$ gs_initdb -w password -D $GAUSSHOME/data/gsdb --nodename "sgnode" --locale="en_US.UTF-8"
/opt/opengauss/bin/gaussdb: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
no data was returned by command ""/opt/opengauss/bin/gaussdb" -V"
The program "gaussdb" is needed by gs_initdb but was not found in the
same directory as "/opt/opengauss/bin/gs_initdb".
Check your installation.
user@debian:/opt/opengauss$ export echo $IBRARY_PAT
user@debian:/opt/opengauss$ export echo $LIBRARY_PAT
user@debian:/opt/opengauss$ export echo $LIBRARY_PATH
user@debian:/opt/opengauss$ sudo apt search libaio
[sudo] password for user: 
Sorry, try again.
[sudo] password for user: 
Sorting... Done
Full Text Search... Done
libaio-dev/stable 0.3.112-3 amd64
  Linux kernel AIO access library - development files

libaio1/stable 0.3.112-3 amd64
  Linux kernel AIO access library - shared library

user@debian:/opt/opengauss$ sudo apt install libaio1
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  libaio1
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 11.2 kB of archives.
After this operation, 35.8 kB of additional disk space will be used.
Get:1 https://mirrors.tuna.tsinghua.edu.cn/debian buster/main amd64 libaio1 amd64 0.3.112-3 [11.2 kB]
Fetched 11.2 kB in 1s (15.2 kB/s)   
Selecting previously unselected package libaio1:amd64.
(Reading database ... 39424 files and directories currently installed.)
Preparing to unpack .../libaio1_0.3.112-3_amd64.deb ...
Unpacking libaio1:amd64 (0.3.112-3) ...
Setting up libaio1:amd64 (0.3.112-3) ...
Processing triggers for libc-bin (2.28-10) ...
user@debian:/opt/opengauss$ gs_initdb -w password -D $GAUSSHOME/data/gsdb --nodename "sgnode" --locale="en_US.UTF-8"
The files belonging to this database system will be owned by user "user".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

creating directory /opt/opengauss/data/gsdb ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 32MB
creating configuration files ... ok
creating template1 database in /opt/opengauss/data/gsdb/base/1 ... The core dump path is an invalid directory
2021-04-10 19:41:33.241 [unknown] [unknown] localhost 139833673037568 0  [BACKEND] WARNING:  macAddr is 12/692242550, sysidentifier is 796994/3363195586, randomNum is 1356810946
ok
initializing pg_authid ... The core dump path is an invalid directory
ok
setting password ... The core dump path is an invalid directory
ok
initializing dependencies ... The core dump path is an invalid directory
ok
loading PL/pgSQL server-side language ... The core dump path is an invalid directory
ok
creating system views ... The core dump path is an invalid directory
ok
creating performance views ... The core dump path is an invalid directory
ok
loading system objects' descriptions ... The core dump path is an invalid directory
ok
creating collations ... The core dump path is an invalid directory
ok
creating conversions ... The core dump path is an invalid directory
ok
creating dictionaries ... The core dump path is an invalid directory
ok
setting privileges on built-in objects ... The core dump path is an invalid directory
ok
initialize global configure for bucketmap length ... The core dump path is an invalid directory
ok
creating information schema ... The core dump path is an invalid directory
The core dump path is an invalid directory
ok
loading foreign-data wrapper for distfs access ... The core dump path is an invalid directory
ok
loading foreign-data wrapper for hdfs access ... The core dump path is an invalid directory
ok
loading foreign-data wrapper for log access ... The core dump path is an invalid directory
ok
loading hstore extension ... The core dump path is an invalid directory
ok
loading foreign-data wrapper for MOT access ... The core dump path is an invalid directory
ok
loading security plugin ... The core dump path is an invalid directory
ok
update system tables ... The core dump path is an invalid directory
ok
vacuuming database template1 ... The core dump path is an invalid directory
ok
copying template1 to template0 ... The core dump path is an invalid directory
ok
copying template1 to postgres ... The core dump path is an invalid directory
ok
freezing database template0 ... The core dump path is an invalid directory
ok
freezing database template1 ... The core dump path is an invalid directory
ok
freezing database postgres ... The core dump path is an invalid directory
ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run gs_initdb.

Success. You can now start the database server of single node using:

    gaussdb -D /opt/opengauss/data/gsdb --single_node
or
    gs_ctl start -D /opt/opengauss/data/gsdb -Z single_node -l logfile

user@debian:/opt/opengauss$ gs_ctl start -D /opt/opengauss/data/gsdb -Z single_node -l logfile
[2021-04-10 19:42:03.404][685][][gs_ctl]: gs_ctl started,datadir is /opt/opengauss/data/gsdb 
[2021-04-10 19:42:03.445][685][][gs_ctl]: waiting for server to start...
...........
[2021-04-10 19:42:24.524][685][][gs_ctl]:  done
[2021-04-10 19:42:24.524][685][][gs_ctl]: server started (/opt/opengauss/data/gsdb)
user@debian:/opt/opengauss$ gsql -d postgres
gsql ((openGauss 2.0.0 build 78689da9) compiled at 2021-03-31 21:04:06 commit 0 last mr  )
Non-SSL connection (SSL connection is recommended when requiring high-security)
Type "help" for help.

postgres=# create role root with login password 'password';
ERROR:  Password must contain at least three kinds of characters.
postgres=# create role root with login password 'Passsword!1';
CREATE ROLE

postgres=# grant all privileges to root;
ALTER ROLE
postgres=# \q
```
因og的用户加密与pg不同, pg的客户端不能直接使用. 可以用HeidiSQL(来自: https://github.com/ljlilzu/ljlilzu.github.io/tree/master/course):
```
1 下载本库的 libpq-og.dll ;
2下载HeidiSQL;
3 将 libpq-og.dll 放入 HeidiSQL 的根目录;
4 启动 HeidiSQL 配置选择 libpq-og.dll
5 enjoy
```

测试结论：
1、占用CPU、内存稍高
![ksnip_20210412-144036](https://user-images.githubusercontent.com/6680267/114372769-80aa6000-9bb4-11eb-8ed3-38ed7e94d890.png)
2、客户端工具比较缺乏，HeidiSQL 可以变通连上，但还是存在问题，自家的Datastudio连接不上，显示内部错误，而且是Jave开发的（对不起，作为用户，我真的很讨厌Java）

