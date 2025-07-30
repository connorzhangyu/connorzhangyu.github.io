---
title: Redis
tags:
  - Redis
categories:
  - Linux
cover: https://image.runtimes.cc/202404051532701.jpg
abbrlink: 27273
date: 2022-11-19 18:25:00
updated: 2024-07-02 22:13:17
---

# 安装Redis

## 安装单机Redis
{% tabs 安装单机Redis %}
<!-- tab Windows安装Redis-->
[Releases · tporadowski/redis](https://github.com/tporadowski/redis/releases)
<!-- endtab -->

<!-- tab Docker安装Redis -->

```shell
docker run \
       -p 6379:6379 \
       --name myredis \
       -v $PWD/redis.conf:/etc/redis/redis.conf \
       -v $PWD/data:/data \
       -d redis:3.2 redis-server /etc/redis/redis.conf \
       --restart=always \
       --appendonly yes
```

命令说明：

- `-name myredis` : 指定容器名称，这个最好加上，不然在看docker进程的时候会很尴尬。
- `p 6699:6379` ： 端口映射，默认redis启动的是6379,外部端口(6699)。
- `v $PWD/redis.conf:/etc/redis/redis.conf` ： 将主机中当前目录下的redis.conf配置文件映射。
- `v $PWD/data:/data -d redis:latest`： 将主机中当前目录下的data挂载到容器的/data
- `-redis-server --appendonly yes` :在容器执行redis-server启动命令，并打开redis持久化配置
- `-restart=always`:自动启动
- 注意事项：
- 如果不需要指定配置，`v $PWD/redis.conf:/etc/redis/redis.conf` 可以不用
- redis-server 后面的那段 `/etc/redis/redis.conf` 也可以不用。
- `$PWD` 在window系统下貌似不能用,可以用相对路径`/`

客户端连接

```shell
# 先查询到myredis容器的ip地址。
docker inspect myredis | grep IP
# 连接到redis容器。然后就进入redis命令行了。
docker run -it redis:latest redis-cli -h 192.168.42.32
```
<!-- endtab -->

<!-- tab Linux源码安装Redis-->

```shell
# Cetnos安装需要的软件
yum -y install gcc gcc-c++ kernel-devel make
# Ubunt 安装需要的软件
sudo apt install gcc g++ make linux-kernel-headers kernel-package
# ubuntu 新内核版本要用到的
sudo apt install gcc g++ make linux-libc-dev

# 下载redis
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
tar -zxvf redis-5.0.5.tar.gz
cd redis-5.0.5

# 安装redis
sudo make && make instal

# 注意make的时候可能会报错: #include <jemalloc/jemalloc.h>,使用下面的命令
make MALLOC=libc
```

修改redis.cnf

```shell
# 设置后台运行
daemonize yes
# 设置log文件路径
logfile /var/log/redis/redis-server.log
# 设置持久化文件存放路径
dir /var/lib/redis
```

```shell
# 创建日志存放目录
mkdir /var/log/redis/
# 创建持久化文件存放目录
mkdir /var/log/redis/
# 创建存放配置的文件夹
mkdir /etc/redis
# 拷贝配置文件并改名
cp redis.conf /etc/redis/6379.conf
# 拷贝自启动脚本文件
cp /usr/local/redis-6.0.3/utils/redis_init_script /etc/init.d/redisd
```

- 将启动脚本复制到`/etc/init.d`目录下，本例将启动脚本命名为redisd（通常都以d结尾表示是后台自启动服务）

```shell
# Centos
#设置为开机自启动服务器
cd /etc/init.d/
chkconfig redisd on

# Ubuntu
#设置服务脚本有执行权限
sudo chmod +x /etc/init.d/redisd
#注册服务
cd /etc/init.d/
sudo update-rc.d redisd defaults
```

- 此处直接配置开启自启动 `chkconfig redisd on` 将报错误： `service does not support chkconfig`

```shell
#!/bin/sh
# chkconfig:   2345 90 10
# description:  Redis is a persistent key-value database
```

通用命令

```shell
#启动Redis服务
sudo service redisd start
#关闭服务
sudo service redisd stop
#重启服务：
sudo service redisd restart
```
<!-- endtab -->
{% endtabs %}

## 安装集群Redis

{% tabs 安装集群Redis %}
<!-- tab Docker安装集群-->

```shell
#!/bin/bash
echo "start install redis-cluster..."
if [ ! -d /opt/docker-redis/7001/ ];then
	mkdir -p /opt/docker-redis/700{1,2,3,4,5,6}/data/
fi
cd /opt/docker-redis/7001/
wget http://download.redis.io/redis-stable/redis.conf -O /opt/docker-redis/7001/redis7001.conf
#
sed -i '/^#/d;/^$/d' redis7001.conf  #取出空行和注释行
sed -i 's/bind/#bind/g;s/appendonly no/appendonly yes/g;s/protected-mode yes/protected-mode no/g' redis7001.conf  #开启持久化，注释监听ip
#
echo '#集群配置' >> /opt/docker-redis/7001/redis7001.conf
echo 'cluster-enabled yes' >>/opt/docker-redis/7001/redis7001.conf
echo 'cluster-config-file nodes-7001.conf' >>/opt/docker-redis/7001/redis7001.conf
echo 'cluster-node-timeout 15000' >>/opt/docker-redis/7001/redis7001.conf
#
echo 'cluster-announce-ip 192.168.92.135' >>/opt/docker-redis/7001/redis7001.conf
echo 'cluster-announce-port 7001' >>/opt/docker-redis/7001/redis7001.conf
echo 'cluster-announce-bus-port 17001' >>/opt/docker-redis/7001/redis7001.conf
#
for port in `seq 7002 7006`;do
	cp redis7001.conf ../${port}/redis${port}.conf
	echo "cluster-config-file nodes-${port}.conf" >>/opt/docker-redis/${port}/redis${port}.conf
	echo "cluster-announce-port ${port}" >>/opt/docker-redis/${port}/redis${port}.conf
	echo "cluster-announce-bus-port 1${port}" >>/opt/docker-redis/${port}/redis${port}.conf
done
#
for port in `seq 7001 7006`;do
#	sed -i "s/logfile \"\"/logfile \"\/usr\/local\/docker\/redis-cluster\/log\/redis.log\"/g" redis${port}.conf
	sed -i "s/port 6379/port ${port}/g" /opt/docker-redis/${port}/redis${port}.conf
	docker run --restart always --name redis-cluster-${port} --net host --privileged=true -v /opt/docker-redis/${port}/redis${port}.conf:/usr/local/docker/redis-cluster/${port}/redis${port}.conf -v  \
	/opt/docker-redis/${port}/data/:/usr/local/docker/redis-cluster/data/ \
	-d redis redis-server /usr/local/docker/redis-cluster/${port}/redis${port}.conf
done
docker ps
sleep 2s
ss -tnulp|grep redis

#创建集群
#redis-cli  --cluster create 192.168.92.135:7001 192.168.92.135:7002 192.168.92.135:7003 192.168.92.135:7004 192.168.92.135:7005 192.168.92.135:7006  --cluster-replicas 1
```

<!-- endtab -->

<!-- tab 源码安装集群-->

**下载,解压,编译安装**
* 用一台虚拟机模拟6个节点，创建出3 master、3 salve 环境。
* 创建文件前,先编译好Redis程序

**创建节点**
创建配置redis.conf
```shell
# 端口7000 7001 7002 7003 7004 7005
port  7000
# 自己建议修改为0.0.0.0
bind 0.0.0.0
# redis后台运行
daemonize yes
# pidfile文件对应7000 7001 7002 7003 7004 7005
pidfile  /var/run/redis_7000.pid
# 开启集群  把注释#去掉
cluster-enabled  yes
# 集群的配置,配置文件首次启动自动生成7000 7001 7002 7003 7004 7005
cluster-config-file  nodes_7000.conf
# 请求超时  默认15秒，可自行设置
cluster-node-timeout  15000
# AOF日志开启
appendonly  yes
```

创建文件夹
```shell
cd /usr/local
mkdir redis_cluster
cd redis_cluster
mkdir 7000 7001 7002 7003 7004 7005
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7000/
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7001/
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7002/
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7003/
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7004/
cp /usr/local/redis-5.0.5/redis.conf /usr/local/redis_cluster/7005/
```

分别修改三个文件夹里的配置文件,修改如下内容


启动节点的redis`/usr/local/bin/redis-server`

```shell
/usr/local/bin/redis-server /usr/local/redis_cluster/7000/redis.conf
/usr/local/bin/redis-server /usr/local/redis_cluster/7001/redis.conf
/usr/local/bin/redis-server /usr/local/redis_cluster/7002/redis.conf
/usr/local/bin/redis-server /usr/local/redis_cluster/7003/redis.conf
/usr/local/bin/redis-server /usr/local/redis_cluster/7004/redis.conf
/usr/local/bin/redis-server /usr/local/redis_cluster/7005/redis.conf
```

检查 redis 启动情况

```shell
ps -ef | grep redi
root      61020      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7000 [cluster]
root      61024      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7001 [cluster]
root      61029      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7002 [cluster]
root      61029      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7002 [cluster]
root      61029      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7002 [cluster]
root      61029      1  0 02:14 ?        00:00:01 redis-server 0.0.0.0:7002 [cluster]
```

**启动集群**

* 装的redis是5.x的版本,这里没有应用到`redis-trib.rb`,所以就不需要装ruby
* 这里启动的时候可能会有报错,因为默认开启了`protected-mode`

```shell
cd /usr/local/bin
redis-cli --cluster create 192.168.0.100:7003 192.168.0.100:7004 192.168.0.100:7005 192.168.0.179:7000 192.168.0.179:7001 192.168.0.179:7002 --cluster-replicas 1

Can I set the above configuration? (type 'yes' to accept): yes
yes
```

**校验,等运行完成**
```shell
[root@worker1 src]# redis-cli --cluster check 192.168.0.179:7000
192.168.0.179:7000 (27bce53b...) -> 0 keys | 5462 slots | 1 slaves.
192.168.0.100:7004 (6b0173d9...) -> 0 keys | 5461 slots | 1 slaves.
192.168.0.100:7003 (9f15a932...) -> 0 keys | 5461 slots | 1 slaves.
[OK] 0 keys in 3 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 192.168.0.179:7000)
M: 27bce53bda92341ca4a5c82c2361ab99f24c0b27 192.168.0.179:7000
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: c7ebcd900fb7d9afb1980797acba45518cb7d877 192.168.0.100:7005
   slots: (0 slots) slave
   replicates 27bce53bda92341ca4a5c82c2361ab99f24c0b27
S: ed5256f8db1bf556a8dadbe8f2b07699507e17d9 192.168.0.179:7001
   slots: (0 slots) slave
   replicates 6b0173d925f70807a9081b7bc09bcd37be857342
S: 758609eaea88bac25b864f2badbab2171a68089b 192.168.0.179:7002
   slots: (0 slots) slave
   replicates 9f15a9329a9d0ec5c7fcb5abbba817730f0942f9
M: 6b0173d925f70807a9081b7bc09bcd37be857342 192.168.0.100:7004
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: 9f15a9329a9d0ec5c7fcb5abbba817730f0942f9 192.168.0.100:7003
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

**Cluster配置**

```shell
# 设置加入cluster，成为其中的节点
cluster-enabled yes|no

# cluster配置文件名，该文件属于自动生成，仅用于快速查找文件并查询文件内容
cluster-config-file < filename>

# 节点服务响应超时时间，用于判定该节点是否下线或切换为从节点
cluster-node-timeout < milliseconds>

# master连接的slave最小数量
cluster-migration-barrier < count>
```

**Cluster节点操作命令**

```shell
# 连接到集群,加一个-c就行
redis-cli -c

# 查看集群节点信息
cluster nodes

# 进入一个从节点redis，切换其主节点
cluster replication <master-id>

# 发现一个新节点，新增主节点
cluster meet ip:port

# 忽略一个没有solt的节点
cluster forget

# 手动故障转移
cluster failover

# 计算键 key 应该被放置在哪个槽上
cluster keyslot <key>

# 返回槽 slot 目前包含的键值对数量
cluster countkeysinslot <slot>

# 返回 count 个 slot 槽中的键
cluster getkeysinslot <slot> <count>
```
<!-- endtab -->
{% endtabs %}



## 配置

```shell
# requirepass foobared 注释去掉并写入要设置的密码 , 需要重启:systemctl restart redis
requirepass 123456

# 将 bind 设置为允许所有 IP 地址的远程访问,需要重启:systemctl restart redis
bind 0.0.0.0
```

# Redis的数据类型

redis自身是一个Map,其中所有的数据都是采用key:value的形式存储

数据类型指的是存储的数据的类型，也就是value部分的类型，key部分永远都是字符串

- string --> String
- hash --> Hashmap
- list --> LinkList
- set --> HashSet
- sorted_set --> TreeSet

## String

- redis所有的操作都是原子性的，采用单线程处理所有业务，命令是一个一个执行的

```shell
# 设值
# 格式 set <key> <value>
192.168.245.129:0>set k1 anthony
"OK"

# 取值
# 格式 get <key>
192.168.245.129:0>get k1
"anthony"

# 删除值,成功返回1,不成功返回0
192.168.245.129:0>del k1
"1"
192.168.245.129:0>del k1234
"0"

# 一次性存入多个值
192.168.245.129:0>mset k1 v1 k2 v2 k3 v3
"OK"

# 一次性取出多个值
192.168.245.129:0>mget k1 k2 k3
 1)  "v1"
 2)  "v2"
 3)  "v3"

