## user-service

### 01. 체크 사항

- benefits-config/user-service.yml 파일 수정
- mysql은 환경변수로 실행할 것이기 때문에 변경 해주지 않아도 됨
- docker network inspect benefits-network 명령으로 gateway-service ip 확인 후 변경

![](https://velog.velcdn.com/images/develing1991/post/d76d7d13-5f93-471f-b35c-acc74cb9e4c0/image.png)

<br>
<br>

### 02. Dockerfile

- 도커 파일 작성

```docker
FROM gradle:8.5.0-jdk17-jammy AS build
WORKDIR /home/app

# 캐싱
COPY ./build.gradle /home/app/build.gradle
COPY ./src/main/java/com/benefits/userservice/UserServiceApplication.java /home/app/src/main/java/com/benefits/userservice/UserServiceApplication.java
RUN gradle clean build -x test

# 캐싱
COPY . /home/app
RUN gradle clean build -x test

FROM openjdk:17-ea-17-jdk-slim
COPY --from=build /home/app/build/libs/*.jar UserService.jar
# COPY --from=build /home/app/ /home/app/
ENTRYPOINT [ "java", "-jar", "UserService.jar"]
```

<br>

### 03. docker image

- 작성한 Dockerfile을 이미지로 빌드

```bash
docker build -t completed0728/user-service:1.0 .
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
docker run -d --network benefits-network --name user-service -e "spring.config.import=optional:configserver:http://config-server:8888" -e "spring.rabbitmq.host=rabbitmq" -e "management.zipkin.tracing.endpoint=http://zipkin:9411/api/v2/spans" -e "eureka.client.serviceUrl.defaultZone=http://naming-server:8761/eureka" -e "spring.datasource.url: jdbc:mysql://mysql:3306/users?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true" -e "logging.file=/api-logs/users-ws.log" completed0728/user-service:1.0
```

<br>

### 05. 실행 택2) docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  user-service:
    container_name: user-service
    image: completed0728/user-service:1.0
    environment:
      spring.rabbitmq.host: rabbitmq
      spring.config.import: optional:configserver:http://config-server:8888
      management.zipkin.tracing.endpoint: http://zipkin:9411/api/v2/spans
      eureka.client.serviceUrl.defaultZone: http://naming-server:8761/eureka
      spring.datasource.url: jdbc:mysql://mysql:3306/users?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true
      logging.file: /api-logs/users-ws.log
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
