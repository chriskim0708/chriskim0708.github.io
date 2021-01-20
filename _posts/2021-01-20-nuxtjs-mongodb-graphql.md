---
title: 'NuxtJS + MongoDB + GraphQL (1)'
date: 2021-01-20 09:57:00 +0900
categories: Nuxtjs
tags:
  - vuejs
  - nuxtjs
  - mongodb
  - mongoose
  - graphql
---

Nuxt를 활용하여 프로젝트를 개발하던 중 백엔드를 직접 구현해야 하는 상황이 되었다.
새로운 언어를 배우는 것보다는 기존 언어를 활용하여 빠르게 구현하는데 초점을 두었고,
아무래도 내가 프론트엔드 개발자다보니 클라이언트 서비스에서 DB를 제어할 수 있는 GraphQL에 관심이 생겼다.
최종적으로는 Nuxt 프로젝트 내 Express 서버에서 MongoDB와 GraphQL 방식으로 구현하기로 했다.

# **현재 내 스켈렉톤 프로젝트는 Nuxtjs + Vue3 Composition API + Typescript 방식**

대부분의 Vue 관련 GraphQL 예제들이 기존 Vue2 문법으로 적용되어 있다보니 어느 정도 참고는 되었으나,
실제 적용 과정에서는 상당한 시행 착오가 있었다.

Vue3에서는 [Composition API](https://v3.vuejs.org/guide/composition-api-introduction.html#setup-component-option)를 활용한 문법이 추가되었고 Nuxt에서는 해당 문법을 지원하기 위해
`@nuxtjs/composition-api` 모듈을 설치하면 된다.

```
npm i @nuxtjs/composition-api
```

즉, 지금 작성하는 포스트는 Nuxtjs에 Vue3 기반의 Composition API를 활용한 예제이므로 참고 바란다.

# **Express 서버 구현**

단순히 GraphQL 서버 구현에만 목적이 있지는 않고 CRUD는 gql로 제어하고,
다른 기능(파일 업로드나 타 서비스 연동 등)은 Router API 구현 방식으로 구현하는데 목적이 있다.

우선 Express 서버 구현부터 진행한다.

```
npm i express
```

`/server/api/index.ts`
```js
import path from 'path'
import express from 'express'

const app = express()

export default {
  path: '/api',
  handler: app,
}
```

Express 서버를 Nuxt 렌더링 포트와 함께 활용하기 위해 `/api` 경로를 활용하기로 했다.
GraphQL 별도로 서버를 올리지 않고 Express 서버와 같이 활용하기 위해 `/api/graphql`로 진행하는 계획.

미들웨어로 동작하기 위해서 아래와 같이 모듈 설정 파일을 만들고 `nuxt.config.js`에 등록한다.

`/modules/apis/index.ts`
```js
module.exports = function (moduleOptions) {
  if (this.options.dev || this.options._start) {
    this.addServerMiddleware('~/server/api/')
  }
}
```

`nuxt.config.js`
```js
modules: [
  '~/modules/apis',
],
```

여기까지 완료했다면 `/api` 경로를 통해 Express에서 작성한 API를 활용하기 위한 기본 작업을 했다고 보면 된다.

# **Mongoose 설정**
Mongoose는 MongoDB ODM 중 가장 인기있는 ODM이다.
Express와 MongDB의 연결은 Mongoose를 활용하여 진행하였다.

```
npm i mongoose
```

당연히 이전 스텝으로 자신의 로컬 PC에 MongoDB를 설치하는 절차를 선행해야한다.

mongoose와 MongoDB가 설치 완료되었다면 Express 서버를 통해 DB 커넥션을 해준다.

`/server/api/index.ts`
```js
import path from 'path'
import express from 'express'

const app = express()

mongoose.connect(`mongodb://localhost:27017/${process.env.MONGODB_DATABASE}`, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
  useFindAndModify: true,
  useCreateIndex: true,
}, (err) => {
  if (err) {
    console.log(err);
  } else {
    console.log(`Succeeded in connecting to database`);
  }
})

export default {
  path: '/api',
  handler: app,
}
```
# **MongoDB Schema Model 생성**
MongoDB 스키마를 생성해준다.
스키마에 대한 자세한 설명은 이 포스트에서는 다루지 않겠다.

`/server/models/users.ts`
```js
import mongoose from 'mongoose'
const { Schema } = mongoose

const UserSchema = new Schema({
  id: String,
  name: String,
  age: Number,
  email: String,
})

export default mongoose.model('User', UserSchema)
```

스키마 타입은 유의할 부분이 mongoose에서는 숫자형이 Number, 이후 다룰 gql에서는 숫자형이 Int라는 점이다.
스키마는 mongoose를 활용한 CRUD에서도 필요하지만 gql과 mongoose의 연결에서도 필요하다.

여기까지 진행했다면 express를 통해 MongoDB를 연결하고 데이터를 CRUD 할 수 있는 환경까지 만들어졌다.

이젠 GraphQL 설정만 진행하면 된다.