# 打印值的长度
192.168.245.129:1>set name anthony
"OK"
192.168.245.129:1>get name
"anthony"
192.168.245.129:1>strlen name
"7"

# 追加
# 有数据就追加
192.168.245.129:1>append name 666
"10"
192.168.245.129:1>get name
"anthony666"
# 没数据就新建
192.168.245.129:1>append othername frankie
"7"
192.168.245.129:1>get othername
"frankie"

# 递增递减,小数不行
192.168.245.129:1>set num 1
"OK"
192.168.245.129:1>incr num
"2"
192.168.245.129:1>incr num
"3"
192.168.245.129:1>decr num
"2"
192.168.245.129:1>decr num
"1"

# 递增递减指定值,小数不行
192.168.245.129:1>incrby num 4
"6"
192.168.245.129:1>decrby num 2
"4"
# 小数不行
192.168.245.129:1>incrby num 1.5
"ERR value is not an integer or out of range"

# 递增指定小数,貌似没有递减
192.168.245.129:1>incrbyfloat num 1.5
"5.5"

# 指定过期时间
# EX 用于指定 key 的过期时间。EX(秒),PX(毫秒),KEEPTTL 保持原有的过期时间不变。
# PX 毫秒-设置指定的到期时间（以毫秒为单位）。
# NX 没有key的时候才能set成功,XX key存在的时候才能set成功
SET key value [EX seconds|PX milliseconds|KEEPTTL] [NX|XX] [GET]

