# MySQL体系结构和存储引擎

## 数据库和实例

- 数据库（database）

  各类型文件的集合；

  如，frm、MYD、MYI、ibd文件。

- 实例（instance）

  操作数据库文件；

  由后台线程以及一个共享内存区组成；

  共享内存可以被运行的后台线程所共享。

<img src="assets/image-20210504102954853.png" alt="image-20210504102954853" style="zoom:67%;" />



## 查看MySQL实例

```shell
# 启动MySQL
./mysqld_safe＆

# 查看启动进程
ps -ef|grep mysqld

mysql 7178 6872 0 10:31 ? 00:00:00 /usr/local/mysql/bin/mysqld
--basedir=/usr/local/mysql
--datadir=/data/mysql
--plugin-dir=/usr/local/mysql/lib/plugin
--user=mysql
--log-error=/data/mysql/mysql.err
--pid-file=/data/mysql/mysql.pid
--socket=/tmp/mysql.sock
--port=3306 # 运行在3306端口上的MySQL实例
```



## MySQL启动时加载配置文件

当启动实例时，MySQL数据库会去读取配置文件，根据配置文件的参数来启动数据库实例。在MySQL数据库中，可以没有配置文件，在这种情况下，MySQL会按照编译时的默认参数设置启动实例。

> Oracle启动会加载spfile参数文件，如果没有参数文件，在启动实例时会提示找不到该参数文件，数据库启动失败。

```shell
mysql --help|grep my.cnf
		order of preference, my.cnf, $MYSQL_TCP_PORT,
/etc/my.cnf /etc/mysql/my.cnf /usr/local/mysql/etc/my.cnf ~/.my.cnf
```

通过命令可以看到，MySQL配置文件的加载顺序是：

`/etc/my.cnf`、`/etc/mysql/my.cnf`、`/usr/local/mysql/etc/my.cnf`、`~/.my.cnf`



## datadir参数

datadir参数指定了数据库所在的路径。

在Linux操作系统下默认datadir为/usr/local/mysql/data，该路径是一个链接。

```shell
# 查看数据库所在路径
SHOW VARIABLES LIKE'datadir'\G;
*************************** 1. row ***************************
Variable_name: datadir
        Value: /data/mysql/
1 row in set (0.00 sec)

# 查看系统datadir下的文件列表
system ls -lh /data/mysql/

# 如果参数未被修改过，那么是存在这样一个超链接的
# lrwxrwxrwx 1 root mysql 16 Aug 6 16:05 data-＞/opt/mysql_data/
# 用户须保证/opt/mysql_data的用户和权限，使得只有mysql用户和组可以访问（通常MySQL数据库的权限为mysql∶mysql）
```



# DDL、DML、DCL、MDL、WAL

## DDL

Data Definition Language 数据库定义语言。如create、procedure、alter、drop。



## DML

Data Manipulation Language 数据操纵语言。如insert、delete、update、select。



## DCL

Data Control Language 数据库控制语言。如grant、deny、revoke等，只有管理员才有这样的权限。



## MDL

Metadata lock，元数据锁，MySQL为实现并发情况下的数据一致性引入的锁机制。



## WAL

WAL: Write-Ahead Logging

先写日志，再写磁盘。具体说，当有一条记录需要更新的时候，InnoDB引擎就会先把记录写到redo log里面，并更新内存，这个时候更新计算完成了。同时InnoDB引擎会在在系统比较空闲的时候，将这个操作记录更新到磁盘里。



WAL机制主要得益于两个方面：

redo log 和 binlog 都是顺序写，磁盘的顺序写比随机写速度要快；

组提交机制，可以大幅度降低磁盘的IOPS消耗。



只要redo log和binlog保证持久化到磁盘，就能确保MySQL异常重启后，数据可以回复。

redo log保证事务的持久性

undo log保证事务的一致性



# MySQL架构

❑连接池组件

❑管理服务和工具组件

❑SQL接口组件

❑查询分析器组件

❑优化器组件

❑缓冲（Cache）组件

❑插件式存储引擎

❑物理文件



## 连接MySQL

在通过TCP/IP连接到MySQL实例时，MySQL数据库会先检查一张`权限视图`，用来判断发起请求的客户端IP`是否允许连接`到MySQL实例。该视图在mysql架构下，表名为user。

```sql
-- 切换到mysql库
USE mysql;
-- 查询user表
SELECT host,user,password FROM user;

-- 通过%以控制IP的访问权限
-- host:192.168.24.%
-- user:root
```





## 命名管道和共享内存







































