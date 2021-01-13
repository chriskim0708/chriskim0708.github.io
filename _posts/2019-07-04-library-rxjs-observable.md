---
title:  "RXJS Cold Observable, Hot Observable"
date:   2019-07-04 12:06:00 +0900
categories: Library
tags:
  - library
  - rxjs
---
Rxjs 관찰자(`Observable`)는 데이터 생성자의 특성에 따라 Hot Observable, Cold Observable로 구분된다.
이 두 가지 구분의 차이점이 무엇이고 왜 중요한지를 알아보자.

## **데이터 생성자에 대한 이해(Data Producer)**
관찰 대상(`Observable`)에 대한 설명 중에 늘상 같이 따라오는 것이 생성자(`Producer`)에 대한 얘기이다.
생성자는 말 그대로 Rxjs observer.next(value)로 전달 할 수 있는 모든 데이터를 의미한다.
그것은 DOM 이벤트가 될 수도 있고 배열일 수도 있고 단순한 문자열일 수 있다.

## **Cold Observable**
Cold Observable에서 생성자(`Producer`)는 Observable 내에서 이뤄지고 반환되기 때문에 구독(`subscribe`)하게 되면 그때부터 데이터를 읽어올 수 있는 구조가 된다.
`of`, `from`, `range`, `interval`, `timer`와 같은 연산자를 사용하는 경우에도 기본적으로는 Cold Observable이다.

Observable을 선언하는 아래 2가지 방식에서 간단한 예를 들어보도록 하자.

{% highlight ts %}
// 1번 방식
const observer1$ = new Observable((observer) => {
  let i = 0;
  setInterval(() => {
    observer.next(i++);
  }, 1000);
});

// 2번 방식
const observer2$ = interval(1000);
{% endhighlight %}

위의 코드는 연산자를 사용했느냐 안했느냐의 차이일 뿐이고 실제 전달되는 데이터는 같다.
다만, 구독(`subscribe`)이 되기 전까지는 데이터를 전달하지 않는 단순한 객체 선언일 뿐이다.
구독을 선언된 순간부터 실제 데이터를 전달하기 시작한다.

구독을 선언하는 방법은 아래와 같다.

{% highlight ts %}
const subscriber1 = observer1$.subscribe(v => console.log(v)); // 0, 1, 2, 3 ~~~~
setTimeout(() => {
  const subscriber2 = observer1$.subscribe(v => console.log(v)); // 10초 뒤에 0, 1, 2, 3 ~~~~
  const subscriber3 = observer2$.subscribe(v => console.log(v)); // 10초 뒤에 0, 1, 2, 3 ~~~~
}, 10000)
{% endhighlight %}

이때부터는 별다른 작업 없이 관찰 대상으로부터 데이터를 읽어들이는 게 가능해진다.
기본적으로는 멀티캐스트 방식으로 각 구독자에게 독립적으로 데이터 스트림을 내보낸다.

Observable에서 데이터가 생성되고 구독을 선언한 시점부터 데이터 스트림이 방출되는 구조를 우리는 Cold Observable이라고 부른다.

## **Hot Observable**
위의 설명으로는 Cold Observable이 이해될 수는 있으나 그 반대 개념으로 Hot Observable을 이해하기는 어렵다.
마찬가지로 샘플 코드로서 Hot Observable을 확인해보자.

간단한 설명으로는 우선 Hot Observable이란 Producer가 Observale 밖에서 생성되고 이것을 Observale이 관찰하는 구조를 뜻한다.

아래의 샘플을 확인해보자.

{% highlight ts %}
let time = 0;
setInterval(() => {
  time++;
}, 1000);
{% endhighlight %}

위의 샘플을 보면 time 변수는 1000ms마다 지속적으로 데이터가 변환되는 중이다.
이것을 Observable로 데이터를 반환하고자 하면 아래와 같은 코드가 가능하다. 

{% highlight ts %}
let time = 0;
setInterval(() => {
  time++;
}, 1000);
const obs$ = new Observable((observer) => {
  observer.next(time);
})
{% endhighlight %}

당연히 console을 찍고자해도 구독이 발생하지 않았기 때문에 데이터 스트림을 읽어올 수는 없다.
다만, Cold Observable과의 가장 큰 차이점이라고 하면 위의 코드는 단순한 객체 선언이 아니라 이미 생성자(`Producer`)는 꾸준히 데이터가 갱신되는 중이라는 점이다.
아래 코드로 바로 확인이 가능하다.

