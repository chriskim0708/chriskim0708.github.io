---
title: 'ESlint와 Prettier 설정'
date: 2019-07-30 14:02:00 +0900
categories: Configuration
tags:
  - eslint
  - prettier
  - configuration
  - vscode
---

Linter란 정적 타입 분석 도구를 말한다.
Linter 중에서도 ESLint는 현재 Javascript에서 가장 많이 사용되는 Linter이다.

`2019년을 기준으로 TypeScript 도구인 TSLint가 ESLint로 흡수 되었다.`

코딩 컨벤션과 에러 분석을 위해서도 Linter는 거의 필수적으로 사용되고 있으며,
ESLint는 기본적인 formatting도 제공하지만 formatting에 특화된 Prettier와 결합하여 더욱 효율적으로 활용이 가능하다.

JavaScript에서 인기도가 높은 VSCode를 기준으로 ESLint와 Prettier를 결합하여 사용하는 방법을 정리해보았다.

## VSCode

우선 VSCode에 확장 기능에서 `ESLint`와 `Prettier - Code formatter`를 검색하여 설치한다.

## ESLint

`CLI` 활용을 위해 전역으로 ESLint를 설치한다.

```
npm install eslint -g
```

`eslint --init` 명령어로 `.eslintrc.json` 기본 옵션 파일을 생성한다.
`eslint --init` 명령어로 설치 시에는 몇 가지 선택 옵션이 나오는데 확장자 `json`, framework 사용 유무에 따라 `vue, react, None of these`를 적절히 선택해주면 된다.

## prettier + eslint-plugin-prettier + eslint-config-prettier

`plugin`이나 `config` 중 하나만을 사용할 수도 있다.
plugin 사용만으로는 eslint formatting rules와 prettier rules가 충돌하므로, eslint-config-prettier를 함께 사용한다.

```
npm i -D prettier eslint-plugin-prettier eslint-config-prettier
```

```
//.eslintrc.json
// extends에 추가
{
  "extends": ["plugin:prettier/recommended"]
}
```

다른 eslint rule을 함께 사용 중이라면 같이 추가해주면 된다.

```
{
  "extends": [
    "plugin:prettier/recommended",
    "eslint:recommended",
    "plugin:vue/essential"
  ]
}
```

필요에 따라 quote 규칙 같이 논란이 있는 부분은 내부적으로 협의하여 사용하도록 한다.

```
//.eslintrc.json
"rules": {
  "singleQuote": true
}
```

Prettier의 기본 목적은 코딩 컨벤션에 대한 논란을 없애고자 하는데 있기 때문에 기본 옵션 외에 굳이 다른 rules를 추가하여 사용할 필요는 없다.
