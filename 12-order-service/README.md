## order-service

### 01. 체크 사항

- 빌드 전 `ObjectMapperConfig.java`파일 확인. 이벤트 버스 `actuator/busrefresh` 기능
- object 네이밍 전략 수정 시.. 이벤트 버스에서 사용하는 메시지 컨버터에 맵핑이 안될 수 있음

<br>

- benefits-config/order-service.yml 파일 수정
- mysql은 환경변수로 실행할 것이기 때문에 변경 해주지 않아도 됨
- docker network inspect benefits-network 명령으로 gateway-service ip 확인 후 변경

![](https://velog.velcdn.com/images/develing1991/post/62f0e71e-7ff4-4e9b-b554-b3119bcb860b/image.png)

<br>

- 카프카 브로커 ip 변경

![](https://velog.velcdn.com/images/develing1991/post/64c7cad5-a103-44b6-9059-6b9dbdbcfca1/image.png)

<br>
<br>

### 02. Dockerfile

- 도커 파일 작성

```docker
FROM gradle:8.5.0-jdk17-jammy AS build
WORKDIR /home/app

# 캐싱
COPY ./build.gradle /home/app/build.gradle
COPY ./src/main/java/com/benefits/orderservice/OrderServiceApplication.java /home/app/src/main/java/com/benefits/orderservice/OrderServiceApplication.java
RUN gradle clean build -x test

# 캐싱
COPY . /home/app
RUN gradle clean build -x test

FROM openjdk:17-ea-17-jdk-slim
COPY --from=build /home/app/build/libs/*.jar OrderService.jar
# COPY --from=build /home/app/ /home/app/
ENTRYPOINT [ "java", "-jar", "OrderService.jar"]
```

<br>

### 03. docker image

- 작성한 Dockerfile을 이미지로 빌드

```bash
docker build -t completed0728/order-service:1.0 .
```

<br>

### 04. 실행 택1) docker run

- 환경변수 네임리졸루션 config-server
- 환경변수 네임리졸루션 rabbitmq
- 환경변수 네임리졸루션 zipkin
- 환경변수 네임리졸루션 naming-server
- 환경변수 네임리졸루션 mysql
- 환경변수 로그 파일

```bash
docker run -d --network benefits-network --name order-service -e "spring.config.import=optional:configserver:http://config-server:8888" -e "spring.rabbitmq.host=rabbitmq" -e "management.zipkin.tracing.endpoint=http://zipkin:9411/api/v2/spans" -e "eureka.client.serviceUrl.defaultZone=http://naming-server:8761/eureka" -e "spring.datasource.url: jdbc:mysql://mysql:3306/orders?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true" -e "logging.file=/api-logs/order-ws.log" completed0728/order-service:1.0
```

<br>

### 05. 실행 택2) docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  order-service:
    container_name: order-service
    image: completed0728/order-service:1.0
    environment:
      spring.rabbitmq.host: rabbitmq
      spring.config.import: optional:configserver:http://config-server:8888
      management.zipkin.tracing.endpoint: http://zipkin:9411/api/v2/spans
      eureka.client.serviceUrl.defaultZone: http://naming-server:8761/eureka
      spring.datasource.url: jdbc:mysql://mysql:3306/orders?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true
      logging.file: /api-logs/order-ws.log
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
