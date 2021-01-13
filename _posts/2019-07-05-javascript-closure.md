---
title:  "자바스크립트 클로저와 호이스팅, 스코프"
date:   2019-07-05 12:06:00 +0900
categories: Javascript
tags:
  - javascript
  - closure
  - hosting
  - scope
---
자바스크립트를 공부하기 위해 반드시 이해해야 될 것이 바로 클로저이다.
클로저는 빈번하게 사용되고 있지만 이론적인 지식을 바탕으로 설명하기는 어려운 것 또한 클로저이다.
이 글에서는 클로저를 이해하기 위해 호이스팅과 스코프에 대한 간단한 설명도 같이 포함하여 글을 쓰고자 한다.

# 클로저란?
클로저에 대한 정의는 생각보다 매우 단순하다.
외부 함수의 변수에 접근할 수 있는 내부 함수를 우리는 클로저라고 부른다.
말 그대로 함수 내부에 작성된 함수이다.

원문 글에 대한 번역을 기반으로 작성된 포스팅입니다.

1. [Javascript-Scope-Closures](https://css-tricks.com/javascript-scope-closures/)
2. [Understand Javascript Closures With Ease](http://javascriptissexy.com/understand-javascript-closures-with-ease/)