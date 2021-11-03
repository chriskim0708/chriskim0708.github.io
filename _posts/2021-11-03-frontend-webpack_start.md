---
title: "Webpack 설치해보기"
date: 2021-11-03 10:00:00 +0900
categories: 프론트엔드
---

웹팩은 간단하게 설명하면 하나 이상의 진입점을 기준으로 종속성을 가지는 모듈들을 하나 또는 하나 이상의 번들 파일로 묶어주는 도구이다.
간단한 웹팩 설정을 구현해보면서 기초적인 지식을 습득하고자 한다.

## install

설치는 간단하다.
nodejs가 설치되어 있다면 npm 명령어로 생성하면 된다.

우선 package.json 파일을 `npm init` 명령어로 생성하자.

```
npm init
```

그 다음 devDependencies에 해당하는 webpack은 -D 명령어까지 추가해서 설치해주면 된다.

```
npm install -D webpack
```

## mode

현재 웹팩의 구동 모드를 지정할 수 있는데 보통은 .env 로 생성하는 환경 설정 파일 옵션과 관련된 부분이다.

공식 문서상으로 빌드 시의 동작 방식도 약간 상이한 듯 하지만 일단은 `development`, `production` 2가지의 모드가 있다라고 알아두고
배포 전의 과정에서는 `development` 모드로 구현하도록 한다.

## entry

웹팩은 기본적으로 모듈의 진입점이 필요하다.

우리가 보통 여러 개의 js 파일을 만든다고 하면 보통 진입점이 되는 index.js 파일에 es6 import 방식 혹은 CommonJS 등의 방식으로
모듈 종속성 트리를 만들 것이다.

이 경우엔 어쨌든 별도의 파일들로 분리되어 있기 때문에 실제로 화면의 렌더링이 시작된다면 각각의 js 파일들은 전부 다운로드 과정을 거치게 된다.

이런 다운로드 과정 동안 html 렌더링 과정이 지연되기 때문에 번들러를 통해 하나의 js 파일로 통합하여 다운로드하는 과정이 필요한 것이고,
진입점은 index.js 파일이 될 것이다.

nodejs는 CommonJS 방식의 모듈화를 지원하고 있고, webpack의 환경설정 파일도 기본적으로 이 모듈 방식으로 작성하면 된다.

프로젝트 루트 디렉토리에 아래와 같이 `webpack.config.js` 파일을 생성해서 진입점을 작성하자.

```js
// webpack.config.js
module.exports = {
  mode: "development",
  entry: "./src/index.js"
};
```

프로젝트의 모든 js 모듈 파일들은 이제 index.js 파일에 es6 혹은 CommonJS 방식을 활용하여 종속성 트리를 만들어주면
webpack이 알아서 이 파일들을 하나로 모아 번들 파일로 만들어준다.

## output

진입점 외에 번들링 된 파일들을 어디로 내보낼 것인지를 지정하는 옵션인 output이 있다.
말 그대로 번들이 완료된 별도의 파일을 어디 폴더의 어떤 파일명으로 내보낼 것인지에 대한 설정을 하는 부분이다.

별도의 폴더 지정을 하지 않을 경우 보통 `dist`라는 폴더를 자동으로 생성해서 그 안에 번들 파일을 생성한다.

```js
// webpack.config.js
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "bundle.js"
  }
};
```

여기까지만 작성해도 바로 번들 파일을 생성할 수 있는데 커맨드 라인을 통해 webpack 빌드를 수행하기 위해서는
webpack-cli 패키지 설치가 필요하다.

```
npm install -D webpack-cli
```

프로젝트 내에서 webpack 명령어를 수행하기 위해서는 package.json에 script 부분 추가를 해주면 된다.

```json
// package.json
{
  "scripts": {
    "build": "webpack"
  }
}
```

작성한 명령어를 통해 빌드를 수행하면 `dist` 폴더에 `bundle.js` 파일이 생성되는 걸 확인할 수 있다.

```
npm run build
```

## loader

로더의 사용 이유는 간단하다.
기본적으로 webpack은 js, json 파일만 해석하고 번들링할 수 있다.

추가적으로 html, css 혹은 jsx 같은 별도의 파일을 해석하기 위해서는 로더를 사용해서 해석하는 전처리 과정이 필요하다.

전처리 과정이라고 표현한 이유는 말 그대로 로더는 `읽고 해석하는` 기능만 수행하기 때문이고 이 해석된 정보를
다시 원하는 양식의 파일로 내보내기 위해서는 플러그인이 설정까지 추가로 필요하다.

