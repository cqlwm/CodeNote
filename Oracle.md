**启动虚拟机后输入的命令**

```shell
systemctl start docker
docker start oracle11g
docker exec -it oracle11g bash
su root
# 密码：helowin
su - oracle
sqlplus /nolog
conn 连接名称/密码
```



# The Network Adapter could not establish the connection

编辑tnsnames.org文件

```shell
tnsping helowin
## TNS-03505：Failed to resolve name

echo $ORACLE_HOME
cd $ORACLE_HOME/network/admin
vi tnsnames.org
```

配置

```
实例名=(DESCRIPTION=
    (ADDRESS=(PROTOCOL=TCP)(HOST=数据库ip)(PORT=1521))
    (CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=实例名))
)
```



# 执行参数

```sql
-- 开始跟踪SQL指令的执行计划和执行的统计信息
SQL> set autotrace on;
-- 行大小
SQL> set linesize 1000;
-- 跟踪该语句执行完成的时间
SQL> set timing on;

SQL> select NAME from T where ID = 106;

NAME
--------------------------------------------------
XZHtB

Elapsed: 00:00:00.01 -- 0.01秒

Execution Plan
----------------------------------------------------------
Plan hash value: 1303508680

------------------------------------------------------------------------------------
| Id  | Operation		    | Name | Rows  | Bytes | Cost (%CPU)| Time	   |
------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT	    |	   |	 1 |	10 |	 1   (0)| 00:00:01 |
|   1 |  TABLE ACCESS BY INDEX ROWID| T    |	 1 |	10 |	 1   (0)| 00:00:01 |
|*  2 |   INDEX UNIQUE SCAN	    | T_PK |	 1 |	   |	 0   (0)| 00:00:01 |
------------------------------------------------------------------------------------

Predicate Information (identified by operation id):
---------------------------------------------------

   2 - access("ID"=106)


Statistics
----------------------------------------------------------
	  1  recursive calls -- 递归
	  0  db block gets   -- 用当前方式从缓冲区高速缓冲中读取的 总块数
	  2  consistent gets -- 逻辑读
	  0  physical reads	 -- 物理读
	  0  redo size
	401  bytes sent via SQL*Net to client
	513  bytes received via SQL*Net from client
	  1  SQL*Net roundtrips to/from client
	  0  sorts (memory)
	  0  sorts (disk)
	  1  rows processed

SQL> select NAME from T where ID = 106;  

NAME
--------------------------------------------------
XZHtB

Elapsed: 00:00:00.00

Execution Plan
----------------------------------------------------------
Plan hash value: 1303508680

------------------------------------------------------------------------------------
| Id  | Operation		    | Name | Rows  | Bytes | Cost (%CPU)| Time	   |
------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT	    |	   |	 1 |	10 |	 1   (0)| 00:00:01 |
|   1 |  TABLE ACCESS BY INDEX ROWID| T    |	 1 |	10 |	 1   (0)| 00:00:01 |
|*  2 |   INDEX UNIQUE SCAN	    | T_PK |	 1 |	   |	 0   (0)| 00:00:01 |
------------------------------------------------------------------------------------

Predicate Information (identified by operation id):
---------------------------------------------------

   2 - access("ID"=106)


Statistics
----------------------------------------------------------
	  0  recursive calls
	  0  db block gets
	  2  consistent gets
	  0  physical reads
	  0  redo size
	401  bytes sent via SQL*Net to client
	513  bytes received via SQL*Net from client
	  1  SQL*Net roundtrips to/from client
	  0  sorts (memory)
	  0  sorts (disk)
	  1  rows processed
```





聚合函数是对结果集的操作。

having在没有形成结果集的时候对数据进行过滤。