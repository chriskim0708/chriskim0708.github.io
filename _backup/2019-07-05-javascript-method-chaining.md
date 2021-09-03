---
title:  "자바스크립트 메서드 체이닝(Method Chaining)"
date:   2019-07-05 12:06:00 +0900
categories: Javascript
tags:
  - javascript
---
우선 메서드 체이닝 프로그래밍 패턴을 사용하기 위해서는 기본적으로 함수 내 this의 이해가 필요하다.
이 this에 대한 이해는 따로 포스팅하여 정리하고 우선은 메서드 체이닝 기법만 이해해보도록 하자.

# 메서드 체이닝
메서드가 객체를 반환하고 그 객체를 통해 또 다른 함수를 호출할 수 있다.
이걸 쇠사슬처럼 연결하듯 함수를 호출하는 방식을 메서드 체이닝이라고 한다.

```
function Sqaure() {
  this._width = 0;
  this._height = 0;
  this._x = 0;
  this._y = 0;
  this._color = '';
}
Sqaure.prototype.setWidth = function(w) {
  this._width = w;
}
Sqaure.prototype.setHeight = function(h) {
  this._height = h;
}
Sqaure.prototype.setX = function(x) {
  this._x = x;
}
Sqaure.prototype.setY = function(y) {
  this._y = y;
}
Sqaure.prototype.setColor = function(c) {
  this._color = c;
}
Sqaure.prototype.getInfo = function() {
  console.log(this._width);
  console.log(this._height);
  console.log(this._x);
  console.log(this._y);
  console.log(this._color);
}

const mySquare = new Sqaure();

mySquare.setWidth(100);
mySquare.setHeight(100);
mySquare.setX(100);
mySquare.setY(100);
mySquare.setColor('Blue');
mySquare.getInfo();
```

보통은 위와 같이 생성자 함수를 생성하고 prototype으로 set, get 함수를 만들 것이다.
그리고 병렬적으로 각각의 set 함수를 호출하여 this 객체에 저장할 것이고 get 함수를 통해 저장된 정보를 호출할 것이다.

물론 이런 병렬적 호출 방식이 잘못됐다는 것은 아니지만 코드 가독성과 길이에서 불필요한 부분을 차지하게 된다.

동일한 생성자 함수 내에서의 prototype을 호출하고 싶다면 메서드 체이닝 기법을 사용하여 아래와 같이 코드를 줄일 수 있게 된다.

```
function Sqaure() {
  this._width = 0;
  this._height = 0;
  this._x = 0;
  this._y = 0;
  this._color = '';
}
Sqaure.prototype.setWidth = function(w) {
  this._width = w;
  return this;
}
Sqaure.prototype.setHeight = function(h) {
  this._height = h;
  return this;
}
Sqaure.prototype.setX = function(x) {
  this._x = x;
  return this;
}
Sqaure.prototype.setY = function(y) {
  this._y = y;
  return this;
}
Sqaure.prototype.setColor = function(c) {
  this._color = c;
  return this;
}
Sqaure.prototype.getInfo = function() {
  console.log(this._width);
  console.log(this._height);
  console.log(this._x);
  console.log(this._y);
  console.log(this._color);
}

const mySquare = new Sqaure();

mySquare.setWidth(100).setHeight(100).setX(100).setY(100).setColor('Blue').getInfo();
```

결과값은 병렬적 호출을 한 위와 동일하지만 코드는 한 줄로 짧게 처리되었다.
여기서 각 prototype 함수에서 반환하는 this는 new Sqaure()로 선언한 자기 자신을 의미한다.
당연히 여기에는 각 this 객체와 prototype 함수들이 같이 반환될 것이고 메서드 체이닝으로 prototype 함수를 호출할 수 있게 되는 것이다.

이런 식으로 생성자 함수 자신을 반환하면서 자신 내의 여러 함수를 릴레이 방식으로 호출하는 프로그래밍 패턴을 메서드 체이닝이라고 부른다.

다음 포스팅에는 함수형 프로그래밍에서 메서드 체이닝 호출 방법에 대해 포스팅하고자 한다.