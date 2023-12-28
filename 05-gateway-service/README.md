## gateway-service

### 01. 체크 사항

- 이후 도커화 할 서비스 중 benefits-config파일에서 작성한 gateway ip를 해당 172.18.0.ip로 변경 해야 함

<br>

### 02. Dockerfile

- 도커 파일 작성

```docker
FROM gradle:8.5.0-jdk17-jammy AS build
WORKDIR /home/app

# 캐싱
COPY ./build.gradle /home/app/build.gradle
COPY ./src/main/java/com/benefits/gatewayservice/GatewayServiceApplication.java /home/app/src/main/java/com/benefits/gatewayservice/GatewayServiceApplication.java
RUN gradle clean build -x test

# 캐싱
COPY . /home/app
RUN gradle clean build -x test

FROM openjdk:17-ea-17-jdk-slim
COPY --from=build /home/app/build/libs/*.jar GatewayService.jar
# COPY --from=build /home/app/ /home/app/
ENTRYPOINT [ "java", "-jar", "GatewayService.jar"]
```

<br>

### 03. docker image

- 작성한 Dockerfile을 이미지로 빌드

```bash
docker build -t completed0728/gateway-service:1.0 .
```

<br>

### 04. 실행 택1) docker run

- 환경변수 네임리졸루션 config-server
- 환경변수 네임리졸루션 rabbitmq
- 환경변수 네임리졸루션 naming-server

```bash
docker run -d -p "8000:8000" --network benefits-network -e "spring.config.import=optional:configserver:http://config-server:8888" -e "spring.rabbitmq.host=rabbitmq" -e "eureka.client.serviceUrl.defaultZone=http://naming-server:8761/eureka" --name gateway-service completed0728/gateway-service:1.0
```

<br>

### 05. 실행 택2) docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  gateway-service:
    container_name: gateway-service
    image: completed0728/gateway-service:1.0
    ports:
      - "8000:8000"
    environment:
      spring.rabbitmq.host: rabbitmq
      spring.config.import: optional:configserver:http://config-server:8888
      eureka.client.serviceUrl.defaultZone: http://naming-server:8761/eureka
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
