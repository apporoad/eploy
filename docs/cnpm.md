### 环境准备

（ps 有一些问题，推荐使用sinopia

centos7+  具体查看 [centos.md](centos.md)

### install node

参考 node.md

### install db

选择mysql  mysql.md

### install cnpmjs.org 服务端

```bash
su - node
cd /data/bin/
git clone https://github.com/cnpm/cnpmjs.org.git
cd cnpmjs.org/
npm install
```

### init db

```sql
CREATE DATABASE cnpmjs;
GRANT ALL ON cnpmjs.* ON cnpmjs@'127.0.0.1' IDENTIFIED BY 'YourPassword';
FLUSH PRIVILEGES;
```

### import data into db

```bash
mysql -uroot cnpmjs < docs/db.sql
//or
mysql -u yourAccount -p cnpmjs < docs/db.sql
```



### edit config 

```bash
vim config/index.js
```

```js
//启动集群
enableCluster: true,
//端口设置，registryPort为注册服务端口，webPort为Web访问端口，默认即可
registryPort: 7001,
webPort: 7002,
//管理员设置
admins: { admin : 'admin@holyond.top', },
//数据库配置
db: 'cnpmjs',
username: 'cnpmjs',
password: 'YourPassword',
dialect: 'mysql',
host: '127.0.0.1',
port: 3306,
//绑定监听地址，可选项。若非本机需要访问，则添加符号//注释此行。亦可在本机架设Nginx做反向代理，则无须修改此行。
//bindingHost: '127.0.0.1',
//注册主机名修改为对应IP地址或域名
registryHost: 'yourDNSOrIp',
//允许发布私有包必须具有的命名前缀  设置 scope
scopes: [ '@holyond' ],
//设置同步源，默认即可
sourceNpmRegistry: 'https://registry.npm.taobao.org',
//同步模式，none不同步只代理源公有模块；exist只同步已存在的模块；all同步所有模块。
syncModel: 'exist', 
```



### 启动服务

```bash
bin/nodejsctl start
```





## 用户客户端安装及配置

```bash
npm install -g cnpm

cnpm set registry http://yourDNS:7001

#设置代理
npm config set proxy=http://127.0.0.1:7001
# https
npm config set https-proxy http://server:port

# 取消代理
npm config delete proxy
npm config delete https-proxy

```



## 用户注册及登录

```bash
## 注册
npm adduser

## 登录
npm login

```

