## rabbitmq

### docker

```bash
docker run -d --name rabbitmq --network suhan-network -p 15672:15672 -p 5672:5672 -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin123 rabbitmq:management
```

<br>

### docker-compose.yml

```bash
docker compose up -d
```

```yml
version: '3.7'
services:
  rabbitmq:
  	container_name: rabbitmq
    image: rabbitmq:management
    ports:
      - "5672:5672" # rabbitmq amqp port
      - "15672:15672" # ui manage port
    environment:
      - RABBITMQ_DEFAULT_USER=admin
      - RABBITMQ_DEFAULT_PASS=admin123
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
