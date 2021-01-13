---
title:  "자바스크립트 함수형 프로그래밍"
date:   2019-06-26 13:37:30 +0900
categories: Javascript
tags:
  - javascript
  - programming
---
명령형 프로그래밍과 함수형 프로그래밍의 차이는 무엇인가? 라는 얘기를 요즘 자주 듣게 된다.
함수형 프로그래밍은 선언형 프로그래밍과 같은 의미로 사용되고 있다.

## **함수형 프로그래밍이란?**
함수형 프로그래밍의 기본 조건으로는 아래의 세가지 조건을 꼽고 있다.
- 순수 함수여야 한다. (동일 입력 값에는 동일 출력 값 원칙)
- How(어떻게)보단 What(무엇)에 중점을 둬야한다.
- 부작용이 없어야 한다. (원본 데이터의 불변성)

두 번째 조건은 약간 추상적이긴 하다.
그럼 실제로 코드를 봐야 무슨 말인지 알 거 같다.

우선 순수 함수라는 개념 자체를 코드로 표현을 해보자.
말 그대로 동일한 인자(`Arguments`)가 100번 들어와도 100번 같은 출력을 해야한다.

아래를 예로 들어보자.

{% highlight ts %}
const arr = [1, 2, 3, 4, 5];
const multiply = (number) => {
  return number * 2;
};
const test = (array) => {
  return array.map(multiply);
};
console.log(test(arr)); // [2, 4, 6, 8, 10]
{% endhighlight %}

test 함수의 코드만 보면 고정적인 값을 반환할거라 예상된다.
정말로 그렇다면 test는 순수 함수로서 함수형 프로그래밍과 같은 의미가 된다.
하지만 test 함수에서 참조하는 있는 1급 객체(1급 객체 설명은 다음에)인 multiply 함수의 내부 코드가 변경된다면 test 함수의 반환값은 변경된다.

{% highlight ts %}
const arr = [1, 2, 3, 4, 5];
const multiply = (number) => {
  return number * 4;
};
const test = (array) => {
  return array.map(multiply);
};
console.log(test(arr)); // [4, 8, 12, 16, 20]
{% endhighlight %}

multiply 함수의 내부 코드를 변경했더니 test 함수의 반환 값이 [2, 4, 6, 8, 10] -> [4, 8, 12, 16, 20]로 변경 됐다.
이것이 무엇을 의미하냐면 동일한 입력 값에 동일한 출력 값을 반환해야된다는 원칙에 위배된다는 것이다.
여기까지의 기준으로는 현재 이 코드는 명령형 프로그래밍이 되는 상황이다.

이것을 이해했다면 이제 이것을 함수형 프로그래밍으로 어떻게 변경해야될 것인지 대충 감이 올 것이다.
크게 2가지 방식이 있다.

입력 인자를 추가하거나 내부 코드가 변경을 해보자.

먼저 입력 인자를 변경하는 1번 방법이다.

{% highlight ts %}
const arr = [1, 2, 3, 4, 5];
const multiply = (number) => {
  return number * 2;
};
const test = (array, fn) => {
  return array.map(fn);
};
console.log(test(arr, multiply)); // [2, 4, 6, 8, 10]
{% endhighlight %}

이것도 따지고 보면 multiply의 내부 코드를 변경하면 출력 값이 바뀌는 거 아니냐? 라고 의문을 표할 수 있지만, 아니다.
왜냐하면 변경된 multiply 자체가 이미 입력 값이 변경된 상황이고 변경한 입력 값을 인자로 넣어서 반환하기 때문에 동일 입력/동일 출력 원칙에 부합한다.

그럼 좀 더 보기 변한 2번 방법을 사용해보자.

{% highlight ts %}
const arr = [1, 2, 3, 4, 5];
const test = (array) => {
  return array.map(v => v * 2);
};
console.log(test(arr)); // [2, 4, 6, 8, 10]
{% endhighlight %}

이것은 왜 함수형 프로그래밍이 되는지 명확하게 알 수 있다.

물론 이 코드보다는 코드의 재사용성을 위해 1번 방법의 코드를 더욱 권장한다.

우리는 이것으로 함수형 프로그래밍의 가장 기본적인 구조를 알았다고 할 수 있겠다.