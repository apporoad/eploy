## docker

```bash
mkdir -p /data/redis/

docker run  --name redis   -p 6379:6379 -v /data/redis:/data -d redis redis-server --appendonly yes  --requirepass 1

docker exec -it redis /bin/bash

redis-cli --pass 1
```

## redis

采用官方Redis集群方案 Redis Cluster

Redis Cluster是一种服务器Sharding技术，3.0版本开始正式提供。



## 物理设置

| **IP**      | **主机名** | **操作系统**      | **包含服务** | **端口说明** | **硬件指标** | **备注** |
| ----------- | ---------- | ----------------- | ------------ | ------------ | ------------ | -------- |
| 192.168.0.X | Redis1     | CentOS7.4 minimal | Redis        | 7000、17000  | 4G2核        | 1A       |
| 192.168.0.X | Redis2     | CentOS7.4 minimal | Redis        | 7000、17000  | 4G2核        | 1B       |
| 192.168.0.X | Redis3     | CentOS7.4 minimal | Redis        | 7000、17000  | 4G2核        | 2A       |
| 192.168.0.X | Redis4     | CentOS7.4 minimal | Redis        | 7000、17000  | 4G2核        | 2B       |
| 192.168.0.X | Redis5     | CentOS7.4 minimal | Redis        | 7000、17000  | 4G2核        | 3A       |
| 192.168.0.X | Redis6     | CentOS7.4 minimal | Redis        | 7000、17000  | 4G2核        | 3B       |

服务器需要相互连接 （6台服务器可以与其他应用共享）

服务器均需要至少千兆以上带宽	



## 安装包

[redis-4.0.14.tar.gz](http://download.redis.io/releases/redis-4.0.14.tar.gz)



## 服务器时间同步

ntp推荐



## selinux

```bash
getenforce

vi /etc/selinux/config

setenforce 0

```



## 基础环境

yum install -y vim gcc g++ gcc-c++ make wget curl net-tools telnet lrzsz

\#防火墙开启

firewall-cmd --zone=public --add-port=7000/tcp --permanent

firewall-cmd --zone=public --add-port=17000/tcp --permanent

firewall-cmd --reload



## ruby

[./ruby.md](./ruby.md)

\#安装redis 组件

```bash
gem install redis
```



## redis

```bash
tar xzvf redis-4.0.14.tar.gz
cd redis-4.0.14
make install
mkdir /opt/redis

cp src/redis-trib.rb /opt/redis/
cp src/redis-server /opt/redis/
cp src/redis-cli /opt/redis/
```



## redis 配置

```bash
cp redis.conf /opt/redis/

mkdir /opt/redis/cluster-info
mkdir -p /data/redis/

#配置节点1

cp /opt/redis/redis.conf  /opt/redis/redis-7000.conf

# 实际ip
export this_ip= xxxxxxxxxxx

#修改配置
sed -i "s@bind 127.0.0.1@bind $this_ip@g" /opt/redis/redis-7000.conf
sed -i "s@port 6379@port 7000@g" /opt/redis/redis-7000.conf
sed -i "s@# cluster-enabled yes@cluster-enabled yes@g" /opt/redis/redis-7000.conf
sed -i "s@# cluster-config-file nodes-6379.conf@cluster-config-file nodes-7000.conf@g" /opt/redis/redis-7000.conf

sed -i "s@# cluster-node-timeout 15000@cluster-node-timeout 5000@g" /opt/redis/redis-7000.conf


sed -i "s@appendonly no@appendonly yes@g" /opt/redis/redis-7000.conf
sed -i "s@daemonize no@daemonize yes@g" /opt/redis/redis-7000.conf
sed -i "s@pidfile /var/run/redis_6379.pid@pidfile /var/run/redis_7000.pid@g" /opt/redis/redis-7000.conf

sed -i 's@logfile \"\"@ logfile \"/data/redis/7000.log\"@g' /opt/redis/redis-7000.conf

#logfile ""
```



## **redis服务开启**

\#所有机器上面的上述配置完成后进行开启操作

```bash
#启动server
/opt/redis/redis-server /opt/redis/redis-7000.conf

#看看是不是1
ps -e |grep redis | wc -l

#连接测试
redis-cli -h xxx -p 7000
```



## 集群开启

\#所有的服务开启后，机器执行集群创建

```bash
/opt/redis/redis-trib.rb create --replicas 1 192.168.0.xxx:7000 192.168.0.xxx:7000 ....
```



## 去除redis警告信息

```bash
vim /etc/rc.local
```

在下面追加内容：

echo 2048 > /proc/sys/net/core/somaxconn

echo never > /sys/kernel/mm/transparent_hugepage/enabled

\#保存退出

 

```bash
#付执行权限
chmod +x /etc/rc.d/rc.local
#将以下内容写入/etc/sysctl.conf文件
vim /etc/sysctl.conf
```

\#写入内容

vm.overcommit_memory=1

\#保存退出



## 开机启动

```bash
vim /etc/systemd/system/redis-server.service
```

 

内容如下：

```properties
[Unit]

Description=Redis Server Manager

After=syslog.target

After=network.target

 

[Service]

Type=forking

ExecStart=/opt/redis/redis-server /opt/redis/redis-7000.conf

ExecStop=pkill redis

 

[Install]

WantedBy=multi-user.target
```

 

设置开机自启动

```bash
systemctl daemon-reload 

systemctl enable redis-server.service
```



## 常用操作

#### 完全重启

#每台机器执行

```bash
pkill redis

/opt/redis/cleanfile.sh

systemctl restart redis-server
```

\#在101机器执行

```bash
/opt/redis/create-cluster.sh
```

#### 简单重启

```bash
pkill redis

systemctl start redis-server.service 
```

#### 查看集群信息

```bash
#连接进入集群信息（任意一台机器的ip都可以）
redis-cli –h 192.168.0.101 –c –p 7000
```

```sh
#查看节点信息
cluster nodes
#查看状态信息
cluster info
#退出
exit
```

#### 修复集群信息

```bash
# 在redis-trib.rb文件所在的目录执行
ruby redis-trib.rb fix xxxxxxxx:7000
```