# 指定过期时间
# time,以秒为单位。
SETEX key time value

# 功能等价NX,  SET if Not eXists
redis> SETNX mykey "Hello"
(integer) 1
redis> SETNX mykey "World"
(integer) 0
```

## Hash

{% mermaid %}

graph LR;
    Key;
    Key-->Field1--> Value1;
    Key-->Field2--> Value2;
    Key-->Field3--> Value3;
    subgraph 相当于Value  
    		Field1;
        Value1;
        Field2;
        Value2;
        Field3;
        Value3;
    end;

{% endmermaid %}

```shell
# 设值/修改值  hset key filed1 value
192.168.245.129:0>HSET user name zhangsan
"1"
192.168.245.129:0>hset user age 38
"1"

# 取一个属性值
192.168.245.129:0>hget user age
"38"

# 取多个属性值
192.168.245.129:0>hmget user age name
 1)  "45"
 2)  "zhangsan"

# 取一个key
192.168.245.129:0>hgetall user
 1)  "name"
 2)  "zhangsan"
 3)  "age"
 4)  "38"

 # 删除一个属性值
192.168.245.129:0>hdel user name
"1"
192.168.245.129:0>hgetall user
 1)  "age"
 2)  "38"

 # 查看有多少个属性
 192.168.245.129:0>hlen user
"3"

# 获取所有的属性
192.168.245.129:0>hkeys user
 1)  "age"
 2)  "name"
 3)  "sex"

# 获取所有的属性值
192.168.245.129:0>hvals user
 1)  "45"
 2)  "zhangsan"
 3)  "n"

 # 指定属性增加指定值
 192.168.245.129:0>hincrby user age 1
"46"
```

## List

{% mermaid %}
flowchart LR

subgraph 顺序表/数组
		id0(头指针)
    id1[华为]
    id2(苹果)
    id3(微软)
end

subgraph 单向链表
    direction LR
    id00[头指针] --> id4[[华为]]
    id4[[华为]] --> id5[[苹果]]
    id5[[苹果]] --> id6[[微软]]
    end
subgraph 双向链表
    direction LR
    id000[头指针] <--> id7[[华为]] 
    id7[[华为]] <--> id8[[苹果]]
    id8[[苹果]] <--> id9[[微软]]
    end

{% endmermaid %}

```shell
# 先插入huawei
192.168.245.129:0>lpush list1 huawei
"1"
# 再插入apple
192.168.245.129:0>lpush list1 apple
"2"
# 最后插入Microsoft
192.168.245.129:0>lpush list1 microsoft
"3"
# 从左边取
192.168.245.129:0>lrange list1 0 2
 1)  "microsoft"
 2)  "apple"
 3)  "huawei"

 # 一次性插入多条数据
192.168.245.129:0>rpush list2 a b c
"3"

#  从左边取
# lrange key start stop
# lindex key index 取指定索引的值
# llen key 取长度
# 没有rrange
192.168.245.129:0>lrange list2 0 2
 1)  "a"
 2)  "b"
 3)  "c"
 # 从左边取的第二钟方法
192.168.245.129:0>lrange list2 0 -1
 1)  "a"
 2)  "b"
 3)  "c"

# lpop从左边删,rpop从右边删
192.168.245.129:0>lpush list3 a b c
"3"
192.168.245.129:0>lpop list3
"c"

----------------------------------------------------------------------------------------------------

# 阻塞取值,从运行命令开始,如果有数据,取出来,立马返回,如果没有数据,就等指定的时间20s,有就立马返回结束,如果没有,就一直等到时间结束
192.168.245.129:0>blpop list4 20
 1)  "list4"
 2)  "32"

# 删除指定数据
# lrem key count value
# count是删除多少个value
192.168.245.129:0>lpush dianzan  a b c d
"4"
192.168.245.129:0>lrem dianzan 1 c
"1"
192.168.245.129:0>lrange dianzan 0 -1
 1)  "d"
 2)  "b"
 3)  "a"
```

## Set



![](https://image.runtimes.cc/202404051516451.png)

```shell
# 添加
192.168.245.129:0>sadd users zs
"1"
192.168.245.129:0>sadd users lisi
"1"
192.168.245.129:0>sadd users ww
"1"

# 查列表
192.168.245.129:0>smembers users
 1)  "lisi"
 2)  "ww"
 3)  "zs"

 # 查数量
192.168.245.129:0>scard users
"3"

# 判断是否有指定数据
192.168.245.129:0>sismember users ls
"0"
192.168.245.129:0>sismember users ww
"1"
192.168.245.129:0>smembers users
 1)  "lisi"
 2)  "ww"
 3)  "zs"

 # 删除指定数据
192.168.245.129:0>srem users ww
"1"
192.168.245.129:0>smembers users
 1)  "lisi"
 2)  "zs"

 # 随机获取集合中指定数量的数据,获取之后,原来的队列数据不变
 srandmember key count
 # 随机获取集合中的某个数据并讲该数据移除集合
 spop key

 --------------------------------------------------------------------------------------
 > sadd u1 a1
