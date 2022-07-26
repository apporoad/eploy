## install
官网docker compose 安装

## 邮件发布出去问题

```bash
## cd docker-compose dir

vim sentry/config.yml

```

```config

mail.backend: 'django_smtp_ssl.SSLEmailBackend'  # Use dummy if you want to disable email entirely
mail.host: 'smtp.exmail.qq.com'
mail.port: 465
mail.username: 'xxxx@xxxxx.com'
mail.password: 'xxxxxxxxxx'
mail.use-tls: true
# mail.use-ssl: false
# The email address to send on behalf of
mail.from: 'xxxxxxxx@xxxxxxxxxxx.com同mail.username'
```

如果有25端口
```config

mail.host: 'smtp.exmail.qq.com'
mail.port: 25
mail.username: 'xxxx@xxxxx.com'
mail.password: 'xxxxxxxxxx'

# The email address to send on behalf of
mail.from: 'xxxxxxxx@xxxxxxxxxxx.com同mail.username'
```

## 插件

```bash
vim sentry/requirements.txt 
```

```config
django-smtp-ssl
sentry-dingding
```


```bash
docker-compose build
docker-compose up -d
```


## 组件缺失

```bash

docker exec -it sentry-self-hosted-web-1 /bin/sh
pip install django-smtp-ssl
docker exec -it sentry-self-hosted-worker-1 /bin/sh
pip install django-smtp-ssl
```

## 常用命令
```bash
docker compose up -d --force-recreate
```


## nginx 配置
```nginx
upstream sentry-server {
        server 192.168.0.173:9000;
}

location ^~ /sentry/ {
        proxy_pass         http://sentry-server/;
        proxy_redirect    off;
        proxy_set_header   Host              $host;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto    $scheme;
}

```

