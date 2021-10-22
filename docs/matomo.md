## centos 7 基础环境

```bash
#selinux 关闭
setenforce 0
getenforce

vim /etc/sysconfig/selinux
#SELINUX=enforcing 改为 SELINUX=disabled

#防火墙
systemctl disable firewalld
systemctl stop firewalld
```



## php

```bash
yum install epel-release
rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

# refs
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel
yum install -y curl
yum install openssl openssl-devel
yum -y install curl-devel
yum install libjpeg.x86_64 libpng.x86_64 freetype.x86_64 libjpeg-devel.x86_64 libpng-devel.x86_64 freetype-devel.x86_64 -y
yum install libjpeg-devel
yum  install  php-mcrypt  libmcrypt  libmcrypt-devel -y
yum install bzip2 bzip2-devel -y
yum -y install libxslt libxslt-devel
yum install libzip libzip-devel -y

# install
yum install --enablerepo=remi,remi-php56 php php-opcache php-pecl-apcu php-devel php-mbstring php-mcrypt php-mysqlnd php-phpunit-PHPUnit php-pecl-xdebug php-pecl-xhprof php-pdo php-pear php-fpm php-cli php-xml php-bcmath php-process php-gd php-common


# start
php-fpm

# auto start
chkconfig --add php-fpm
```

## nginx

```bash
#官网 下载  
## 编译安装

```

配置 nginx.conf  如下

```nginx
server {
    listen       80;
    server_name  xxxx;
    root  /data/www;

    location / {
        index  index.html index.htm;
    }

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root /data/www
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

### mysql

```bash



```



## matomo

