---
title: "Docker 설치하기"
date: 2021-09-29 08:00:00 +0900
categories: Docker
---

# Docker 설치

Docker Desktop을 설치하는 macOS, windows의 경우에는 그냥 공식 사이트를 보고 설치하는 게 가장 편할 거 같습니다.
일단은 이 포스트에서는 Linux에서 가장 간단하게 설치하는 법만 작성하겠습니다.

## Linux

가장 간단한 방법으로 curl이 설치되어있다는 가정 하에 아래와 같은 명령어를 사용해주시면 됩니다.

```
curl -fsSL https://get.docker.com/ | sudo sh
```

보통은 root 유저 권한으로 실행하기 위해 `sudo` 명령어를 포함할텐데 sudo 없이 사용하기 위해서는
usermod를 통한 권한 조정이 필요합니다.

### sudo 권한 부여

```
sudo usermod -aG docker $USER # 현재 사용자에게 권한 부여
sudo usermod -aG docker 유저명 # 별도의 사용자에게 권한 부여
```

## Compose 설치

도커를 좀 더 유연하고 통합적인 설계로 사용하고 싶다면 docker-compose를 이용하는 것이 좋은 방법입니다.
도커를 관리하는 방법은 Dockerfile, Docker Compose 2가지 방식을 제공하는데

Dockerfile은 하나의 서비스에 필요한 설정을 작성하기 좋은 방식이고 사용하기 간단합니다.

Docker Compose는 여러 개의 서비스를 한번에 관리하기 좋은 방식이며 Dockerfile 실행도 지원하기 때문에
각자의 서비스에 필요하다면 Dockerfile을 작성하고 이것을 통합적으로 Docker Compose에서 제어하는 것을 추천하고 싶습니다.

### compose install

간단하게 curl을 활용하여 설치가 가능합니다.

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

바이너리에 실행 권한을 적용해야 합니다.

```
sudo chmod +x /usr/local/bin/docker-compose
```

설치가 제대로 됐는지 테스트를 해봅니다.

```
docker-compose --version
```

docker-compose.yml을 작성하는 방식 등은 docker 사용 후기 등을 작성하면서 자연스럽게 포스팅 할 예정입니다.