1
> sadd u1 a2
1
> sadd u1 a3
1
> sadd u2 a1
1
> sadd u2 a2
1
# 交集
> sinter u1 u2
a2
a1
# 并集
> sunion u1 u2
a1
a2
a3
# 差集 (u1,u2)顺序不一样,结果不一样
> sdiff u1 u2
a3
```

redis应用于随机推荐类信息检索，例如热点歌单推荐，热点新闻推荐，热点旅游线路，应用APP推荐，大V推荐等

## sort_set

```shell
# 添加数据
> zadd scores 100  zhangsan
1
> zadd scores 90  lisi
1
> zadd scores 95  wangwu
1

# 获取全部数据
> zrange scores 0 -1
lisi
wangwu
zhangsan

# 获取全部数据
> zrange scores 0 -1 withscores
lisi
90
wangwu
95
zhangsan
100

# 反向获取全部数据
> zrevrange scores 0 -1 withscores
zhangsan
100
wangwu
95
lisi
90

# 删除数据
> zrem scores zhangsan
1
> zrange scores 0 -1 withscores
lisi
90
wangwu
95

# 按条件获取数据,可以用作分页
zrangebyscore key min max [WITHSCORES] [LIMIT]
zrevrangebyscore key max min [WITHSCORES]

# 条件删除
zremrangebyrank key start stop
zremrangebyscore key min max

-----------------------------------------排名-------------------------------------------------------
# 添加模拟数据
> zadd movies 143 aa 97 bb 201 cc
3

# 获取数据对应的索引(排名)
> zrank movies bb
0
# 反向获取
> zrevrank movies bb
2

# score 值获取与修改
zscore key member
zincrby key increment member
```

# Redis 通用命令

```shell
# 删除
del key

# 获取key是否存在
exists key

# 获取key的类型
type key

# 设置指定有效期,秒
expire key second
# 设置指定有效期,毫秒
pexpire key milliseconds
# 有效期是时间戳
expireat key timestamp

# 获取key的有效期
ttl key
pttl key  毫秒

# 集群环境下,模糊查询key
# 要是查不出来数据,99999要换成一个很大的值
SCAN 0 MATCH * count 99999 
```
# 持久化

很多时候我们需要持久化数据也就是将内存中的数据写入到硬盘里面，大部分原因是为了之后重用数据（比如重启机器、机器故障之后恢复数据），或者是为了防止系统故障而将数据备份到一个远程位置。

Redis不同于Memcached的很重一点就是，Redis支持持久化，而且支持两种不同的持久化操作。

Redis的一种持久化方式叫快照（snapshotting，RDB）

另一种方式是只追加文件（append-only file,AOF）

这两种方法各有千秋，下面我会详细这两种持久化方法是什么，怎么用，如何选择适合自己的持久化方法。

![](https://image.runtimes.cc/202404051454660.png)

## RDB(快照)

save 会生成rdb文件

### save指令相关配置

| 配置项              | 描述                                    |      |
| ------------------- | --------------------------------------- | ---- |
| dbfilename dump.rdb | 指定本地数据库文件名，默认值为 dump.rdb |      |
| dir ./              | 指定本地数据库存放目录                  |      |
|                     |                                         |      |

注意：**Redis是单线程的**，所有命令都会在类似队列中排好队，不建议使用save指令，因为save指令的执行会阻塞当前Redis服务器，直到当前RDB过程完成位置，有可能会造成长时间阻塞，**线上环境不建议使用**

### bgsave指令

手动启动后台保存操作，但不是立即执行

![](https://image.runtimes.cc/202404051453704.png)

执行成功了不会在控制台输出,可以在日志中看到

```shell
58142:M 07 Aug 2020 07:23:17.355 * Starting BGSAVE for SYNC with target: disk
58142:M 07 Aug 2020 07:23:17.355 * Background saving started by pid 58183
58183:C 07 Aug 2020 07:23:17.357 * DB saved on disk
58183:C 07 Aug 2020 07:23:17.357 * RDB: 0 MB of memory used by copy-on-write
58142:M 07 Aug 2020 07:23:17.456 * Background saving terminated with success
```

bgsave命令是针对save阻塞问题做的优化。Redis内部所有涉及到RDB操作都采用bgsave的方式，save命令可以放弃使用

### save配置

```
# second：监控时间范围
# changes：监控key的变化量
save second changes

save 900 1      #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
save 300 10     #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
save 60 10000    #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
```

### save配置原理

![](https://image.runtimes.cc/202404051453062.png)

**注意：**save配置要根据实际业务情况进行设置，频度过高或过低都会出现性能问题，结果可能是灾难性的save配置中对second与changes设置通常具有互补对应关系，尽量不要设置成包含性关系save配置启动后执行的是bgsave操作

### 对比

![](https://image.runtimes.cc/202404051453536.png)

### RDB启动方式

- 全量复制在主从复制中会提到
- 服务器运行过程中重启debug reload
- 关闭服务器时指定保存数据shutdown save

### RDB 优缺点

### RDB优点

- RDB是一个紧凑压缩的二进制文件，存储效率较高
- RDB内部存储的是redis在某个时间点的数据快照，非常适合用于数据备份，全量复制等场景
- RDB恢复数据的速度要比AOF快很多
- 应用：服务器中每X小时执行bgsave备份，并将RDB文件拷贝到远程己气中，用于灾难恢复

### RDB缺点

- RDB方式无论是执行指令还是利用配置，无法做到实时持久化，具体较大的可能性丢失数据
- bgsave指令每次运行要执行fork操作创建子进程，要牺牲掉一些性能
- Redis的众多版本中未进行RDB文件格式的版本统一，有可能出现个版本服务之间数据格式无法兼容现象

### RDB存储的弊端

- 存储数据量较大，效率较低——基于快照思想，每次读写都是全部数据，当数据量巨大时，效率非常低
- 大数据量下的IO性能较低
- 基于fork创建子进程，内存产生额外消耗
- 宕机带来的数据丢失风险

### 解决思路

- 不写全数据，仅记录部分数据
- 改记录数据未记录操作过程
- 对所有操作均进行记录，排除丢失数据的风险
- 这也就是AOF的引入

## AOF

### AOF写数据过程

![](https://image.runtimes.cc/202404051454829.png)

### AOF写数据的三种策略

```
appendfsync always  #每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速度
appendfsync everysec #每秒钟同步一次，显示地将多个写命令同步到硬盘 **(默认的)**
appendfsync no    #让操作系统决定何时进行同步
```

### AOF功能开启和相关配置

```
# 是否开启APF持久化功能，默认为不开启
appendonly yes|no

# AOF写数据策略
appendfsync always|everysec|no

# AOF持久化文件名，默认文件名为appendonly.aof,建议配置为appendonly-端口号.aof
appendfilename filename

