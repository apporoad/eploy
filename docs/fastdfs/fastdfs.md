##  集群设计图

2 x tracker + 2 X storage

主机规划

| IP          | 主机名      | 操作系统 | 服务规划                           | 端口                     | 备注 |
| ----------- | ----------- | -------- | ---------------------------------- | ------------------------ | ---- |
| 192.168.0.1 | fileServer1 | centos6+ | Tracker、storage、Nginx、Keepalive | 23000、22122、8080、8088 |      |
| 192.168.0.2 | fileServer2 | centos6+ | Tracker、storage、Nginx、Keepalive | 23000、22122、8080、8088 |      |

PS：

1. 两台服务器需要相互连接，client可以内网访问2台服务器
2. 两台服务器及web站点之间均需要至少**千兆**以上带宽

## 安装问题列表

[qa](./fastdfs_qa.md)

## 下载组件包

安装包：  

[FastDFS_v5.08.tar.gz](https://sourceforge.net/p/fastdfs/activity/)  

[fastdfs-nginx-module_v1.19.zip](https://github.com/happyfish100/fastdfs-nginx-module/tree/V1.20) 

[nginx-1.10.3.tar.gz](http://nginx.org/download/nginx-1.10.3.tar.gz)  

[keepalived-1.2.22.tar.gz](https://www.keepalived.org/software/keepalived-1.2.22.tar.gz)  官网<http://www.keepalived.org/ >  

[libfastcommon.zip](https://github.com/happyfish100/libfastcommon/tree/V1.0.39)  

下载到 /temp



## 服务器时间同步

设置ntp服务器

略

```bash
yum install -y ntp
```

ntpdate time.nist.gov 

## 基础环境

#### 防火墙

```bash
# centos 6
iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 22122 -j ACCEPT
iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 23000 -j ACCEPT
iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 8088 -j ACCEPT
/etc/init.d/iptables save

# centos 7
firewall-cmd --zone=public --add-port=22122/tcp --permanent
firewall-cmd --zone=public --add-port=23000/tcp --permanent
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=8088/tcp --permanent
firewall-cmd --reload
```

### selinux

```bash
getenforce

vim /etc/selinux/config

setenforce 0

```

## 安装fastdfs

两台主机均需要安装

```bash
yum install -y vim glibc-devel gcc libevent-devel make unzip git
yum install -y libc

#libfastcommon安装
git clone https://github.com/happyfish100/libfastcommon
cd libfastcommon
chmod +x make.sh
./make.sh 
./make.sh install

#fastdfs
tar zxvf FastDFS_v5.08.tar.gz
cd FastDFS
./make.sh
./make.sh install

# 看看
ll /usr/bin/fdfs*

ll /etc/fdfs

```

## 配置运行tracker

两台主机均需要操作

```bash
mkdir -p /fdfs/tracker
cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
vim /etc/fdfs/tracker.conf
```

文件修改以下项：

```c
disabled=false            #启用配置文件
port=22122                #设置tracker的端口号
base_path=/fdfs/tracker   #设置tracker的数据文件和日志目录（需预先创建）
http.server_port=8080     #设置http端口号
```



```bash
#启动
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
#have a look
netstat -unltp | grep fdfs
# 日志
tail -f /fdfs/tracker/logs/trackerd.log

chmod +x /etc/rc.d/rc.local
# 开机启动
vim /etc/rc.d/rc.local
# /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
```



## 配置运行storage

两台主机均需要操作

```bash
mkdir -p /fdfs/storage
cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
vim /etc/fdfs/storage.conf
```

```c
#修改以下项
disabled=false                    #启用配置文件
group_name=group1                 #组名，根据实际情况修改
											ZJ1 ZJ2 group1
											ZJ3 ZJ4 group2
port=23000                        #设置storage的端口号
base_path=/fdfs/storage           #设置storage的日志目录（需预先创建）
store_path_count=1         #存储路径个数，需要和store_path个数匹配
store_path0=/fdfs/storage         #存储路径
tracker_server=192.168.0.1:22122 #ZJ1 tracker服务器的IP地址和端口号
tracker_server=192.168.0.2:22122#ZJ2 tracker ip port
http.server_port=8888     #设置http端口号
```

```bash
#运行
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart

netstat -unltp | grep fdfs
# 日志
tail -f /fdfs/storage/logs/storaged.log

##运行fdfs_monitor查看storage服务器是否已经登记到tracker服务器
/usr/bin/fdfs_monitor /etc/fdfs/storage.conf

chmod +x /etc/rc.d/rc.local
#开机自动启动
vim /etc/rc.d/rc.local
# 	/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart
```

ps：每个group中所有storage的端口号必须一致

## Storage上安装配置nginx

在storage上安装的nginx主要为了提供http的访问服务，同时解决group中storage服务器的同步延迟问题

#### nginx 安装

两台主机均需要操作

```bash
cp /temp/nginx-1.10.3.tar.gz /usr/local/src
cp /temp/fastdfs-nginx-module_v1.19.zip /usr/local/src
cd /usr/local/src
tar zxvf nginx-1.10.3.tar.gz
unzip fastdfs-nginx-module_v1.19.zip
yum install -y pcre pcre-devel zlib zlib-devel openssl openssl-devel

cd nginx-1.10.3
#config
./configure --prefix=/usr/local/nginx --add-module=/usr/local/src/fastdfs-nginx-module-master/src --with-stream
#编译 安装
make && make install
#FastDFS的nginx插件模块的配置文件cp到FastDFS配置文件目录
cp /usr/local/src/fastdfs-nginx-module-master/src/mod_fastdfs.conf /etc/fdfs/
```

#### nginx配置

```bash
#配置nginx
vim /usr/local/nginx/conf/nginx.conf

```

```c
将server段中的listen端口号改为8080：
listen       8080;
在server段中添加：
location ~/group1/M00 {
    #root /fdfs/storage/data;
    ngx_fastdfs_module;
}
```

```bash
#配置fastdfs模块配置
vim /etc/fdfs/mod_fastdfs.conf
```

```c
//一般只需改动以下几个参数即可：
base_path=/fdfs/storage           #保存日志目录
tracker_server=192.168.0.1:22122 #tracker服务器的IP地址以及端口号
tracker_server=192.168.0.2:22122
storage_server_port=23000         #storage服务器的端口号
group_name=group1                 #当前服务器的group名 (ZJ1 ZJ2 group1
									ZJ3 ZJ4 group2)
url_have_group_name = true        #文件url中是否有group名
store_path_count=1                #存储路径个数，需要和store_path个数匹配
store_path0=/fdfs/storage         #存储路径
http.need_find_content_type=true  #从文件扩展名查找文件类型（nginx时为true）
group_count = 1                   #设置组的个数
//在末尾增加3个组的具体信息：
[group1]
group_name=group1
storage_server_port=23000
store_path_count=1
store_path0=/fdfs/storage
```



```bash
#超链接，便于nginx下载
ln -s /fdfs/storage/data /fdfs/storage/data/M00
ln -s /fdfs/storage/data /fdfs/storage/data/group1
ll /fdfs/storage/data/M00
```

#### 启动 nginx

```bash
/usr/local/nginx/sbin/nginx
netstat -unltp | grep nginx

#日志
tail -f /usr/local/nginx/logs/error.log

#开机自启动
chmod +x /etc/rc.d/rc.local
vim /etc/rc.d/rc.local
#添加进文件：/usr/local/nginx/sbin/nginx
```

访问浏览器，查看是否成功：

http://192.168.0.x:8080



## Tracker主机上安装配置nginx

在tracker上安装的nginx主要为了提供http访问的反向代理、负载均衡以及缓存服务

#### nginx安装

```bash
cp /temp/nginx-1.10.3.tar.gz /usr/local/src

cp /temp/fastdfs-nginx-module_v1.19.zip /usr/local/src
cd /usr/local/src
tar zxvf nginx-1.10.3.tar.gz


unzip fastdfs-nginx-module_v1.15.tar.gz
# or github 方式下载
git clone https://github.com/happyfish100/fastdfs-nginx-module.git
cd fastdfs-nginx-module
git checkout V1.20
cd ..

yum install -y pcre pcre-devel zlib zlib-devel gd-devel

cd nginx-1.10.3
./configure --prefix=/usr/local/nginx  --with-http_image_filter_module --with-stream
# tracker 和 storage合用时：
./configure --prefix=/usr/local/nginx --add-module=/usr/local/src/fastdfs-nginx-module-master/src --with-http_image_filter_module  --with-stream 

make && make install
```

#### nginx配置运行

```bash
vim /usr/local/nginx/conf/nginx.conf
#其中 ++++++前面的需要注意修改
```

```nginx
worker_processes  1;                  #根据CPU核心数而定 +++++++++++++++++++++++++++++++++
events {
    worker_connections  65535;        #最大链接数
    use epoll;                        #新版本的Linux可使用epoll加快处理性能
}
http {
#设置缓存参数
    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 300m;
    sendfile        on;
    tcp_nopush      on;
    proxy_redirect off;
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_connect_timeout 90;
    proxy_send_timeout 90;
    proxy_read_timeout 90;
    proxy_buffer_size 16k;
    proxy_buffers 4 64k;
    proxy_busy_buffers_size 128k;
    proxy_temp_file_write_size 128k;
#设置group1的服务器
    upstream fdfs_group1 {
        server 192.168.0.1:8080 weight=1 max_fails=2 fail_timeout=30s;    #+++++++++++++++++++++++++++++
        server 192.168.0.2:8080 weight=1 max_fails=2 fail_timeout=30s;	  #+++++++++++++++++++++++++++++
    }
   server {
#设置服务器端口
        listen       8088;  
#设置group1的负载均衡参数
        location /group1/M00 {
            proxy_next_upstream http_502 http_504 error timeout invalid_header;
            #proxy_cache http-cache;
            proxy_cache_valid  200 304 12h;
            proxy_cache_key $uri$is_args$args;
            proxy_pass http://fdfs_group1;
            expires 30d;
        }
#设置清除缓存的访问权限
        location ~ /purge(/.*) {
            allow 127.0.0.1;
            allow 172.16.1.0/24;
            deny all;
            #proxy_cache_purge http-cache  $1$is_args$args;
        }
}
```

启动nginx，同上



## 测试总体

选取任意主机，准备测试图片G_success.jpg

```bash
vim /etc/fdfs/client.conf
#base_path=/fdfs/tracker                    #日志存放路径
#tracker_server=192.168.0.1:22122          #tracker服务器IP地址和端口号
#http.tracker_server_port=8088              #tracker服务器的http端口号

#上传
/usr/bin/fdfs_upload_file /etc/fdfs/client.conf  /temp/G_success.jpg

# 网页上面查看是否成功
#路径为 http://192.168.0.1:8088/group1/xxxxxxx
#xxxxx 为上传的返回值
```



## HA改造

keepalive方式保证可用性

#### keepalived安装

两台主机均需要安装

```bash
tar zxvf keepalived-1.2.22.tar.gz
cd keepalived-1.2.22
yum -y install popt popt-devel openssl openssl-devel

./configure --prefix=/usr/local/keepalived --sysconf=/etc
make && make install
cp /usr/local/keepalived/sbin/keepalived  /bin/
chkconfig --add keepalived

#开机启动
chkconfig keepalived on

# 服务启动
/etc/init.d/keepalived start
```

#### 配置keepalived

```bash
cp /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf_bak
#配置Master 或者 backup （ master只主要运行机器，backup是备机）
vim /etc/keepalived/keepalived.conf
#其中 ++++++前面的需要注意修改
```

```nginx
! Configuration File for keepalived
global_defs {
	notification_email {
		xxx@163.com
		
	}
	notification_email_from xxx@163.com
	smtp_server smtp.163.com
	smtp_connect_timeout 30
	router_id LVS_DEVEL
}
vrrp_script Monitor_Nginx {
	 script "/root/monitor_nginx.sh"
	 interval 2
	 weight 2
}
vrrp_instance VI_1 {
	state MASTER    #(主机为MASTER，备用机为BACKUP)    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++
	interface eth0  #(HA监测网络接口)

	virtual_router_id 51 #(主、备机的virtual_router_id必须相同) 
	#mcast_src_ip 172.26.11.99 #(多播的源IP，设置为本机外网IP，与VIP同一网卡)此项可不设置
	priority 90 #(主、备机取不同的优先级，主机值较大，备份机值较小,值越大优先级越高)  ++++++++++++++++++++++++++++++++++++++++
	advert_int 1 #(VRRP Multicast广播周期秒数)
	authentication {
		auth_type PASS #(VRRP认证方式)
		auth_pass 123 #(密码)
	}

	track_script {
		Monitor_Nginx #(调用nginx进程检测脚本)
	}
	virtual_ipaddress {
192.168.0.3 #(VRRP HA虚拟地址)   ++++++++++++++++++++++++++++++++++++++++++++++++++++++
	}
}
```

#### 添加监控脚本

```bash
vim /root/monitor_nginx.sh
```

```nginx
#!/bin/bash
if [ "$(ps -ef | grep "nginx: master process"| grep -v grep )" == "" ]
 then
 killall keepalived
fi
```

```bash
# 执行权限
chmod +x /root/monitor_nginx.sh

#防火墙
#在iptables 中加入对vrrp报文的允许  注意书写iptables的时候是-i插入 因为通常大家的iptables最后一条默认都是拒绝的。(vrrp广播报文使用的地址是224.0.0.18)
vim /etc/sysconfig/iptables
#添加iptables -A INPUT -d 224.0.0.18 -j ACCEPT 
service iptables restart
 
#启动服务
service keepalived restart

#开机启动
vim /etc/rc.d/rc.local
#添加进文件：service keepalived restart
```

测试HA，可以关闭某台，看上传下载是否存在问题



## 常用命令

```bash
vim /etc/fdfs/mod_fastdfs.conf
tail -l /usr/local/nginx/logs/error.log
vim /usr/local/nginx/conf/nginx.conf 
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
/usr/local/nginx/sbin/nginx -s reload重启时 最好也启动 keepalived
vim /etc/keepalived/keepalived.conf
service keepalived restart

/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart

#监视
/usr/bin/fdfs_monitor /etc/fdfs/client.conf
```



## 其他说明

1. 一些元数据放在/fdfs/tracker/data 下面
2. 需要监控磁盘，监控磁盘剩余量建议20%（fastdfs会默认留10%空间）

​	