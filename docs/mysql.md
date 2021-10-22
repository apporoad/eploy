



##  docker 安装

```bash
# docker 中下载 mysql
docker pull mysql:5.7

mkdir -p /home/mysql/data
#启动
docker run --name mysql --restart=always -p 3306:3306 -v /home/mysql/data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=xxxxxxxxx   -d mysql:5.7 


#进入容器
docker exec -it mysql bash
exit
#登录mysql
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Lzslov123!';

```

## docker utf 8

```bash
vi my.cnf
# 输入以下内容：
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
collation-server=utf8_general_ci
character-set-server=utf8
init-connect='SET NAMES utf8'

# 保存退出
:wq

vi Dockerfile
# 输入以下内容：
from mysql:5.7
COPY my.cnf /etc/mysql/conf.d/mysqlutf8.cnf
CMD ["mysqld", "--character-set-server=utf8", "--collation-server=utf8_unicode_ci"]


docker build -t mysqlutf8 .


docker run --name mysql --restart=always -p 3306:3306 -v /home/mysql/data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=1   -d mysqlutf8  --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

#进入容器
docker exec -it mysql bash
exit
#登录mysql
mysql -u root -p

## 查看编码
show variables like 'character_set_%';
```


### 数据库备份

```bash
systemctl enable crond
systemctl start cornd

mkdir -p /data/scripts
mkdir -p /data/bak/mysql/
vim /data/scripts/mysql_dumps.sh
```

```bash
#!/bin/bash
docker_name=mysql
data_dir="/data/bak/mysql/"

#here change password
docker exec -it ${docker_name} mysqldump -uroot -pxxxxxxxxx --all-databases > "$data_dir/data_`date +%Y%m%d`.sql"

find $data_dir -mtime +7 -name 'data_[1-9].sql' -exec rm -rf {} \;

crontab -e

0 2 * * * sh /data/scripts/mysql_dumps.sh > /data/scripts/mysql_dumps.log 2>&1
```





## yum安装

```bash
wget http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
rpm -qpl mysql57-community-release-el7-7.noarch.rpm
yum list Mysql* 
yum install mysql-community-server
mysql -V
```



## 创建用户，授权

创建

```bash
#获取初始化密码
grep 'password' /var/log/mysqld.log |head -n 1

#修改密码
set password for root@localhost = password('123')；

mysql -u root -p
#密码


#插入用户
xx> CREATE USER 'test'@'%' IDENTIFIED BY 'password'；
xx> exit

#test
mysql -u test -p

```

授权

```bash
mysql -u root -p
#密码

xx> create database testDB;

xx> grant all privileges on testDB.* to test identified by '1234';

xx> flush privileges;

#所有数据库权限
## mysql>grant select,delete,update,create,drop on *.* to test@"%" identified by "1234";


```

## 其他常见操作

```bash
#删除用户
mysql>Delete FROM user Where User='test' and Host='localhost';
mysql>flush privileges;
mysql>drop database testDB; //删除用户的数据库
#删除账户及权限：
mysql>drop user 用户名@'%';
mysql>drop user 用户名@ localhost; 
#修改指定用户密码
mysql>update mysql.user set password=password('新密码') where User="test" and Host="localhost";
mysql>flush privileges;
#列出所有数据库
mysql>show database;
#切换数据库
mysql>use '数据库名';
#列出所有表
mysql>show tables;
#显示数据表结构
mysql>describe 表名;
#删除数据库和数据表
mysql>drop database 数据库名;
mysql>drop table 数据表名;
```

