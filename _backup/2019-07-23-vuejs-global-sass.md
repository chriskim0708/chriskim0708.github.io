---
title:  "Nuxtjs에서 Sass Global Variables 미적용 현상 해결"
date:   2019-07-23 14:56:00 +0900
categories: Vuejs
tags:
  - vuejs
  - sass
---
Sass를 활용하는 경우 $를 활용한 Global Variables를 자주 세팅하고 쓰는 편이다.
Nuxtjs에서 sass 세팅을 하면서 하위 component 단위에 global variables가 제대로 적용되지 않아 애를 먹었는데 해결법을 적어보고자 한다.


## **style-resources 다운로드**
# **설치방법**
```
npm i @nuxtjs/style-resources
```

npm i 옵션은 npm install --save와 같은 의미로 package.json에서 devDependencies가 아닌 dependencies에 저장된다.

# **Global Sass 설정**
```
// _fonts.scss
@import url(https://fonts.googleapis.com/earlyaccess/notosanskr.css)

// _variables.scss
$color-red: #f04137 !default;
$color-black: #000 !default;
$color-gray: #777 !default;
$color-light-gray: #999 !default;
```

```
// index.scss
@import './variables.scss';
@import './fonts.scss';
```

굳이 index.scss에 @import를 활용하여 묶어준 이유는 font처럼 @import url을 활용하는 경우 config 세팅을 정상적으로 했더라도 빌드 에러가 발생해서이다.
찾아본 내용으로 @import url 해석이 정상적으로 이뤄지지 않아서라고 한다.

그래서 index.scss에 묶어주고 그걸 config에 세팅해주는 방식으로 하기로 했다.

# **Config설정**
```
// nuxt.config.ts (필자는 ts로 활용 중)
modules: [
  '@nuxtjs/style-resources'
],
styleResources: {
  sass: [
    './assets/sass/index.scss'
  ]
}
```

# **default.vue에 import**
```
// default.vue

<style lang="scss">
@import 'assets/sass/index';
</style>
```

config.ts만 설정해도 적용되지 않을까 했는데 default.vue에 @import까지 끝내야 정상 적용된다.

처음의 시작은 마지막 과정만 적용했을 경우 import가 자식 컴포넌트에 global variables가 정상 적용되지 않아 이 과정까지 오게 되었다.