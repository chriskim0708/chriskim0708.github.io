---
title: "스코프(Scope)란?"
date: 2021-09-03 12:00:02 +0900
categories: Javascript
---

## Scope

자바스크립트에서 스코프란 변수에 접근할 수 있는 범위를 말합니다.
일반적으로는 전역 스코프와 지역 스코프로 구분 됩니다.

## 전역 스코프

만일 변수가 모든 함수에 속하지 않고 블록(`{}`)에도 들어있지 않다면 그 변수를 전역 변수라고 합니다.
전역 변수는 자바스크립트 내의 어떠한 스코프에서도 사용할 수 있습니다.

```js
const globalVariable = "some value"; // 전역 변수

function hello() {
  console.log(globalVariable);
}

console.log(globalVariable);
```

변수 선언 방식이 `var` 밖에 없던 es5 이전에선 전역 변수의 사용은 최대한 자제하는 것이 상식이었습니다.

이 부분은 var의 선언적 특성 중 재선언이 가능하다는 특징 때문이었는데 이 부분은 var, let, const의 차이점에 대한 포스트에서 다루도록 하겠습니다.

es6+ 이후에는 전역 변수를 그렇다고 자주 써야되냐고 한다면 그건 아니겠지만 재선언적 특징에 의한 위험도는 사라졌다고 봐도 됩니다.

## 지역 스코프

코드 내 특정 구역에서만 사용할 수 있는 변수를 지역변수라고 합니다.
지역 변수는 2가지 레벨로 구분됩니다.

1. 함수 스코프 레벨
2. 블록 스코프 레벨

### 함수 스코프(Function Scope)

함수 내에서 변수를 선언하게 되면 그 함수의 범위 안에서만 이 변수에 접근할 수 있습니다.
함수 외부에서는 함수 내부에 있는 변수에 접근할 수 없습니다.
아래 예제를 보겠습니다.

```js
function helloWorld() {
  const hello = "Hello World!";
  console.log(hello);
}
helloWorld(); // "Hello World!"
console.log(hello); // Uncaught ReferenceError: hello is not defined
```

위와 같이 함수 내에 변수를 선언하고 함수를 실행하면 정상적으로 지역변수를 참조하여 console.log를 실행하는 것을 볼 수 있습니다.
하지만 전역 스코프에서 함수 스코프 내의 지역변수를 접근하려고 하면 is not defined 에러를 반환합니다.

### 블록 스코프(Block Scope)

블록 스코프의 특징은 함수(Function)이 아닌 if, for 등의 블록(`{}`)으로 이루어진 영역을 의마합니다.
여기에 선언된 지역변수는 블록 스코프 레벨 변수라고 합니다.

함수 스코프와 블록 스코프를 굳이 구분해서 설명하는 이유는 var, let, const가 함수 스코프와 블록 스코프에서 동작 방식이 다르기 때문입니다.

이 부분은 역시나 var, let, const의 차이점에 대한 포스트에서 다루도록 하겠습니다.

일단은 아래 간단한 예제만 확인해보겠습니다.

```js
if (true) {
  const hello = "Hello World!";
  console.log(hello); // "Hello World!"
}
console.log(hello); // Uncaught ReferenceError: hello is not defined
```

## 함수 호이스팅과 스코프
