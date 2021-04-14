---
title: 'docker로 mongoDB 설치하기'
date: 2021-03-30 10:50:00 +0900
categories: Docker
tags:
  - docker
  - mongo
---

# mongo images pull
[Docker Hub](https://hub.docker.com/)를 통해서 먼저 pull 받을 mongo 목록을 찾은 후
간단하게 해당 목록에서 제공하는 pull 명령어를 사용하자.

```
docker pull mongo
```

# docker-compose.yml 설정
docker-compose는 설치되어 있다는 전제로 진행한다.

`docker-compose.yml` 파일 생성 후 아래와 같이 작성하자.

```rust
verison: '3.8'
services:
    mongodb:
        image: 'mongo'
        container_name: 'mongodb'
        restart: 'always'
        environment:
            MONGO_INITDB_ROOT_USERNAME: root
            MONGO_INITDB_ROOT_PASSWORD: tech!234
        volumes:
            - ./mongo/data:/data/db
        ports:
            - '27017:27017'
```

위와 같이 작성 후 volumes으로 사용할 폴더를 생성해준다.
./와 같은 경로는 docker-compose.yml 파일이 있는 곳을 기준으로 한다.

적절한 곳에 docker-compose.yml 파일과 volumes로 활용할 폴더를 생성해준다.

volumes 설정 기준은 `./mongo/data` 부분이 docker-compose.yml 파일 기준 인스턴스 폴더 경로이고
`:`를 사용하여 도커 폴더와 구분을 해준 후에 `/data/db`와 같이 도커 내 data 폴더를 지정해준다.

environment로 설정한

```
MONGO_INITDB_ROOT_USERNAME: root
MONGO_INITDB_ROOT_PASSWORD: tech!234
```

이 부분은 mongo가 처음 인스톨 될 때 사용할 관리자 계정 정보라고 생각하면 된다.

여기까지 설정이 끝났다면 

```
docker-compose up -d
```

명령어를 통해 컨테이너를 실행하자.

빌드가 되지 않은 경우 빌드를 먼저 한 후 실행하게 되며 -d 명령어를 통해 백그라운드에서 계속 실행되는 상태로 유지된다.
