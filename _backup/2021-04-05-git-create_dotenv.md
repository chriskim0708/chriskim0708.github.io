---
title: 'GIT: gitlab-runner create .env file'
date: 2021-04-05 15:25:00 +0900
categories: Git
tags:
  - git
  - gitlab
---

배포 방식에 따라 다르겠지만 나의 경우에는 환경 변수인 .env를 .gitignore에 등록하여 상태 관리에 포함시키지 않는다.

이유는 여러가지가 있지만 보통 자신의 local .env의 경우에는 다른 작업자와 공유해도 별 의미도 없거니와
production .env를 아무리 private 프로젝트라 하더라도 remote에 올려서 저장하는 것은 껄끄러운 부분이 있어서이기도 하다.

가장 큰 이유는 운영 배포 서비스가 단순히 1개의 인스턴스에서 배포되는 구조가 아니라 여기저기 다른 production 환경 변수로 설치되어야 하는 경우에는
거기에 따라 .env를 무작정 늘려주는 건 효율이 떨어지기 때문에 어느 정도에 가이드 파일만 만든 후에 배포 인스턴스에서 .env를 설정하여 배포하는 구조로 작업한다.

# docker error
```
npm ERR! code ENOENT
npm ERR! syscall spawn git
npm ERR! path git
npm ERR! errno -2
npm ERR! enoent Error while executing
npm ERR! enoent undefined ls-remote -h -t ssh://git@github.com/eligrey/FileSaver.js.git
```

# solution
```
// Dockerfile
RUN apt-get install -y git
RUN git --version
```