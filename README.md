# docker-compose를 이용한 mongod + flask + 구미콘 + nginx 구축

## 설명

이 프로젝트는 docker-compose를 통해서 flask + 구미콘 + nginx + mongodb를 컨테이너로 만드는 예제 프로젝트이다.

도커 컴포즈를 실행하여 총 3개의 flask, webserver(nginx) + mongodb 컨테이너가 생성된다.

flask는 get, post 등 간단한 rest api를 통해 요청을 받아 mongodb에 데이터를 저장하고 조회하는 기능을 갖는다.

## 도커컴포즈 명령문

도커컴포즈를 보면, 서비스들은 각각의 고유한 데이터 볼륨을 통해 영속적인 데이터를 관리한다.

프론트엔드, 백엔드 총 2개의 내부 내트워크를 사용한다.

### 도커컴포즈 실행

```
docker-compose up -d 
```

### 도커컴포즈 삭제

```
docker-compose down -v
```

주의: 삭제시 이미지가 자동으로 삭제 안되는 경우가 있으니 강제로 삭제해 주자.

## 실행직후 몽고DB에 접속해서 계정 생성

몽고DB를 보안모드로 사용하므로 도커 컨테이너 최초 생성시 몽고DB의 관리자 계정으로 접근하여, 사용할 데이터베이스와 그 db사용자를 생성해야 한다.

### 몽고디비 컨테이너에 접속

```
docker exec -it mongodb bash 
```   

루트로 접근 - 비밀번호 12345678 (docker-compose.yml에 설정된 값)
```
mongo -u hanumoka -p
```

flask 가 사용하는 DB의 계정생성
```
use flaskdb
db.createUser({user: 'flaskuser', pwd: '12345678', roles: [{role: 'readWrite', db: 'flaskdb'}]})
exit
```

생성된 계정 확인

```
mongo -u flaskuser -p 12345678 --authenticationDatabase flaskdb
```

## flask api 테스트로 서비스 동작 확인

flask api 동작 확인 
welcome 요청
```
curl -i http://your_server_ip
```

결과
```
{"message":"Welcome to the Dockerized Flask MongoDB app!","status":true}
```

데이터 저장 요청
```
curl -i -H "Content-Type: application/json" -X POST -d '{"todo": "Dockerize Flask application with MongoDB backend"}' http://your_server_ip/todo
```
결과
```
{"message":"To-do saved successfully!","status":true}
```

저장된 데이터 조회 요청
```
curl -i http://your_server_ip/todo
```
결과
```
{"data":[{"id":"5c9fa25591cb7b000a180b60","todo":"Dockerize Flask application with MongoDB backend"}],"status":true}
```

