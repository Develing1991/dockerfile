## grafana

### 01. 체크 사항

- 그라파나 대시보드에서 프로메테우스 데이터 소스 커넥션 url 네임리졸루션으로 변경

![](https://velog.velcdn.com/images/develing1991/post/568edae4-6ac5-49ca-9f34-2ee1930ffae7/image.png)

<br>

### 02. 실행 택1) docker run

- 볼륨 옵션: 내 로컬 grafana /data폴더 하위 파일들 마운트

```bash
docker run -d -p 3000:3000 --network benefits-network -v /Users/suhanlee/Desktop/msa-project/grafana-v10.2.3/data:/var/lib/grafana
```

### 03. 실행 docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  grafana:
    container_name: grafana
    image: grafana/grafana
    ports:
      - "3000:3000"
    volumes:
      - /Users/suhanlee/Desktop/msa-project/grafana-v10.2.3/data:/var/lib/grafana
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
