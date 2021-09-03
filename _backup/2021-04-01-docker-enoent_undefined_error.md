---
title: 'npm ERR! enoent undefined ls-remote -h -t'
date: 2021-04-01 9:31:00 +0900
categories: Docker
tags:
  - docker
---

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