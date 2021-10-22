

## download src

http://nginx.org/en/download.html

## just do it 
```bash

mkdir -p  /data/src
cd /data/src

wget -c http://nginx.org/download/nginx-1.20.1.tar.gz


# unzip 
tar -zxf nginx-1.20.1.tar.gz




yum -y install gcc pcre pcre-devel zlib zlib-devel openssl openssl-devel


## other libs

yum install -y libxslt-devel
yum install -y gd-devel
yum install perl-ExtUtils-Embed -y
yum install gperftools-devel -y

## 
/data/src/
git clone https://github.com/cubicdaiya/ngx_dynamic_upstream

cd nginx-1.20.1

./configure  --add-module=/data/src/ngx_dynamic_upstream --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/var/lib/nginx/tmp/client_body --http-proxy-temp-path=/var/lib/nginx/tmp/proxy --http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi --http-uwsgi-temp-path=/var/lib/nginx/tmp/uwsgi --http-scgi-temp-path=/var/lib/nginx/tmp/scgi --pid-path=/run/nginx.pid --lock-path=/run/lock/subsys/nginx --user=nginx --group=nginx --with-compat --with-debug --with-file-aio --with-google_perftools_module --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_degradation_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module=dynamic --with-http_mp4_module --with-http_perl_module=dynamic --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-http_xslt_module=dynamic --with-mail=dynamic --with-mail_ssl_module --with-pcre --with-pcre-jit --with-stream=dynamic --with-stream_ssl_module --with-stream_ssl_preread_module --with-threads 



make -j 4


make install

```

## 服务化
```bash
vim /lib/systemd/system/nginx.service
```

```vi
[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss-lookup.target
 
[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true
 
[Install]
WantedBy=multi-user.target

```

```bash
systemctl daemon-reload

systemctl restart nginx

systemctl enable nginx
```




## 日志按日分隔及清理


```bash

mkdir -p /etc/nginx/script

cat <<EOF > /etc/nginx/script/clear_log.sh
#!/bin/bash
LOGS_PATH=/var/log/nginx
YESTERDAY=\$(date -d "yesterday" +%Y%m%d)

mv \${LOGS_PATH}/access.log \${LOGS_PATH}/access_\${YESTERDAY}.log

kill -USR1 \`ps axu | grep "nginx: master process" | grep -v grep | awk '{print \$2}'\`

cd \${LOGS_PATH}

find . -mtime +7 -name "*20[1-9][0-9]*" | xargs rm -f

exit 0
EOF


chmod +x /etc/nginx/script/clear_log.sh

crontab -e
```

```crontab
0 0 * * * root /etc/nginx/script/clear_log.sh
```



## 禁用ipv6

```nginx
http {
  resolver 127.0.0.1 ipv6=off; 
}
```

```bash
vim /etc/sysctl.conf
```
```config
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
net.ipv6.conf.eth0.disable_ipv6 = 1
net.ipv6.conf.eth1.disable_ipv6 = 1
net.ipv6.conf.eth2.disable_ipv6 = 1
net.ipv6.conf.eth3.disable_ipv6 = 1
```
```bash
sysctl -p

sudo nginx -s reload
```

