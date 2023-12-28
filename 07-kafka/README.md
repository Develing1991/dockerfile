## kafka

### 01. 체크 사항

- 이후 도커화 할 서비스에 브로커 ip를 172.18.0.101를 작성 해야 하기 때문에
- zookeeper: `172.18.0.100`
- kafak(boker): `172.18.0.101` 로 고정

<br>

### 02. 실행 docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  zookeeper:
    image: wurstmeister/zookeeper
    platform: linux/amd64
    container_name: zookeeper
    ports:
      - "2181:2181"
    networks:
      my-network:
        ipv4_address: 172.18.0.100
  kafka:
    container_name: kafka
    image: wurstmeister/kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 172.18.0.101
      KAFKA_CREATE_TOPICS: "test:1:1"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - zookeeper
    networks:
      my-network:
        ipv4_address: 172.18.0.101
networks:
  my-network:
    external: true
    name: benefits-network # 서브넷  172.18.0.0/255.255.0.0  / 게이트 웨이 172.18.0.1
```
