## zipkin

### 01. 체크 사항

- 없음

<br>

### 02. 실행 택1) docker run

```bash
docker run -d -p 9411:9411 --network benefits-network --name zipkin openzipkin/zipkin
```

### 03. 실행 docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  zipkin:
    container_name: zipkin
    image: openzipkin/zipkin
    ports:
      - "9411:9411"
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
