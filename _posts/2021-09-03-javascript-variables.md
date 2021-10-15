---
title: "자바스크립트 변수(Variable)"
date: 2021-09-01 11:00:02 +0900
categories: Javascript
---

# var, let, const

자바스크립트에서 변수를 선언하는 키워드는 var, let, const가 있습니다.

let, const는 es6 이후에 도입된 변수 선언 방식입니다.

변수 선언 방식에 따라 유의미한 차이점을 가지고 있고 크게 분류하면 아래와 같습니다.

1. 중복선언
2. 재할당
3. 호이스팅
4. 스코프 레벨
5. 전역객체(Window) 프로퍼티

## 1. 중복선언

### var: 중복선언 가능

```js
var a = 10;
console.log(a); // 10
var a = 20;
console.log(a); // 20
```

var는 중복 선언을 하더라도 오류를 반환하지 않고 마지막에 선언된 값이 변수에 저장됩니다.

어떻게 보면 재할당과 크게 다르진 않은 동작을 보이지만 이런 중복선언은 코드가 길어질수록 디버깅을 어렵게 만드는 요소가 됩니다.

### let, const: 중복선언 불가능

```js
let a = 10;
let a = 20; // SyntaxError: Identifier 'a' has already been declared

const b = 10;
const b = 20; // SyntaxError: Identifier 'b' has already been declared
```

let, const로 선언한 변수는 같은 변수명으로 중복선언이 불가능합니다.

에러를 반환하기 때문에 실수할 여지가 없어지게 됩니다.

## 2. 재할당

### var, let: 재할당 가능

```js
var a = 10;
a = 20;
console.log(a); // 20

let b = 10;
b = 20;
console.log(b); // 20
```

var, let은 초기화 이후 재할당이 가능합니다.

### const: 재할당 불가

```js
const c = 20;
c = 30; // TypeError: Assignment to constant variable.
```

const는 상수를 선언할 때 씁니다.

선언 및 초기화 이후에는 다른 값을 재할당 할 수 없습니다.

추가적으로 const는 반드시 선언 시점에 값을 초기화 해줘야 합니다.

```js
const c; // SyntaxError: Missing initializer in const declaration
```

## 3. 호이스팅

호이스팅은 간단하게 코드 실행 전 변수 선언문들이 먼저 실행되어 참조할 수 있게 되는 상태를 말합니다.

일반적으로 변수선언 없이 해당 변수를 참조하려고 하면 에러가 발생하겠죠?

```js
console.log(a); // ReferenceError: a is not defined
```

변수 참조가 먼저 이뤄지고 변수 선언이 나중에 이뤄지는 상황들을 살펴보겠습니다.

### var 호이스팅

var 변수는 먼저 undefined로 초기화되어 호이스팅 됩니다.

때문에 변수선언이 코드 실행 이후에 이뤄지더라도 undefined로 참조가 가능합니다.

```js
console.log(a); // undefined
var a = 1;
```

### let, const 호이스팅

let, const는 변수 초기화 전에는 참조할 수 없는 상태로 호이스팅 됩니다.

변수가 선언이 되지 않아 `is not defined` 에러를 반환하는 것과 변수 초기화 전 참조할 수 없는 `cannot acccess before initialization`은 다르다는 것을 알아두시길 바랍니다.

즉, 참조할 수 없는 상태를 참조할 수 있는(?) 형태로 호이스팅 됩니다.

## 4. 스코프 레벨

스코프란 변수에 접근할 수 있는 범위를 뜻합니다.

크게는 함수 레벨 스코프, 블록 레벨 스코프로 나눠집니다.

### var: 함수 레벨 스코프

var는 함수 내부에 선언된 변수만 지역변수이며, 블록 레벨 스코프에서는 전역변수입니다.

```js
function hello() {
  var a = "Hello World!";
  console.log(a);
}
hello(); // "Hello World!"
console.log(a); // Uncaught ReferenceError: a is not defined
```

외부 스코프에서 함수 내의 지역변수인 a를 참조할 수 없기 때문에 is not defined 에러를 반환합니다.

블록 레벨 스코프에서 같은 코드를 작성해보겠습니다.

```js
if (true) {
  var a = "Hello World!";
  console.log(a); // "Hello World!"
}
console.log(a); // "Hello World!"
```

외부 스코프에서 블록 스코프의 지역변수인 a를 참조할 수 있습니다.

### let, const: 블록 레벨 스코프

블록 레벨 스코프는 es6 이후부터 도입된 개념입니다.

let, const는 함수 레벨 스코프나 블록 레벨 스코프 어디서든 지역변수로 선언됩니다.

```js
// function level scope
function a() {
  let b = 1;
}
console.log(b); // Uncaught ReferenceError: b is not defined

// block level scope
if (true) {
  const c = 1;
}
console.log(c); // Uncaught ReferenceError: c is not defined
```

## 5. 전역객체(Window) 프로퍼티

### var or Function Declarations(함수 선언식)

var 혹은 함수 선언식의 경우에는 변수 및 함수가 window 전역 프로퍼티로 선언됩니다.

```js
var a = 1;

function b() {
  return 1;
}

console.log(this); // this --> Window Global Object
```

위의 코드를 확인해보면 아래와 같이 window 전역 객체 프로퍼티로 적용된 변수 a와 함수 b를 확인할 수 있습니다.

![](/assets/images/frontend-performance-01.png)

### const, let 및 const 함수 표현식

const, let 변수에 선언하는 경우와 const 변수에 쓰는 함수 표현식의 경우에는 동작 방식이 var와 다릅니다.

```js
const a = 1;
let b = 1;

const c = function () {
  return 1;
};

console.log(this);
```

위의 코드를 확인해보면 window 전역 객체에 해당 프로퍼티가 생성되지 않음을 볼 수 있습니다.

![](/assets/images/frontend-performance-02.png)

정리하자면,

`var, var의 함수 표현식, 함수 선언식의 경우에는 window 전역 객체의 프로퍼티로 확장된다.`

`const, let, const(let도 마찬가지)의 함수 표현식은 window 전역 객체의 프로퍼티로 확장되지 않는다.`

이렇게 정리할 수 있겠습니다.
