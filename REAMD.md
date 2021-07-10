# doocker-compose를 이용한 mongod + flask + 구미콘 + nginx 구축

실행
docker-compose up -d 

삭제
docker-compose down -v

삭제시 이미지가 자동으로 삭제 안되는 경우가 있으니 강제로 삭제해 주자.

실행직후 몽고DB에 접속해서 계정 생성

몽고디비 컨테이너에 접속
docker exec -it mongodb bash    

루트로 접근 - 비밀번호 12345678 (docker-compose.yml에 설정된 값)
mongo -u hanumoka -p

flask 가 사용하는 DB의 계정생성
use flaskdb
db.createUser({user: 'flaskuser', pwd: '12345678', roles: [{role: 'readWrite', db: 'flaskdb'}]})
exit

생성된 계정 확인
mongo -u flaskuser -p 12345678 --authenticationDatabase flaskdb


flask api 동작 확인 
요청
curl -i http://your_server_ip
결과
{"message":"Welcome to the Dockerized Flask MongoDB app!","status":true}

요청
curl -i -H "Content-Type: application/json" -X POST -d '{"todo": "Dockerize Flask application with MongoDB backend"}' http://your_server_ip/todo
결과
{"message":"To-do saved successfully!","status":true}

요청
curl -i http://your_server_ip/todo
결과
{"data":[{"id":"5c9fa25591cb7b000a180b60","todo":"Dockerize Flask application with MongoDB backend"}],"status":true}