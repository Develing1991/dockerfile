## naming-server

### 01. 체크 사항

- 만약 naming-server에도 config-client를 추가 한다면
- 실행 옵션 환경변수에 -e "spring.config.import=optional:configserver:http://config-server:8888" 추가
- 또는 docker-compose.yml에 environment: 에 작성
- -> ### 현재는 없이 진행 ###

<br>

### 02. Dockerfile

- 도커 파일 작성

```docker
FROM gradle:8.5.0-jdk17-jammy AS build
WORKDIR /home/app

# 캐싱
COPY ./build.gradle /home/app/build.gradle
COPY ./src/main/java/com/benefits/namingserver/NamingServerApplication.java /home/app/src/main/java/com/benefits/namingserver/NamingServerApplication.java
RUN gradle clean build -x test

# 캐싱
COPY . /home/app
RUN gradle clean build -x test

FROM openjdk:17-ea-17-jdk-slim
COPY --from=build /home/app/build/libs/*.jar NamingServer.jar
# COPY --from=build /home/app/ /home/app/
ENTRYPOINT [ "java", "-jar", "NamingServer.jar"]
```

<br>

### 03. docker image

- 작성한 Dockerfile을 이미지로 빌드

```bash
docker build -t completed0728/naming-server:1.0 .
```

<br>

### 04. 실행 택1) docker run

```bash
docker run -d -p "8761:8761" --network benefits-network --name naming-server completed0728/naming-server:1.0
```

<br>

### 05. 실행 택2) docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  naming-server:
    container_name: naming-server
    image: completed0728/naming-server:1.0
    ports:
      - "8761:8761"
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
