### install by docker-compose

```bash
mkdir -p /data/showdoc

vim docker-compose.yml

```

```yaml
version: '3.5'
services:
  showdoc:
    image: star7th/showdoc
    container_name: showdoc
    hostname: showdoc
    restart: always
    ports:
      - 4999:80
    volumes:
      - /data/showdoc:/var/www/html
    privileged: true

```