우선 로더를 설정하는 부분부터 살펴보자.

module.rules 부분에 loader 설정들을 추가하면 되는데 우선 간단하게 html-loader만 사용해보자.
html-loader도 기본적으로는 패키지이기 때문에 npm install을 통한 설치가 먼저 필요하다.

```
npm install -D html-loader
```

그리고 아래와 같이 작성해주면 된다.

```js
// webpack.config.js
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: {
              minimize: true
            }
          }
        ]
      }
    ]
  }
};
```

플러그인과 달리 로더가 설치되고 사용 선언을 하면 알아서 읽어들여 해석하므로 모듈을 import 하는 과정이 필요하진 않다.

이 상태에서 빌드 과정을 수행해봐도 아직 플러그인을 통한 파일 방출이 이뤄지지 않았기에 무언가 수행되는 것도, 에러가 나는 부분도 없다.

## plugin

플러그인은 보통 로더를 통해 해석된 별도의 파일들을 원하는 형태의 번들 파일로 내보내는 기능을 제공한다.

방금 html-loader를 통해 해석된 정보를 번들 파일로 내보내보자.

우선 플러그인도 마찬가지로 패키지이기 때문에 설치가 먼저 필요하다.

```
npm install -D html-webpack-plugin
```

로더와는 달리 플러그인이 동작하기 위해서는 모듈을 불러와서 사용을 해줘야한다. (물론 로더도 플러인에서 제공하는 로더일 경우는 다르지만)

```js
// webpack.config.js
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: {
              minimize: true
            }
          }
        ]
      }
    ]
  },
  plugins: [new HtmlWebpackPlugin()]
};
```

plugins 부분에 추가해주면 되는데 알아서 기본이 되는 `index.html` 파일을 생성해서 `dist` 폴더에 생성해준다.
별도의 root 엔트리를 가지는 SPA 웹앱인 경우 template 설정이 추가적으로 필요하다.

# webpack-dev-server

개발하는 과정에서 코드 추가사항이 반영될 때마다 새로 번들링하고 새로고침해서 확인하고 하는 과정이 반복된다면 번거로운 일이 될 것이다.

이런 부분을 webpack-dev-server를 활용하여 로컬에 서버를 올리고 코드가 변경이 될 때마다 자동으로 새로운 코드가 반영되어 새로고침 된다면
번거로운 상황을 많이 줄일 수 있고 이런 부분을 webpack-dev-server가 담당해준다.

그리고 http 요청 같은 경우 server to server 간의 요청을 기반으로 하기 때문에 일반적인 html 파일을 열어 요청하는 게 불가능하다.

웹 서버에서 어플리케이션 구동을 시켜야하는데 이 과정에서도 그때그때 배포하거나 서버를 띄우거나 하는 작업을 번거로운 작업이 될 수 있다.

webpack-dev-server는 자신의 로컬PC를 기반으로 웹 서버를 구동할 수 있고 로컬PC에서 http 통신에 대한 개발을 바로 진행할 수 있게 도와준다.

아래의 패키지 설치 명령어를 이용하자.

```
npm install -D webpack-dev-server
```

설치 이후에는 webpack-dev-server와 관련된 설정을 webpack.config.js에 추가해주면 된다.

```js
// webpack.config.js
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.html$/,
        use: [
          {
            loader: "html-loader",
            options: {
              minimize: true
            }
          }
        ]
      }
    ]
  },
  plugins: [new HtmlWebpackPlugin()],
  devServer: {
    open: true
  }
};
```

`open: true` 옵션만 넣어준 이유는 `host`는 `localhost`, `port`는 `8080`이 기본인데
변경하고 싶을 경우에만 넣어주면 되기 때문이다.

변경을 실시간으로 감지하는 `watch` 옵션도 default는 `true` 상태이다.

서버를 구동시키기 위해 package.json에 script를 추가하여 사용해주자.

```json
// package.json
{
  "scripts": {
    "start": "webpack serve",
    "build": "webpack"
  }
}
```

`npm start` 명령어를 통해 서버를 구동하면 별도의 작업없이 바로 웹 페이지가 열리면서 번들된 dist에 있는 파일들을 기준으로
웹 서버가 구동된다.

여기까지 했다면 웹팩의 아주 기본적인 기능들은 사용해봤다고 할 수 있다.
