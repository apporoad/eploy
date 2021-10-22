## docker-compose
```bash
vim docker-compose.yml

```

```yaml
version: '3'
services:
  rabbitmq:
    image: rabbitmq:3.9-management
    container_name: rabbitmq
    restart: always
    hostname: myRabbitmq
    ports:
      - 15672:15672
      - 5672:5672
    volumes:
      - ./data:/var/lib/rabbitmq
    environment:
      - RABBITMQ_DEFAULT_USER=root
      - RABBITMQ_DEFAULT_PASS=root123

```

```bash
docker-compose up -d

# visit localhost:15672

```