# AOF持久化文件保存路径，与RDB持久化文件保持一致即可
dir
```

### 重写

随着命令的不断写入AOF，文件会越来越大，为了解决这个问题，Redis引入AOF重写机制压缩文件体积，AOF文件重写是将Redis进程内的数据转换为写命令同步到新AOF文件的过程，简单说就是将同样一个数据的若干个命令执行结果转换为最终结果数据对应的指令进行记录

### AOF重写作用

- 降低磁盘占用量，提高磁盘利用路
- 提高持久化效率，降低持久化写时间，提高IO性能
- 降低数据恢复用时，提高数据恢复效率

### AOF重写规则

- 进程内已超时的数据不再写入文件
- 忽略无效指令，重写时使用进程内数据直接生成，这样新的AOF文件只保留最终数据的写入命令　如del key1,hdel key2,srem key3,set key 222等
- 对统一数据的多条命令合并为一条命令如 lpush list1 a ,lpush list1 b,lpush list1 c可以转化为lpush list1 a b c为防止数据量过大造成客户端缓冲区溢出，对list,set,hash,set等类型，每条指令最多写入64个元素

### 重写方式

```
# 手动重写,在命令行执行,会覆盖原来的aof文件,但是文件更小
bgrewriteaof

# 自动重写
auto-aof-rewrite-min-size 		size
auto-aof-rewrite-percentage 	percentage
```

手动重写流程:

![](https://image.runtimes.cc/202404051454591.png)

自动重写的触发条件:

```
# 自动重写触发条件设置
auto-aof-rewrite-min-size
auto-aof-rewrite-percentage percent

# 自动重写触发对比参数（运行指令info Persistence获取具体信息）
aof_current_size
aof_base_size
```


![](https://image.runtimes.cc/202404051517623.png)

![](https://image.runtimes.cc/202404051455402.png)

![](https://image.runtimes.cc/202404051456010.png)

## AOF和RDB的区别

![](https://image.runtimes.cc/202404051456409.png)

### RDB和AOF的选择之感

- 对数据非常敏感，建议使用默认的AOF持久化方案AOF持久化策略使用erverysecond，每秒钟fsync一次。该策略redis任然可以保持很好的处理性能，当出现问题时，最多丢失0-1秒中的数据。注意：由于AOF文件存储体积较大，且恢复数据较慢
- 数据呈现阶段有效性，建议使用RDB持久化方案数据可以良好的做到阶段内无丢失（该阶段是开发者或运维人工手工维护的），且恢复速度较快，阶段点数据恢复通常采用RDB方案注意：利用RDB实现紧凑的数据持久化会使Redis降得很低
- 综合对比
1. RDB与AOF得选择实际上是在做一种权衡，每种都有利弊
2. 如不能承受数分钟以内得数据丢失，对业务数据非常敏感，选用AOF
3. 如能承受数分钟以内数据丢失，且追求大数据集得恢复速度，选用RDB灾难恢复选用RDB
4. 双保险策略，同时开启RDB和AOF，重启后，Redis优先使用AOF来恢复数据，降低丢失数据的量

## **Redis 4.0 对于持久化机制的优化**

Redis 4.0 开始支持 RDB 和 AOF 的混合持久化（默认关闭，可以通过配置项 `aof-use-rdb-preamble` 开启）。

如果把混合持久化打开，AOF 重写的时候就直接把 RDB 的内容写到 AOF 文件开头。这样做的好处是可以结合 RDB 和 AOF 的优点, 快速加载同时避免丢失过多的数据。当然缺点也是有的， AOF 里面的 RDB 部分是压缩格式不再是 AOF 格式，可读性较差。

# 事务

```
# 开启事务,设定事务的开启位置，此指令执行后，后续的所有指令均加入到事务中
multi

# 执行提交事务,加入事务的命令暂时到任务队列中，并没有立即执行，只有执行exec命令才开始执行
exec

# 取消事务,终止当前事务定义，发生在multi之后，exec之前
discard
```

## 事务的工作流程

![](https://image.runtimes.cc/202404051456083.png)

## 事务的注意事项

- 语法错误指命令书写格式有误
- 处理结果如果定义的事务中所包含的命令存在语法错误，整体事务中所有命令均不会被执行。包括那些语法正确的命令

---

- 运行错误指命令格式正确，但是无法正常的执行。例如对list进行incr操作
- 处理结果能够正确运行的命令会执行，运行错误的命令不会执行注意：已经执行完毕的命令对应的数据不会自动回滚，需要程序员自己在代码中实现回滚。

### 手动进行事务回滚(基本没法用)

- 记录操作过程中被影响的数据之前的状态单数据：string多数据：hash,list,set,zset
- 设置指令恢复所有的被修改的项单数据：直接set（注意周边属性，例如时效）多数据：修改对应值或整体克隆复制

# 锁

```
# 对key添加监视锁，在执行exec前如果key发生了变化，终止事务执行
watch key1 [key2…]

# 取消对所有key的监视
unwatch
```

## 分布式锁

```
# 加锁
setnx lock-key value

# 设置超时时间
expire lock-key second

# 删除锁
dek lock-key
```

- 利用setnx命令的返回值特征，有值则返回设置失败，无值则返回设置成功
- 对于返回设置成功的，拥有控制权，进行下一步的具体业务操作
- 对于返回设置失败的，不具有控制权，排队或等待操作完毕通过**del**操作释放锁

# 删除策略

## 过期数据删除策略

- Redis是一种内存级数据库，所有数据均存放在内存中，内存中的数据可以通过TTL指令获取其状态
- XX : 具有时效性的数据
- 1 : 永久有效的数据
- 2 : 已经国企的数据 或 被删除的数据 或 未定义的数据

## 时效性数据的存储结构
![](https://image.runtimes.cc/202404051457852.png)
### 定时删除

- 创建一个定时器，当key设置过期时间，且过期时间到达时，由定时器任务立即执行对键的删除操作
- **优点**：节约内存，到时就删除，快速释放掉不必要的内存占用
- **缺点**：CPU压力很大，无论CPU此时负载多高，均占用CPU，会影响redis服务器响应时间和指令吞吐量
- **总结**：用处理器性能换取存储空间

### 惰性删除

- 数据到达过期时间，不做处理。等下次访问该数据
1. 如果未过期，返回数据
2. 发现已经过期，删除，返回不存在
- 优点：节约CPU性能，发现必须删除的时候才删除
- 缺点：内存压力很大，出现长期占用内存的数据
- 总结：用存储空间换取处理器性能

### 定期删除

- Redis启动服务器初始化时，读取配置server.hz的值，默认为10
- 每秒钟执行server.hz次serverCron()

![](https://image.runtimes.cc/202404051457886.png)

- 周期性轮询redis库中时效性数据，采用随机抽取的策略，利用过期数据占比的方式删除频度
- 特点1：CPU性能占用设置有峰值，检测频度可自定义设置
- 特点2：内存压力不是很大，长期占用内存的冷数据会被持续清理
- 总结：周期性抽查存储空间

![](https://image.runtimes.cc/202404051516388.png)

# 逐出算法

- Redis使用内存存储数据，在执行每一个命令前，会调用freeMemorylfNeeded()检测内存是否充足。如果内存不满足新加入数据的最低存储要求，redis要临时删除一些数据为当前指令清理存储空间。清理数据的策略称为逐出算法。
- 注意：逐出数据的过程不是100%能够清理出足够的可使用的内存空间，如果不成功则反复执行。当对所有数据尝试完毕后，如果不能达到内存清理的要求，将出现错误信息

![](https://image.runtimes.cc/202404051457056.png)

相关配置

```
# 最大可使用内存
maxmemory

