---
title: "npm ERR! enoent undefined ls-remote -h -t"
date: 2021-04-01 9:31:00 +0900
categories: 개발환경
---

Dockerfile을 작성하고 빌드하는 과정에서 아래와 같은 오류를 만나 해결한 방법을 적어본다.

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
