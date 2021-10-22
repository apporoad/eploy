## 源码安装
[文档](./nginx/nginx_prod.md)

## docker

```bash
docker pull nginx
	
#docker run --name runoob-nginx-test -p 8081:80 -d nginx

mkdir -p /data/nginx/www
mkdir -p /data/nginx/logs
docker run -d -p 10000:80 --restart=always --name nginx-test -v /data/nginx/www:/usr/share/nginx/html -v /data/nginx/logs:/var/log/nginx nginx
#-v ~/nginx/conf/nginx.conf:/etc/nginx/nginx.conf 

```

### ssh反向代理配置
```nginx

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

stream {
	upstream ssh191{
		server 192.168.112.191:22;
	}
	upstream ssh192{
		server 192.168.112.192:22;
	}
	upstream ssh193{
		server 192.168.112.193:22;
	}
	
	server {
		listen 52191 so_keepalive=on;
		proxy_connect_timeout 10h;
		proxy_timeout 10h;
		proxy_pass ssh191;
	}
	server {
		listen 52192 so_keepalive=on;
		proxy_connect_timeout 10h;
		proxy_timeout 10h;
		proxy_pass ssh192;
	}
	server {
		listen 52193 so_keepalive=on;
		proxy_connect_timeout 10h;
		proxy_timeout 10h;
		proxy_pass ssh193;
	}

}


```


### 根据参数代理服务

```nginx
location / {
    if ($query_string ~*  ^(.*)corpId=aaa(.*)$) {
        
        #set $tempUrl http://localhost:11540/;
        set $tempUrl $scheme://10.10.10.10:8085$request_uri;
        # proxy_pass $tempUrl;
    }
    #proxy_pass http://$tempUrl/;
    # add_header Content-Type text/plain;
    # return 200 'hello hello good day';
    proxy_set_header Host $host;
    proxy_set_header  X-Real-IP  '';
    proxy_set_header  X-Forwarded-For '';
    proxy_pass $tempUrl;
}

```

### ssl方式配置
准备证书到响应位置

```nginx
server {
listen  443 ssl;
server_name test.fuck.com;

ssl_certificate     /usr/local/nginx/ssl.cer;
ssl_certificate_key  /usr/local/nginx/private.key;
ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers         HIGH:!aNULL:!MD5;

location / {
    add_header Content-Type text/plain;
    return 200 'hello hello good good day!!!';
}

error_page  500 502 503 504  /50x.html;
    location = /50x.html {
    root                        html;
}
}

```

### 编码问题，代理windows
charset gb2312;

```nginx

server {
listen  443 ssl;
server_name xxxxxx;

# gzip off;

location / {
    charset gb2312;
    add_header Content-Type text/plain;
    return 200 'hello hello good good day!!!';
}

error_page  500 502 503 504  /50x.html;
    location = /50x.html {
    root                        html;
}
}

```

### 隐匿方式代理
```nginx

location / {
    proxy_hide_header X-Powered-By;
    proxy_hide_header X-Real-IP;
    proxy_hide_header X-Forwarded-For;
    proxy_hide_header host;
    proxy_hide_header origin;
    proxy_hide_header connection;
    proxy_hide_header keep-alive;

    # resolver 100.100.2.136;
    proxy_pass http://127.0.0.1:8080$request_uri;
    
}
```

### 加header
```nginx

location / {
    add_header Content-Type text/plain;
	add_header hello world;
    # resolver 100.100.2.136;
    proxy_pass http://127.0.0.1:8080$request_uri;
    
}
```


### qps限制

```nginx
http {
    limit_req_zone $binary_remote_addr zone=perip:10m rate=1r/s;
    limit_req_zone $server_name zone=perserver:10m rate=10r/s;

    server {
        limit_req zone=perip burst=20 nodelay;
        limit_req zone=perserver burst=10;
    }
}

```


### 跨域
```nginx
location / {

    if ($request_method = 'OPTIONS') {
        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Access-Control-Allow-Methods' 'GET,POST,OPTIONS,PUT,DELETE' always;
        add_header 'Access-Control-Allow-Headers' '*' always;
        add_header 'Access-Control-Max-Age' 1728000 always;
        add_header 'Content-Length' 0;
        add_header 'Content-Type' 'text/plain; charset=utf-8';
        return 204;
    }

    if ($request_method ~* '(GET|POST|DELETE|PUT)') {
        add_header 'Access-Control-Allow-Origin' '*' always;
    }

}
```



### 定时删除文件脚本

```bash
mkdir -p /etc/nginx/script

vim /etc/nginx/script/clear_log.sh
```

```sh
#!/bin/bash
LOGS_PATH=/var/log/nginx
YESTERDAY=$(date -d "yesterday" +%Y%m%d)
mv ${LOGS_PATH}/access.log ${LOGS_PATH}/access_${YESTERDAY}.log
kill -USR1 `ps axu | grep "nginx: master process" | grep -v grep | awk '{print $2}'`
cd ${LOGS_PATH}
find . -mtime +7 -name "*20[1-9][0-9]*" | xargs rm -f
exit 0

```


```bash
crontab -e
```

```sh
0 0 * * *  /etc/nginx/script/clear_log.sh
```