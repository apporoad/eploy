

### docker

```bash
docker pull postgres

docker volume create v_pgdata

docker run --restart=always  --name pg -v v_pgdata:/var/lib/postgresql/data -e POSTGRES_PASSWORD=save11 -p 5432:5432 -d postgres
```

pgadmin

```bash
docker pull dpage/pgadmin4:4.17
docker run --restart=always   --name pgadmin -p 5080:80 \
    -e 'PGADMIN_DEFAULT_EMAIL=xxx@xxx.com' \
    -e 'PGADMIN_DEFAULT_PASSWORD=save11' \
    -e 'PGADMIN_CONFIG_ENHANCED_COOKIE_PROTECTION=True' \
    -e 'PGADMIN_CONFIG_LOGIN_BANNER="Authorised users only!"' \
    -e 'PGADMIN_CONFIG_CONSOLE_LOG_LEVEL=10' \
    -d dpage/pgadmin4:4.17
```



账号:   

db： postgres  

user： postgres