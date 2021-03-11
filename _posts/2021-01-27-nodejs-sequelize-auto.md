---
title: 'CommandLine에서 xlsx 파일을 csv 파일로 변환하기'
date: 2021-01-27 15:44:00 +0900
categories: Nodejs
tags:
  - nodejs
  - sequelize
---

sequelize는 관계형 데이터베이스(RBDMS)에 Query문을 사용하지 않고 CRUD를 가능하게 해주는 ORM(Object-Relational Mapping)이다.
다만, sequelize를 사용할 때 각 데이터베이스의 스키마 정의가 필요하다.
처음부터 sequelize로 작업하지 않고 중간에 sequelize를 사용한 코드 리팩토링을 진행하려고 하는 경우에는 이미 정의된 데이터베이스 스키마를 일일히 작업하기란 여간 어려운 부분이 아닐 수 없다.
sequelize-auto는 sequelize를 사용하기 위한 스키마 정의를 사람 손을 거치지 않고 자동으로 생성해주는 편리한 도구이다.

이번 포스트에서는 sequelize-auto를 사용하는 법을 간단하게 작성해보았다.

필수 dependencies 설치
### LINUX
```
npm install -g sequelize
npm install -g mysql2
npm install -g sequelize-cli
npm install -g sequelize-auto
```

위 3가지 의존성을 global로 설치해주면 된다.
local에 설치해서 사용도 가능하지만 이래저래 자주 쓰게 되서 global로 설치했다.

주로 활용하는 옵션을 정리해보았다.

### 명령어
```
sequelize-auto -o "./models" -d databasename -h host -u username -p port -x password -t tablename -e rdbmstype
```

### 옵션 해석
```
-o: 스키마 파일을 생성할 경로
-d: 데이터베이스(혹은 스키마) 이름
-h: 데이터베이스 host
-p: 데이터베이스 port
-u: 접속 아이디
-x: 패스워드
-t: 특정 테이블만 가져오고 싶은 경우 추가 // 없으면 전체 테이블을 생성함
-e: 데이터베이스 타입 // mysql, postgre 등
```

이미 존재하는 데이터베이스의 스키마를 일일히 작성하는 수고를 덜더록 하자.
express 서버 시작 시에 sequelize-auto를 사용하여 스키마 파일을 생성하게 하는 방법도 있지만 나중에 따로 다루도록 하겠다.

