---
title: 'Vuejs 몇 가지 ESLint 오류사항 해결'
date: 2019-08-02 14:23:00 +0900
categories: Vuejs
tags:
  - vuejs
  - eslint
---

Vuejs에 TypeScript 기반의 ESLint 설정을 하면서 발생한 lint error에 대해 해결 방법을 정리해보았다.

## .js module exports 오류

TypeScript와 ESLint + Prettier를 결합하는 과정에서 .vue에 대한 Lint는 깔끔하게 적용되었다.
다만 몇몇 config.js를 활용해야하는 설정 파일들에 대해 lint error가 발생하여 .eslintrc.json에 옵션을 추가해야했다.

```javascript
module.exports = {}; // lint error
```

```json
// .eslintrc.json
{
  "env": {
    "browser": true,
    "es6": true,
    "node": true // 추가
  }
}
```

lint `env` 옵션에 `node: true`를 넣었더니 깔끔하게 해결 되었다.

## .spec.js 오류

테스트코드 작성을 위한 .spec.js 파일에서도 lint error가 발생하였다.
Mocha, Chai를 기반으로 쓰고 있었기 때문에 아래와 같이 config 옵션을 추가하였다.

```json
// .eslintrc.json
{
  "env": {
    "browser": true,
    "es6": true,
    "node": true,
    "mocha": true // 추가
  }
}
```

describe, it에 대한 lint error는 해결되었으나 expect 문법에 대한 lint error가 여전히 발생했다.
Jest 기반의 테스트 언어는 아니지만 Chai가 env 옵션에 존재하지 않아서 Jest를 추가했다.

```json
// .eslintrc.json
{
  "env": {
    "browser": true,
    "es6": true,
    "node": true,
    "mocha": true,
    "Jest": true // 추가
  }
}
```

위의 옵션을 config에 다 추가하니 테스트코드에 대한 lint error가 완전히 해결되었다.

## prettier 오류

정확한 원인은 알 수 없으나 ESLint + Prettier를 결합하고 `singleQuote` rules를 추가했더니 수시로 오류가 뜨기 시작했다.

```json
// .eslintrc.json
{
  "rules": {
    "singleQuote": true
  }
}
```

error 처리에 대해 ESLint와 Prettier가 충돌하는 느낌이었는데 아래의 rules로 변경하고 해결하였다.

```json
// .eslintrc.json
{
  "rules": { "prettier/prettier": ["error", { "singleQuote": true }] }
}
```
