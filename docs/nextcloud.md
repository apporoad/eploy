## just do it

```bash

mkdir -p /data/nextcloud_compose

cd /data/nextcloud_compose

vim docker-compose.yml

```

```yml
version: '3'
services:
  nextcloud:
    image: nextcloud:apache
    container_name: nextcloud_web
    depends_on:
      - redis
    environment:
      - UID=1000
      - GID=1000
      - UPLOAD_MAX_SIZE=10G
      - APC_SHM_SIZE=128M
      - OPCACHE_MEM_SIZE=128
      - CRON_PERIOD=15m
      - TZ=Aisa/Shanghai
      - DB_TYPE=mysql
      - DB_NAME=nextcloud
      - DB_USER=admin
      - DB_PASSWORD=admin123
      - DB_HOST=nextcloud-db
    volumes:
       - /docker/nextcloud:/var/www/html
    expose:
      - 7009
    ports:
      - 7009:80/tcp
    restart: always

  redis:
    image: redis:alpine
    container_name: redis
    restart: always
    expose:
      - 6379
    restart: always

  onlyoffice:
    image: onlyoffice/documentserver
    container_name: onlyoffice
    hostname: onlyoffice
    environment:
      - REDIS_SERVER_HOST=redis
      - REDIS_SERVER_PORT=6379
    volumes:
      - /docker/onlyoffice/data:/var/www/onlyoffice/Data
      - /docker/onlyoffice/logs:/var/log/onlyoffice
    restart: always
    expose:
      - 9000
      - 9001
    ports:
      - 9000:80
      - 9001:443
    links:
      - db


  db:
    image: postgres
    container_name: nextclouddb
    restart: always
    ports:
      - 5432:5432
    volumes:
      - /docker/postgre/db:/var/lib/postgres
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
      POSTGRES_DB: nextcloud
```


```bash
docker-compose up -d

```



访问 localhost:7009即可


## 集成onlyoffice

以管理员身份登录nextCloud，
单击【admin】 -> 【+Apps】
单击搜索图标并填入“onlyoffice”
在“onlyoffice”上单击【Download and enable】等待下载并启用即可

也可以采用离线模式
安装到/docker/nextcloud/apps

```bash
 docker exec -u 33 -it nextcloud_web  ./occ config:system:set allow_local_remote_servers --value true --type bool
 ```

 ## nginx反向代理配置注意点
 ```nginx
  client_max_body_size 10G;
  client_header_buffer_size 5120k;
  large_client_header_buffers 16 5120k;
  sendfile            on;
  tcp_nopush          on;
  tcp_nodelay         on;
  keepalive_timeout   65;
  types_hash_max_size 4096;
  add_header X-Content-Type-Options nosniff;
  add_header X-XSS-Protection "1; mode=block";
  add_header X-Robots-Tag none;
  add_header X-Download-Options noopen;
  add_header X-Permitted-Cross-Domain-Policies none;
  add_header Strict-Transport-Security "max-age=15768000";

  gzip on;
  gzip_vary on;
  gzip_comp_level 3;
  gzip_min_length 1024;
  gzip_proxied expired no-cache no-store private no_last_modified no_etag auth;
  gzip_types application/atom+xml application/javascript application/json application/ld+json application/manifest+json application/rss+xml application/vnd.geo+json application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/bmp image/svg+xml image/x-icon text/cache-manifest text/css text/plain text/vcard text/vnd.rim.location.xloc text/vtt text/x-component text/x-cross-domain-policy; 
 



 ```