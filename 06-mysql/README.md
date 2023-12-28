## mysql

### 01. 체크 사항

- 이후 도커화 할 서비스에 datasource.url 환경 변수로 localhost 대신 mysql 네임리졸루션으로 지정

<br>

### 02. 실행 택1) docker run

- 볼륨 옵션: 내 로컬 /mysql 폴더 하위의 데이터 마운트
- 기타 옵션 (캐릭터 셋)

```bash
docker run -d -p "3306:3306" --network benefits-network --restart=always -v /Users/suhanlee/Desktop/msa-project/mysql/database:/var/lib/mysql --name mysql completed0728/mysql:1.0 --lower_case_table_names=1 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

<br>

### 03. 실행 택2) docker compose up -d

```bash
docker compose up -d
```

```yml
version: "3.7"
services:
  mysql:
    image: mysql:8.0.33
    platform: linux/amd64
    restart: always
    command:
      - --lower_case_table_names=1
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      - MYSQL_DATABASE=msa
      - MYSQL_ROOT_PASSWORD=root1234!!
      - TZ=Asia/Seoul
    volumes:
      - /Users/suhanlee/Desktop/msa-project/mysql/database:/var/lib/mysql
    networks:
      my-network:
networks:
  my-network:
    external: true
    name: benefits-network
```
