---
title: 'GIT: gitlab-runner create .env file'
date: 2021-04-05 15:25:00 +0900
categories: Git
tags:
  - nuxtjs
---

최근에 진행한 프로젝트 중 기존의 vue 프로젝트에 pages 컴포넌트를 하나 만들고
그 안에 iframe을 활용하여 다른 nuxtjs의 앱을 띄워서 제어하는 개발을 진행했다.

그리고 그 nuxtjs 앱에서는 parent의 localstorage에 접근해서 jwt 인증 토큰을 가져온 후 재사용하여
인증하는 개념으로 개발을 했는데 여기서 활용한 것이 post message 방식이었다.

설계는 우선 iframe 내에서 parent로 jwt를 달라는 요청을 하는 것이고 그 메세지를 받은 parent는 다시 iframe으로
jwt를 전달을 해주는 것이다.

우선은 iframe과 parent에 각각 message를 받을 수 있게 리스너를 추가했다.

## POST MESSAGE

```js
window.addEventListener('message', receiveMessage)
```

이렇게 작성하면 일단은 메세지를 `받을 수 있는` 상태까지는 가능하다.

받은 이후의 콜백 메서드의 역할을 하는 것이 receiveMessage 라고 보면 된다.

먼저 parent의 receiveMessage는 요청이 오면 jwt를 넘겨주는 역할을 하게 된다.

```js
function receiveMessage(event: any) {
  if (event.origin !== `iframe location origin`) {
    return
  }

  const message = event && event.data && event.data.request
  
  event.source.postMessage({
    jwt: window.localStorage.getItem('jwt'),
  }, `iframe location origin`)
}
```

event에서 origin을 체크하는 부분을 작성해주지 않으면 이 parent로 들어오는 모든 window post message에 응답을 주게 되어
보안상으로 문제가 생길 여지가 크다.

때문에 정확히 요청이 들어올 location을 지정해줄 필요가 있다.

event.source는 나에게 요청이 들어온 대상의 window를 의미한다.

정확히 메세지가 들어온 대상에게만 jwt를 전송해주는 역할이다.

이후에는 parent에서 보내주는 jwt를 iframe에서 받는 작업이 필요하다.

```js
function receiveMessage(event) {
  if (event.origin !== `parent location origin`) {
    return
  }

  const { jwt } = event.data

  if (jwt && level) {
    window.localStorage.setItem('jwt', jwt)
  }
}
```

마찬가지로 iframe으로 요청을 주는 parent에 대한 정확힌 location 지정이 필요하다.

그 이후에 받은 jwt를 iframe 자신의 localstorage에 저장하는 방식이다.

## JWT 사용에서의 문제점

이제부터 여러가지 문제가 발생했는데 프로젝트의 구성이 받은 jwt를 활용해서 graphql 요청 header로 활용하는 부분이었는데
추가적으로 iframe의 프로젝트는 ssr 렌더링으로 작업되어 있었다.

즉, 서버사이드 렌더링 과정에서는 당연히 window 객체가 존재하지 않기 때문에 별도의 분기 처리들이 필요하게 되었다.

window 객체가 존재하지 않는다는 것은 말 그대로 window.localStorage에 접근하지 못한다는 말이 된다.

`if (process.client)`을 활용하여 모든 window.localStorage.getItem('jwt')를 캐치하는 부분을 처리해줘서 기본적인 오류는 잡았다.

다만, apollo-link가 되는 과정에서 middleware에 jwt를 호출할 경우 문제가 발생했는데, 초기 렌더링 이후 jwt를 호출하는 부분이 undefined가 잡히는 부분이었고,
두번째 호출부터 정상적으로 호출이 되는 것이었다.

서버사이드 렌더링의 프리렌더링 과정에서 이미 호출된 window.localStorage.getItem('jwt') 부분을 undefined로 인식하고 있었고,
이걸 처음 호출하는 부분이 계속 갱신이 되지 않았던 문제였는데 결론적으로는 타이밍 문제이긴 했다.

내 생각으로는 nuxtjs에서 apollo-link 연결을 하는 시점에 process.client로 jwt를 가져오면 된다고 판단했는데,
이 시점에서는 undefined로 잡히는 부분이 해결되지 않았다.

그래서 해결한 것은 post message를 receive하는 타이밍을 정확히 해당 pages component가 호출되는 시점으로 바꿔줬다.

즉, 기존에는 pages component가 호출되기 전 window.localStorage에 접근해서 jwt를 가져오게 했었고 처음 한번은 undefined가 잡히고 graphql 호출을 하기 때문에
문제가 되던 부분을 (사실 이것도 graphql이 호출되는 시점에 localStorage를 재호출하기에 해결될 걸로 생각했지만 안됨...) post message가 receive 되고 나서 jwt를 localStorage에 넣어주고
그 다음 로직으로 graphql을 호출하게 했더니 해결되었다.

결론적으로 ssr 빌드 과정에서 server 영역에 포함되던 window 접근 방식을 모두 client 영역으로 바꿔준 이후에야 정상적으로 동작하게 됐고
그 타이밍도 딱 graphql를 호출하기 직전으로 바꿔주니 해결됐다.