# 每次选取代删除数据的个数
maxmemory-samples

# 删除策略
maxmemory-policy
```

| 属性            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| volatile-lru    | 从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰 |
| volatile-ttl    | 从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰 |
| volatile-random | 从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰 |
| volatile-lfu    | 从已设置过期时间的数据集(server.db[i].expires)中挑选最不经常使用的数据淘汰 |
| allkeys-lru     | 当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key （这个是最常用的） |
| allkeys-random  | 从数据集（server.db[i].dict）中任意选择数据淘汰              |
| allkeys-lfu     | 当内存不足以容纳新写入数据时，在键空间中，移除最不经常使用的key |
| no-eviction     | 禁止驱逐数据，也就是说当内存不足以容纳新写入数据时，新写入操作会报错。这个应该没人使用吧！ |

![](https://image.runtimes.cc/202404051457458.png)

数据逐出策略配置依据

- 使用INFO命令输出监控信息，查询缓存int和miss的次数，根据业务需求调优Redis配置

# 配置文件

```
# 设置服务器以守护进程的方式运行
deamonize yes|no

# 绑定主机地址
bind 127.0.0.1

# 设置服务器端口号
port 6379

# 设置数据库数量
databases 16

----------------------------------日志配置--------------------------------------
# 设置服务器以指定日志记录级别
loglevel debug|verbose|notice|warning

# 日志记录文件名
logfile 端口号.log

# 持久化文件存放目录
dir ./

----------------------------------日志配置--------------------------------------

# 设置同一时间最大客户链接数，默认无限制。当客户端连接到达上线，Redis会关闭新的链接
maxclients 0

