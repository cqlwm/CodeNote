# 安装Docker

**安装**

```shell
yum install docker -y
```



**启动docker**

```shell
# 启动docker
systemctl start docker
# 停止docker
systemctl stop docker
# 重启docker
systemctl restart docker
```



# 阿里镜像配置

```shell
sudo mkdir -p /etc/docker

vim /etc/docker/daemon.json
{
  "registry-mirrors": [
    "http://hub-mirror.c.163.com",
    "https://0duvlw2o.mirror.aliyuncs.com"
  ]
}

sudo systemctl daemon-reload
sudo systemctl restart docker
```



# 拉取镜像

```shell
docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```



# 创建容器

```shell
docker run -d -p 1521:1521 --name oracle11g \
registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```

> 如果空间不足可以清理数据：
> 删除所有dangling数据卷（即无用的Volume）：
>
> docker volume rm $(docker volume ls -qf dangling=true)



# 启动容器

```shell
# 启动
docker start oracle11g
# 停止
docker stop oracle11g
```



# 容器配置

**进入容器**

```shell
docker exec -it oracle11g bash
```



**切换到root 用户**

```shell
su root
# 密码：helowin
```



**配置profile**

```shell
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
export ORACLE_SID=helowin
export PATH=$ORACLE_HOME/bin:$PATH

# 更新profile
source /etc/profile
```



**创建软连接**

```shell
ln -s $ORACLE_HOME/bin/sqlplus /usr/bin
```



# 配置Oracle



**切换oracle 用户**

```shell
su - oracle
```



**登录oracle数据库**

```sql
sqlplus /nolog
-- 登录管理员
conn /as sysdba
```



**修改sys、system用户密码**

```sql
alter user system identified by system;
alter user sys identified by sys;
```



**持久化密码**

```sql
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
```

> oracle11g，静默安装后用户的密码有效期默认设置为180天，180天后密码将失效，oracle会提示要修改密码。



**创建用户并赋予权限**

```sql
-- 创建用户
create user test identified by test;
-- 并给用户赋予权限
grant connect,resource,dba to test;
```



**过程中出现的报错需要输入以下内容**

```sql
alter database mount;
alter database open;
```



**查看SID**

```sql
-- 用户登录
sqlplus /nolog
conn user/password

-- 查看SID
SELECT instance_name FROM v$instance;
```



# 连接数据库

**连接参数**

```
host: Linux IP
port: 1521
SID: helowin
user: test
password: test
```



**创建表测试**

```sql
create table TB_USER
(
	ID varchar2(10),
	NAME varchar2(10)
);
```



**开机设置**

```shell
# 快速关机
poweroff
# 启动docker
systemctl start docker
# 启动
docker start oracle11g
```

> 虚拟机最好正常关机，避免虚拟机损坏。



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



# 虚拟化问题

解决办法： 关闭Hyper-v服务
以管理员身份运行命令提示符执行命令：

```shell
bcdedit /set hypervisorlaunchtype off
```

重启，运行vm即可
若想恢复Hyper-v服务启动

```shell
bcdedit / set hypervisorlaunchtype auto
```

