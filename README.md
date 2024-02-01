# dockerfile

## gateway-service 관련해서..

자동 할당 ip방식에서 docker-compose의 작성 순서나 depends_on에 ip가 결정되는 가변적 가능성과 의존성이 존재하기 때문에

benefits-config 설정 파일의 gateway-service.ip를 5번을 지정하는건 무의미 하므로

gateway-serivce의 ip를 50번으로 고정하기로 결정

(예를 들면 docker-compose.yml파일 작성 순서에 앞쪽에는 다른 컨테이너 작성이 없어야 함과
동시에 gateway-service의 앞에는 depends_on이 반드시 4개 존재해야 5번으로 할당 가능)

- user-service, order-service, product-service, review-service, seller-service, supervisor-service 는 gateway-service의 ip로 오는 요청으로 제한했기 때문에 gateway-service의 ip를 직접적으로 컨택해야 하는 상황 즉, 타이트한 결합이 필요 (자동 할당 x, 50번 고정 o)

- 다른 서비스들에선 ip가 아닌 네임리졸루션으로 통신하기 때문에 순서가 상관 없다

## docker-compose healthcheck

https://docs.docker.com/compose/compose-file/05-services/#healthcheck

https://devlifetestcase.tistory.com/88

https://superuser.com/questions/1080239/run-powershell-command-from-cmd

### 해결 과제

config-server가 완전히 실행된 이 후에 config-server 자원을 바라보는 나머지 서비스가 실행되야 모든 서비스가 한 번에 정상 실행 되는데

<del>config-server 의 상태를 체크해도 걔속.. unhealty... 음..</del>

그래서 일단 편법으로 rabbitmq healthcheck는 성공 하니깐..
config-server는 healthcheck 없이 그냥 바로 실행 시키고

rabbitmq의 healthcheck start_period를 약 30초간 주고 시간을 벌음 (이 시간동안 config-server는 이미 정상 업 상태)

gateway-service를 rabbitmq healthcheck 확인을 의존 시킴 (즉 30초 뒤에 rabbitmq healthcheck -> 확인 되면 gateway-service 실행)

나머지 서비스들은 gateway-service를 의존 시키고..

<br><br>

![](https://velog.velcdn.com/images/develing1991/post/143df509-2284-4870-ba70-91bcdfedb675/image.png)

![](https://velog.velcdn.com/images/develing1991/post/569a80eb-3987-4004-b066-5b2989236cae/image.png)
