---
title:  "트랜스파일러(Transpiler) Babel의 기능"
date:   2019-06-28 14:30:00 +0900
categories: Vuejs
tags:
  - babel
  - vuejs
---
Vue-CLI를 통해 프로젝트를 create하면 트랜스파일러 선택 중에 Babel을 볼 수 있다.
그냥 단순히 Babel이 트랜스파일러라는 개념 외의 정확히 무얼 의미하는지 알아보도록 하자.

ES6+ 자바스크립트 코드를 작성하면 당연하게도 아무런 개발 환경 구축 없이 모든 브라우저에서 동작하길 기대하는 건 무리일 것이다.

보통은 모든 브라우저에서의 동작 환경을 위해 트랜스파일러(`Transpiler`)와 모듈 번들러(`Module bundler`)를 설치하여 개발 환경을 구축한다.
이중에서 트랜스파일러인 Babel의 간단한 개념을 정리하고자 한다.

## **Babel**
# **Babel이란?**
Babel은 ES6+ 코드를 ES5 이하의 버전으로 트랜스파일링한다.

{% highlight ts %}
[1, 2, 3].map(n => n ** n);
{% endhighlight %}

위 코드는 ES6에서 화살표 함수(`Arrow Function`)과 ES7에서 거듭제곱 연산자(`Exponentiation Operator`)를 사용하고 있다.
당연히 위의 코드를 그냥 실행할 경우 구형 브라우저에서는 에러를 호출하게 될 것이다.
그렇다면 최신의 코드를 작성하면서도 구형 브라우저에서도 실행할 수 있게 하기 위해 Babel을 거쳐 트랜스파일링하게 되면 코드가 아래와 같이 ES5 스펙에 맞춰 변경된다.

{% highlight ts %}
'use strict';

[1, 2, 3].map(function (n) {
  return Math.pow(n, n);
});
{% endhighlight %}

Vue 프로젝트 생성 시에 선택하는 Babel이 대략 어떤 구조로 동작하는지 확인해보았다.