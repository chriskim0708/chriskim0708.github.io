---
layout: posts
title:  "Observable Hot, Cold의 구분"
date:   2019-06-28 12:06:00 +0900
categories: Rxjs
background: '/img/bg-post.jpg'
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
  const arr = [1,2,3,4,5].forEach(v => observer.next(v));
});

// 2번 방식
const observer2$ = from([1,2,3,4,5]);
{% endhighlight %}

위의 코드는 연산자를 사용했느냐 안했느냐의 차이일 뿐이고 실제 전달되는 데이터는 같다.
다만, 구독(`subscribe`)이 되기 전까지는 데이터를 전달하지 않는 단순한 객체 선언일 뿐이다.
구독자가 선언된 순간부터 실제 데이터를 전달하기 시작한다.

구독자를 선언하는 방법은 아래와 같다.

{% highlight ts %}
const subscriber1 = observer1$.subscribe(v => console.log(v));
const subscriber2 = observer2$.subscribe(v => console.log(v));
{% endhighlight %}

이때부터는 별다른 작업 없이 관찰자(`observer`)로부터 데이터를 읽어들이는 게 가능해진다.
기본적으로는 멀티캐스트 방식으로 각 구독자에게 독립적으로 데이터 스트림을 내보낸다.

Observable에서 Producer가 선언되고 그 Producer가 구독을 선언한 시점부터 데이터 스트림이 방출되는 구조를 우리는 Cold Observable이라고 부른다.

## **Hot Observable**
위의 설명으로는 Cold Observable이 이해될 수는 있으나 그 반대 개념으로 Hot Observable을 이해하기는 어렵다.
마찬가지로 샘플 코드로서 Hot Observable을 확인해보자.

간단한 설명으로는 우선 Hot Observable이란 Producer가 Observale 밖에서 생성되고 이것을 Observale이 관찰하는 구조를 뜻한다.

아래의 2가지 코드를 비교해보자.

{% highlight ts %}
// Cold Observable WebSocket Connect Sample
const observer$ = new Observable((observer) => {
  const source$ = interval(1000);
  observer.next(take(1000));
})
{% endhighlight %}

{% highlight ts %}
// Hot Observable WebSocket Connect Sample
const socket = new Socket(ws://someurl');
const observer$ = new Observable((observer) => {
  socket.addEventListener('message', (e) => observer.next(e));
})
{% endhighlight %}

아주 단순하게 2가지 코드 모두 WebSocket 생성자를 참조하는 구조이지만,
Cold Observable의 경우에는 Observable이 선언된 시점부터, Hot Observable은 이미 생성되어 동작하고 있는 WebSocket을 중간부터 구독한다는 점에서 차이가 있다.
Cold Observable은 구독이 없다면 단순한 객체 선언일 뿐이지만, Hot Observable은 구독에 상관없이 이미 데이터는 생성되고 있다는 의미이다.
다만, Hot Observable은 구독이 발생하기 전에는 이미 생성된 데이터가 지속적으로 손실되고 있을 뿐이다.

2가지 코드 다 모두 장단점을 가지고 있다.

Cold Observable 구조에서는 관찰자가 선언된 시점부터 웹 소켓이 연결되기 때문에 구독을 하게 되면 언제나 웹 소켓이 재갱신이 되는 현상이 발생할 것이다.
이것은 말 그대로 모든 구독자가 동일한 웹 소켓을 데이터를 공유할 수 없다는 것을 뜻한다.

Hot Observable 구조에서는 모든 구독자가 동일한 데이터 스트림을 공유할 수 있지만 오류, 완료, 구독 취소 등의 행위에서 웹 소켓을 닫는 작업을 진행할 수 없다.

그럼 우리가 이제 해볼 것은 Hot Observable처럼 모든 구독자가 데이터 스트림을 공유할 수 있으면서도 Cold Observable처럼 오류, 완료, 취소 등에 대한 제어가 가능한 구조를 만들어 보는 것이다.

아래의 코드로서 Hot, Cold Observable을 병합하여 효율적인 코드를 작성해보자.

{% highlight ts %}
import { Observable, interval, throwError, of } from 'rxjs';
import { take, publish, catchError, mergeMap } from 'rxjs/operators';

const socket$ = new Socket(ws://someurl');
const observer$ = socket$.pipe(
  catchError(err => of(`socket error`)),
  tap(msg => msg === 'socket error' ? socket$.close() : console.log('ok')),
  publish()
)

const subscriber1 = observer$.subscribe(v => console.log('sub1: ' + v));

setTimeout(() => {
  const subscriber2 = observer$.subscribe(v => console.log('sub2: ' + v));
}, 10000)

observer$.connect();
{% endhighlight %}

위 코드의 구조를 보면 기본적으로는 Hot Observable 구조에 해당한다고 볼 수 있다.
socket$에서는 이미 웹 소켓 데이터가 구조적으로 흘러가고 있으며