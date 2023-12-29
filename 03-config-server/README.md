## config-server

### 01. 체크 사항

- config 정보가 native, git-local, github인지 확인
- 공개키 복사 (대신 볼륨으로 변경)
- 도커 내부 공개키 location 정보 변경

![](https://velog.velcdn.com/images/develing1991/post/68c3dfce-8c7a-4463-83e3-f72e37bd5fc5/image.png)

<br>

### 02. Dockerfile

- 도커 파일 작성

```docker
FROM gradle:8.5.0-jdk17-jammy AS build
WORKDIR /home/app

# 캐싱
COPY ./build.gradle /home/app/build.gradle
COPY ./src/main/java/com/benefits/configserver/ConfigServerApplication.java /home/app/src/main/java/com/benefits/configserver/ConfigServerApplication.java
RUN gradle clean build -x test

# 캐싱
COPY . /home/app
RUN gradle clean build -x test

FROM openjdk:17-ea-17-jdk-slim
COPY --from=build /home/app/build/libs/*.jar ConfigServer.jar
# COPY --from=build /home/app/ /home/app/
ENTRYPOINT [ "java", "-jar", "ConfigServer.jar"]
```

<br>

### 03. docker image

- 작성한 Dockerfile을 이미지로 빌드

```bash
docker build -t completed0728/config-server:1.0 .
```

<br>

### 04. 실행 택1) docker run

- 볼륨 옵션: 내 로컬 client.jks공개키 마운트
- 환경변수 네임리졸루션 rabbitmq
- 환경변수 profile default

```bash
docker run -d -p 8888:8888 --network benefits-network -v /Users/suhanlee/Desktop/msa-project/rsakey/client.jks:/client.jks -e "spring.rabbitmq.host=rabbitmq" -e "spring.profiles.active=default" --name config-server completed0728/config-server:1.0
```

<br>

### 05. 실행 택2) docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  config-server:
    container_name: config-server
    image: completed0728/config-server:1.0
    ports:
      - "8888:8888"
    environment:
      spring.rabbitmq.host: rabbitmq
      spring.profiles.active: default
    volumes:
      - /Users/suhanlee/Desktop/msa-project/rsakey/client.jks:/client.jks
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