{% highlight ts %}
let time = 0;
setInterval(() => {
  time++;
}, 1000);
const obs$ = new Observable((observer) => {
  observer.next(time);
})
setTimeout(() => {
  const subscriber1 = obs$.subscribe(v => console.log(v));
}, 5000) // 5
setTimeout(() => {
  const subscriber2 = obs$.subscribe(v => console.log(v));
}, 10000) // 10
{% endhighlight %}

0부터 1초마다 1이 더해지는 interval 구조에서 각각 5초, 10초마다 구독을 요청했기에 구독자1에게는 5, 구독자2에게는 10이 찍히게 된다.
말 그대로 subscribe하는 시점에서 데이터가 갱신되는 것이 아닌 흘러가는 데이터를 중간부터 참조하게 된다는 의미이다.

이것이 생성자를 내부부터 생성하여 subscribe 시점부터 갱신되어 데이터 스트림을 반환하는 Cold Observable과 가장 큰 차이점이다.

## **Cold Observable** vs **Hot Observable**
그럼 누군가는 Cold Observable 구조가 좋은건가요? Hot Observable 구조가 좋은건가요? 라고 물어볼 수 있을 것이다.
이것은 정답이 없으며 자신이 subscribe하고자 하는 Producer가 웹 소켓과 같은 지속적으로 갱신되어 전달되는 데이터냐 아니면 subsribe 할 때마다 갱신하여 반환되는 데이터냐에 따라 갈릴 뿐이다.

좀 더 쉽게 얘기하자면,

내가 지금 보고자 하는 게 어떤 영화의 DVD를 구매하여 처음부터 보고자 한다면 이것은 Cold Observable이라고 할 수 있다.
그리고 이것은 여러명이 각자 DVD를 구매하여 플레이한다 하여도 누구든지 처음부터 시청해야 할 것이다.

하지만 보고자 하는 게 아프리카TV 라이브 방송이라면 당연히 누구든지 라이브가 진행 중인 중간에 참여하여 보게 될 것이고 어느 누구던 같은 화면을 공유하여 보고 있을 것이다.
이것이 Hot Observable 구조이다.

## **Cold & Hot Mix**
필요에 따라서는 적절히 Cold Observable과 Hot Observable 방식을 적절히 mix하여 적용해야하는 경우도 있다.

아래 코드를 보자.
{% highlight ts %}
const observer$ = interval(1000);
{% endhighlight %}

interval 연산자를 활용한 Observable은 기본적으로 Cold Observable이다.
구독(`subscribe`)를 선언한 순간부터 각 구독자에게 멀티캐스트 방식으로 0부터의 데이터 스트림을 보내게 된다는 말이다.

그럼 이 Cold Observable인 interval 데이터 스트림을 Hot Observable처럼 서로 공유 가능한 데이터 스트림으로 만들어보자.

{% highlight ts %}
const observer$ = interval(1000);

const subscriber1 = observer$.subscribe(v => console.log(v)); // 0, 1, 2, 3 ~~~~
setTimeout(() => {
  const subscriber2 = observer$.subscribe(v => console.log(v));
}, 10000) // 10초 뒤에 0, 1, 2, 3 ~~~~
{% endhighlight %}

위의 샘플은 방금 전 Cold Observable 구조를 설명하기 위해 작성한 샘플 코드였다.

우리가 원하는 구조는 10초 뒤에 발생하는 subscriber는 interval 데이터 스트림을 10, 11, 12 ~~~ 와 같이 중간부터 캐치를 하는 것이다.

{% highlight ts %}
import { interval } from 'rxjs';
import { share } from 'rxjs/operators';

const observer$ = interval(1000).pipe(
  share()
);

const subscriber1 = observer$.subscribe(v => console.log(v)); // 0, 1, 2, 3 ~~~~
setTimeout(() => {
  const subscriber2 = observer$.subscribe(v => console.log(v));
}, 10000) // 10초 뒤에 10, 11, 12, 13 ~~~~
{% endhighlight %}

pipe에 share를 선언한 것만으로 interval 연산자가 Hot Observable 구조로 바뀌게 되었다.
물론 이 방식 외에도 publish(), refCount()를 통한 데이터 공유도 가능하지만 그런 부분에 대해선 다음 포스팅에 작성하도록 하겠다.

Cold Observable 구조의 Observable을 Hot Observable로 만들 때는 당연히 지속적인 변화 가능성이 있는 데이터여야 하는 기본 조건도 필요하겠으나 이런 식으로 데이터 스트림을 공유하겠다고 선언한 순간부터는 구독자가 몇 명이 생기더라도 같은 데이터 스트림을 바라보게 된다.

아주 단순하지만 Cold Observable과 Hot Observable의 구조적 원리를 파악하기에는 더 없이 깔끔한 샘플이라고 볼 수 있을 것이다.