# 客户端限制等待最大时常，达到最大之后关闭连接。如需关闭该功能，设置为0
timeout 300
```

# Redis-Cluster结构设计

# 数据存储设计

Key -> CRC16(Key) 相当于HashCode值--->%16384

- 将所有的存储空间计划切割成16384份，每台主机保存一部分,每份代表的使一个存储空间，不是一个key的保存空间
- 将key按照计算出的结果放到对应的存储空间


![](https://image.runtimes.cc/202404051457725.png)

当有新的机器加入集群的时候,就会每台机器转移一些数据空间


![](https://image.runtimes.cc/202404051457378.png)

# 集群内部通讯设计

![](https://image.runtimes.cc/202404051458468.png)

# 集群常用命令

参考:[https://www.cnblogs.com/kevingrace/p/7910692.html](https://www.cnblogs.com/kevingrace/p/7910692.html)

以下命令是Redis Cluster集群所独有的，执行下面命令需要先登录redis

```
# （客户端命令：redis-cli -c -p port -h ip）
[root@manage redis]# redis-cli -c -p 6382 -h 192.168.10.12
192.168.10.12:6382>
```

- 集群`cluster info` ：打印集群的信息`cluster nodes` ：列出集群当前已知的所有节点（ node），以及这些节点的相关信息。
- 节点`cluster meet <ip> <port>` ：将 ip 和 port 所指定的节点添加到集群当中，让它成为集群的一份子。`cluster forget <node_id>`：从集群中移除 node_id 指定的节点。`cluster replicate <master_node_id>` ：将当前从节点设置为 node_id 指定的master节点的slave节点。只能针对slave节点操作。`cluster saveconfig` ：将节点的配置文件保存到硬盘里面。
- slot`cluster addslots <slot> [slot ...]` ：将一个或多个槽（ slot）指派（ assign）给当前节点。`cluster delslots <slot> [slot ...]` ：移除一个或多个槽对当前节点的指派。`cluster flushslots` ：移除指派给当前节点的所有槽，让当前节点变成一个没有指派任何槽的节点。`cluster setslot <slot> node <node_id>`：将槽 slot 指派给 node_id 指定的节点，如果槽已经指派给
- 另一个节点，那么先让另一个节点删除该槽>，然后再进行指派`cluster setslot <slot> migrating <node_id>`：将本节点的槽 slot 迁移到 node_id 指定的节点中。`cluster setslot <slot> importing <node_id>` ：从 node_id 指定的节点中导入槽 slot 到本节点。`cluster setslot <slot> stable`：取消对槽 slot 的导入（ import）或者迁移（ migrate）。
- 键`cluster keyslot <key>`：计算键 key 应该被放置在哪个槽上。`cluster countkeysinslot <slot>`：返回槽 slot 目前包含的键值对数量。`cluster getkeysinslot <slot> <count>`：返回 count 个 slot 槽中的键 。

# 解决方案

# 缓存预热

在高请求之前，做好一系列措施，保证大量用户数量点击造成灾难。

1. 请求数量较高
2. 主从之间数据吞吐量较大，数据同步操作频度较高

### 缓存预热解决方案

前置准备工作：

1. 日常例行统计数据访问记录，统计访问频度较高的热点数据
2. 利用LRU数据删除策略，构建数据留存队列例如：storm与kafka配合

准备工作：

1. 将统计结果中的数据分类，根据级别，redis优先加载级别较高的热点数据
2. 利用分布式多服务器同时进行数据读取，提速数据加载过程

实施：

1. 使用脚本程序固定出大数据预热过程
2. 如果条件允许，使用CDN（内容分发网络），效果会更好

缓存预热总结：缓存预热就是系统启动前，提前将相关的缓存数据直接加载到缓存系统。避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题！用户直接查询事先被预热的缓存数据!

# 缓存雪崩

1. 系统平稳运行过程中，忽然数据库连接量激增
2. 应用服务器无法及时请求
3. 大量408，500错误页面出现
4. 客户反复刷新页面获取数据
5. 数据库崩溃
6. 应用服务器崩溃
7. 重启应用服务器无效
8. Redis服务器崩溃
9. Redis集群崩溃
10. 重启数据之后再次被瞬间流量放倒

### 问题排查

简介：缓存同一时间大面积的失效，所以，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉

1. 在一个较短的时间内，缓存中较多的key集中过期
2. 此周期内请求访问过期的数据，redis未命中，redis向数据库获取数据
3. 数据库同时接受到大量的请求无法即时处理
4. Redis大量请求被积压，开始出现超时现象
5. 数据库流量激增，数据库崩溃
6. 重启后任然面对缓存中无数据可用
7. Redis服务器资源被严重占用，Redis服务器崩溃
8. Redis集群呈现崩塌，集群瓦解
9. 应用服务器无法即时得到数据响应请求，来自客户端的请求数量越来越多，应用服务器崩溃
10. 应用服务器，redis，数据库全部重启，效果不理想

### 问题分析

- 短时间范围内
- 大量key集中过期

### 解决方案（道）

1. 更多的页面静态化处理
2. 构建多级缓存架构Nginx缓存+redis缓存+ehcache缓存
3. 检测Mysql严重耗时业务进行优化对数据库的瓶颈排查：例如超时查询、耗时较高事务等
4. 灾难预警机制监控redis服务器性能指标1、CPU占用、CPU使用率2、内存容量3、查询平均响应时间4、线程数
5. 限流、降级短时间范围内习生一些客户体验，限制一部分请求访问，降低应用服务器压力，待业务低速运转后再逐渐放开访问

### 解决方案（术）

1. LRU与LFU切换
2. 数据有效期策略调整根据业务数据有效期进行分类错峰，A类90分钟，B类80分钟，C类70分钟过期时间使用固定形式+随机值的形式，稀释集中到期的key的数量
3. 超热数据使用永久key
4. 定期维护（自动+人工）对即将过期数据做访问量分析，确认是否演示，配合访问量统计，做热点数据的延时
5. 加锁 慎用！！！！！！！

总结缓存雪崩式瞬间过期数量太大，导致对数据库服务器造成压力。如果能有效避免过期时间集中，可以有效解决雪崩现象的出现（约40%）。配合其他策略一起使用，并监控服务器的运行数据，根据运行巨鹿做快速调整

![](https://image.runtimes.cc/202404051458329.png)

# 缓存击穿

### 数据库服务器崩溃（2）

1. 系统平稳运行过程中
2. 数据库连接量瞬间激增
3. Redis服务器无大量key过期
4. Redis内存平稳，无波动
5. Redis服务器CPU正常
6. 数据库崩溃

### 问题排查

1. Redis中某个key过期，该key访问量巨大
2. 多个数据请求从服务器直接压到Redis后，均为命中
3. Redis在短时间内发起了大量对数据库中同一个数据的访问

**问题分析**

- 单个key高热数据
- key过期

### 解决方案（术）

1. 预先设定以电商为例，每个商家根据店铺等级，指定若干款主打商品，在购物节期间，加大此类信息key的过期时常注意：购物节不仅仅指当天，以及后续若干天，访问峰值呈现逐渐降低趋势
2. 现场调整监控访问量，对自然流量激增的数据延长过期时间或设置为永久性key
3. 后台刷新数据启动定时任务，高峰期来临之前，刷新数据有效期，保存不丢失
4. 二级缓存设置不同的失效时间，保障不会被同时淘汰就行
5. 加锁分布式锁，防止被击穿，但是要注意也是性能瓶颈，慎重！！！！！！！！

总结：缓存击穿就是单个高热数据过期的瞬间，数据访问较大，未命中redis后，发起了大量对同一数据的数据库访问，导致对数据库服务器造成压力。应对策略应该在业务数据分析与预防方面进行，配合运行监控测试与即时调整策略，毕竟单个key的过期监控难度较高，配合雪崩处理策略即可

# 缓存穿透

### 数据库服务器崩溃（3）

简介：一般是黑客故意去请求缓存中不存在的数据，导致所有的请求都落到数据库上，造成数据库短时间内承受大量请求而崩掉。

1. 系统平稳运行过程中
2. 应用服务器流量随时间增量较大
3. Redis服务器命中率随时间逐步降低
4. Redis内存平稳，内存无压力
5. Redis服务器CPU占用激增
6. 数据库服务器压力激增
7. 数据库崩溃

### 问题排查

1. redis中大面积出现未命中
2. 出现非正常URL访问

**问题分析**

- 获取的数据在数据库中也不存在，数据库查询未得到对应数据
- Redis获取到null数据未进行持久化，直接返回
- 下次此类数据到达重复上述过程
- 出现黑客攻击服务器

### 解决方法（术）

1. 缓存null对查询结果为null的数据进行缓存（长期使用，定期清理），设定短时限，例如30-60秒，最高五分钟
2. 白名单策略提前预热各种分类数据id对应的bitmaps,id作为bitmaps的offset,相当于设置了数据白名单。当加载正常数据后放型，加载异常数据时直接拦截（效率偏低）使用布隆过滤器（有关布隆过滤器的命中问题对当前状态可以忽略）
3. 实时监控试试监控redis命中率（业务正常范围时，通常回有一个波动值）与null数据的占比非活动时间波动：通常检测3-5倍，超过5倍纳入重点排查对象活动时间波动：通常检测10-50倍，超过50倍纳入重点排查对象根据背书不同，启动不同的排查流程。然后使用黑名单进行防控（运营）
4. key加密问题出现后，临时启动防灾业务key,对key进行业务层传输加密服务，设定校验程序，过来的key校验例如每天随机分配60个加密串，挑选2-3个，混淆到页面数据id中，发现访问key不满足规则，驳回数据访问

**总结**缓存穿透是访问了不存在的数据，跳过了合法数据的redis数据缓存阶段，每次访问数据库，导致对数据库服务器造成压力。通常此类数据的出现量是一个较低的值，当出现此类情况以毒攻毒，并即时报警。应对策略应该在临时预案防范方面多做文章无论是黑名单还是白名单，都是对整体系统的压力，警报解除后尽快移除

# Redis-Java

# 单机Jedis

```
/**
 * 192.168.0.5 单机
 */
public class SingletonDemo {

    private Jedis jedis;

    @Before
    public void before() {
        //指定Redis服务Host和port
        jedis = new Jedis("192.168.0.5", 6379);
    }

    @After
    public void after() {
        //使用完关闭连接
        jedis.close();
    }

    /**
     * 设值
     */
    @Test
    public void set() {
        //访问Redis服务
        jedis.set("k1", "v1");
        System.out.println(jedis.get("k1"));
    }

    /**
     * 设值,并且设置超时时间
     */
    @Test
    public void setTime() {
        String key = "setTimeKey";
        jedis.setex(key, 20000, "setTimeValue");
        System.out.println(jedis.get(key));
        System.out.println(jedis.ttl(key));
    }

    /**
     * 分布式锁原理,
     * key不存在的时候才插入
     */
    @Test
    public void setNX() {
        String key = "setNXKey";
        // 1.先插入一个值
        jedis.set(key, "setNXValue");

        // 2.NX是不存在时才set， XX是存在时才set， EX是秒，PX是毫秒
        String set = jedis.set(key, "setNXValue---NX", "NX", "EX", 20000L);
        System.out.println(set);
        System.out.println(jedis.get(key));
        System.out.println(jedis.ttl(key));

        System.out.println("======================================================");

        // 3.设置个没有的key
        String key2 = "setNXKey2";
        String set2 = jedis.set(key2, "setNXValue2---NX", "NX", "EX", 20000L);
        System.out.println(set2);
        System.out.println(jedis.get(key2));
        System.out.println(jedis.ttl(key2));
    }

