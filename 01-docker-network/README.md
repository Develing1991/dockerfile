## 도커 네트워크 172.18.0.0 대역 실행 중인 컨테이너 확인 및 삭제

```bash
docker network ls
```

## 네트워크 생성

```bash
docker network create --gateway 172.18.0.1 --subnet 172.18.0.0/16 benefits-network
```

## 네트워크 확인

```bash
docker network inspect benefits-network
```
