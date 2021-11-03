
```bash
vim docker-compose.yml
```

```yml

MinDoc_New:
  image: registry.cn-hangzhou.aliyuncs.com/mindoc/mindoc:v2.0-beta.2
  privileged: false
  restart: always
  ports:
    - 8181:8181
  volumes:
    - /data/docker/minidoc/database:/mindoc/database
    - /data/docker/minidoc/uploads:/mindoc/uploads
  environment:
    - MINDOC_RUN_MODE=prod
    - MINDOC_DB_ADAPTER=sqlite3
    - MINDOC_DB_DATABASE=./database/mindoc.db
    - MINDOC_CACHE=true
    - MINDOC_CACHE_PROVIDER=file
    - MINDOC_ENABLE_EXPORT=false
    - MINDOC_BASE_URL=
    - MINDOC_CDN_IMG_URL=
    - MINDOC_CDN_CSS_URL=
    - MINDOC_CDN_JS_URL=
  dns:
    - 223.5.5.5
    - 223.6.6.6

```