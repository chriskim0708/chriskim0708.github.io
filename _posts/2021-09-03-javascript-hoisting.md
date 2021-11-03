---
title: "변수 호이스팅(Hoisting)이란?"
date: 2021-09-03 12:00:01 +0900
categories: 자바스크립트
---

자바스크립트가 코드를 실행하기 전 변수 선언문을 미리 실행해두어 참조가 가능한 상태가 되는 것을 변수 호이스팅이라고 한다.

## 간단한 예제

아래 예제를 보면 아무 변수 선언도 없을 경우 not defined 에러를 반환하는 것을 볼 수 있다.

```js
console.log(a); // Uncaught ReferenceError: a is not defined
```

그리고 코드 실행 이후에 변수를 초기화하여 값을 넣어본다.

```js
console.log(a); // undefined
var a = 1;
```

그냥 간단한 예제로만 봐도 분명히 같은 라인에서 실행된 코드지만 결과가 다르다는 것을 알 수 있다.

`console.log` 이후 선언된 변수 a가 어떠한 동작이 이뤄졌다는 것을 짐작할 수 있는 부분이다.

변수의 선언문들은 실행 코드를 만나면 코드가 실행되기 전에 선언문들만 미리 실행되어 참조가 가능한 상태가 된다.

선언되지 않은 변수의 경우에는 말 그대로 호이스팅 할 변수가 없기 때문에 `is not defined` 에러를 반환하게 되고,
변수 선언 전 참조되는 변수 선언문의 경우에는 undefined로 초기화되어 변수 호이스팅 된다.

물론 모든 변수가 undefined를 반환하는 것은 아니다.

var, let, const는 호이스팅이 다르게 동작된다.

## 함수 호이스팅

함수는 `function`으로 선언하는 선언식과 `const` 변수에 포함하는 표현식이 있습니다.

어떤 방식을 사용하느냐에 따라 호이스팅이 달라진다.

### 함수 선언식

`function`으로 선언된 함수는 현재 스코프 내에서 가장 위로 호이스팅 된다.

```js
hello(); // "Hello World!"
function hello() {
  console.log("Hello World!");
}
```

undefined로 초기화되어 호이스팅 되는 `var` 변수와도 동작이 다르다는 것을 알 수 있다.

현재 스코프 내 최상위로 호이스팅 되는 부분을 검증해보자.

```js
hello(); // "Hello World!"
innerHello(); // innerHello is not defined
function hello() {
  console.log("Hello World!");
  innerHello(); // "Hello World! - Inner"
  function innerHello() {
    console.log("Hello World! - Inner");
  }
}
```

함수 내부에 선언된 innerHello 함수가 전역 스코프로 호이스팅 됐다면 정상적으로 실행됐겠지만,
is not defined 에러를 반환한다.

하지만, hello 함수 내에서 함수 선언식보다 먼저 호출을 해도 정상적으로 console.log을 반환하게 된다.

현재 스코프 내에서 최상위로 호이스팅 됐다는 것을 알 수 있다.

### 함수 표현식

const에 선언된 표현식 함수의 경우에는 현재 스코프의 가장 위로 호이스팅 되지 않고,
const 변수의 일반적인 특징을 그대로 따라가게 된다.

```js
a(); // Uncaught ReferenceError: Cannot access 'a' before initialization
const a = function () {
  console.log("Hello World!");
};
```

let, const의 경우에는 변수 초기화 전에는 access 할 수 없는 상태로 호이스팅 되기 때문에
Reference 에러를 반환한다.