    /**
     * key存在时才插入
     */
    @Test
    public void setXX() {
        String key = "setXXKey";
        // 1.先插入一个值
        jedis.set(key, "setXXValue");

        // 2.NN
        String set = jedis.set(key, "setXXValue---XX", "XX", "EX", 20000L);
        System.out.println(set);
        System.out.println(jedis.get(key));
        System.out.println(jedis.ttl(key));

        System.out.println("======================================================");

        // 3.设置个没有的key
        String key2 = "setXXKey2";
        String set2 = jedis.set(key2, "setXXValue2---NX", "XX", "EX", 20000L);
        System.out.println(set2);
        System.out.println(jedis.get(key2));
        System.out.println(jedis.ttl(key2));
    }

    @Test
    public void expire() {
    }

    /**
     * redis监控信息
     */
    @Test
    public void info() {
        String info = jedis.info();
        Stream.of(info.split("\r\n")).forEach(row -> {

                    String[] split = row.split(":");
                    if (split.length == 2) {

                        System.out.printf("key:%s ====  value:%s \r\n",split[0],split[1]);
                    }

                }
        );
    }

    /**
     * jedispool
     */
    @Test
    public void jedisPool() throws InterruptedException {

        StopWatch watch = new StopWatch();
        watch.start();

        for (int i = 0; i < 100000; i++) {
            jedis.set(i + "", i + "");
            jedis.close();
        }
        watch.stop();
        System.out.println("5-"+watch.getTime());
    }

    @Test
    public void jedisPool2() {

        // 替换成你的reids地址和端口
        String redisIp = "192.168.0.5";
        int reidsPort = 6379;
        JedisPool jedisPool = new JedisPool(new JedisPoolConfig(), redisIp, reidsPort);
        Jedis resource = jedisPool.getResource();
        StopWatch watch = new StopWatch();
        watch.start();

        for (int i = 0; i < 100000; i++) {
            resource.set(i + "", i + "");
        }
        watch.stop();
        System.out.println("5-"+watch.getTime());

    }
}
```

# 单机Jedis连接集群节点

会报错

```
redis.clients.jedis.exceptions.JedisMovedDataException: MOVED 12706 192.168.0.8:6379
	at redis.clients.jedis.Protocol.processError(Protocol.java:115)
	at redis.clients.jedis.Protocol.process(Protocol.java:161)
	at redis.clients.jedis.Protocol.read(Protocol.java:215)
	at redis.clients.jedis.Connection.readProtocolWithCheckingBroken(Connection.java:340)
	at redis.clients.jedis.Connection.getStatusCodeReply(Connection.java:239)
	at redis.clients.jedis.Jedis.set(Jedis.java:121)
	at reids.ClusterDemo.set(ClusterDemo.java:68)
```

# 集群Jedis

```
/**
 * anthony@base:/var/log/redis$ redis-cli --cluster check 192.168.0.6:6379
 * 192.168.0.6:6379 (99af86e5...) -> 0 keys | 5461 slots | 1 slaves.
 * 192.168.0.8:6379 (982f74c7...) -> 3 keys | 5461 slots | 1 slaves.
 * 192.168.0.7:6379 (1069a632...) -> 0 keys | 5462 slots | 1 slaves.
 * [OK] 3 keys in 3 masters.
 * 0.00 keys per slot on average.
 * >>> Performing Cluster Check (using node 192.168.0.6:6379)
 * M: 99af86e59c7f5a4ddf212b29e9e1b12fa5e7a866 192.168.0.6:6379
 *    slots:[0-5460] (5461 slots) master
 *    1 additional replica(s)
 * S: 102517bddae6af2434519e8f348cf062b0152fa7 192.168.0.9:6379
 *    slots: (0 slots) slave
 *    replicates 982f74c79ed5c5811b7011507c56f81374c70a0b
 * S: 392c5e4dcd5608c74a76902668ed58fb6ab50aaf 192.168.0.11:6379
 *    slots: (0 slots) slave
 *    replicates 1069a6320a0489f63e807c970c27f86f13f417cf
 * S: 4d12dd0de1af9d90bfac7dd141c36c348071d59f 192.168.0.10:6379
 *    slots: (0 slots) slave
 *    replicates 99af86e59c7f5a4ddf212b29e9e1b12fa5e7a866
 * M: 982f74c79ed5c5811b7011507c56f81374c70a0b 192.168.0.8:6379
 *    slots:[10923-16383] (5461 slots) master
 *    1 additional replica(s)
 * M: 1069a6320a0489f63e807c970c27f86f13f417cf 192.168.0.7:6379
 *    slots:[5461-10922] (5462 slots) master
 *    1 additional replica(s)
 * [OK] All nodes agree about slots configuration.
 * >>> Check for open slots...
 * >>> Check slots coverage...
 * [OK] All 16384 slots covered.
 */
public class ClusterDemo {

    private JedisCluster jedis;

    @Before
    public void before() {
        Set<HostAndPort> nodes = new HashSet<>();
        nodes.add(new HostAndPort("192.168.0.6", 6379));
        nodes.add(new HostAndPort("192.168.0.7", 6379));
        nodes.add(new HostAndPort("192.168.0.8", 6379));
        nodes.add(new HostAndPort("192.168.0.9", 6379));
        nodes.add(new HostAndPort("192.168.0.10", 6379));
        nodes.add(new HostAndPort("192.168.0.11", 6379));

        GenericObjectPoolConfig poolConfig = new GenericObjectPoolConfig();
        jedis = new JedisCluster(nodes,poolConfig);
    }

    @After
    public void after() throws IOException {
        //使用完关闭连接
        jedis.close();
    }

    /**
     * 设值
     */
    @Test
    public void set() {
        //访问Redis服务
        jedis.set("k1", "v1");
        System.out.println(jedis.get("k1"));
    }

    /**
     * redis集群的节点信息
     * map.key = IP:PORT
     */
    @Test
    public void info() {
        Map<String, JedisPool> clusterNodes = jedis.getClusterNodes();
        clusterNodes.keySet().forEach(key->{
            System.out.printf("%s==%s\r\n",key,clusterNodes.get(key).getResource().info());
        });
    }
}
```

RedisTemplate

```
# 插入
myVO.setCode(typeCode);
redisTemplate.opsForList().rightPush(key, JSONUtil.toJsonStr(myVO));

# 取出
List<String> range = redisTemplate.opsForList().range(key, 0, 100);

# 删除取出的
# trim 是保留的意思
if(range.size()  >0){
    redisTemplate.opsForList().trim(key, range.size() + 1, -1);
}
```