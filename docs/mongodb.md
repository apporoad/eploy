## mongo



### 包

[mongo 3.2](http://downloads.mongodb.org/linux/mongodb-linux-x86_64-rhel70-3.2.16.tgz)



## 基础环境

```bash
firewall-cmd --zone=public --add-port=27800/tcp --permanent

firewall-cmd --reload

## selinux 也要关闭
```

#### 时间同步



## mongo docker方式安装使用

```bash
docker pull mongo
docker images mongo

#启动容器
mkdir -p /data/db
cd /data
docker run  --name mongo1 -p 27017:27017 -v $PWD/db:/data/db -d mongo


#docker exec -it xxxxxxxxxx mongo --host 172.17.0.1
# 登录
docker exec -it $(docker ps | grep mongo1 | awk 'NR > 0 {print $1}') mongo
#--host 172.17.0.1

```





### mongo 生产方式安装

错误提示：./bin/mongod: error while loading shared libraries: libssl.so.6: cannot open shared object file: No such file or directory

解决方案：

```bash
yum -y whatprovides libstdc++.so.6
yum install libstdc++-4.8.5-16.el7_4.1.i686
ln -sf /usr/lib64/libssl.so.10 /usr/lib64/libssl.so.6
ln -sf /usr/lib64/libcrypto.so.10 /usr/lib64/libcrypto.so.6
```



```bash
tar xvzf mongodb-linux-x86_64-rhel70-3.2.16.tgz
mv mongodb-linux-x86_64-rhel70-3.2.16 /usr/mongodb

mkdir -p /data/mongo/db
mkdir -p /data/mongo/logs
vim  /usr/mongodb/bin/mongodb.conf
```

编辑以下内容

```properties
# MongoDB config start - 2017-08
# 设置数据文件的存放目录
dbpath =/data/mongo/db
# 设置日志文件的存放目录及其日志文件名
logpath =/data/mongo/logs/mongodb.log
# 设置端口号（默认的端口号是 27017）
port = 27800
# 设置为以守护进程的方式运行，即在后台运行
fork = true
# 设置集群
replSet = janus
# nohttpinterface = true
nohttpinterface = true
# MongoDB config end - 2017-08
```



### 运行

```bash
#启动
/usr/mongodb/bin/mongod --config /usr/mongodb/bin/mongodb.conf

ps aux |grep mongodb

## 设置开机启动
vim /etc/rc.d/rc.local
```



### 集群配置

```bash
/usr/mongodb/bin/mongo --port=27800

```

```mysql
use janus

rs.initiate({ _id:"janus", members:[ {_id:0,host:'192.168.0.197:27800',priority:2}, {_id:1,host:'192.168.0.198:27800',priority:1}] }) 

slave加上:

rs.slaveOk();
```



### 用户管理

```mysql
use xxxx
db.createUser({user: "dba",pwd: "xxxxx",roles: [ { role: "readWrite", db: "xxx" } ]})
```



 

