## prometheus

### 01. 체크 사항

- prometheus.yml파일의 정보 중 localhost를 각 컨테이너 네임리졸루션에 맞게 수정
- prometheus, gateway-service

![](https://velog.velcdn.com/images/develing1991/post/79b1da21-a38e-4ce2-8dca-d734d78d0e47/image.png)

<br>

### 02. 실행 택1) docker run

- 볼륨 옵션: 내 로컬 prometheus.yml파일 마운트

```bash
docker run -d -p 9090:9090 -v /Users/suhanlee/Desktop/msa-project/prometheus-2.45.2.darwin-amd64/prometheus.yml:/etc/prometheus/prometheus.yml --network benefits-network --name prometheus prom/prometheus
```

### 03. 실행 docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  prometheus:
    container_name: prometheus
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - /Users/suhanlee/Desktop/msa-project/prometheus-2.45.2.darwin-amd64/prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
