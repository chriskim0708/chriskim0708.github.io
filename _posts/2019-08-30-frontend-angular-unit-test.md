---
layout: post
title: 'Angular Framework Jamine + Karma Unit Test'
date: 2019-08-30 10:37:00 +0900
categories: Angular
background: '/img/bg-post.jpg'
---

# Angular Unit Test
Angular 프레임워크는 기본적으로 `Jasmine`, `Karma`로 테스트를 할 수 있는 환경을 제공한다.

# Jasmine
Front-End 테스트 개념인 BDD(`Behavior Driven Development`) 테스트 프레임워크이다.
TDD와 BDD의 개념은 말만 다르다고 생각할 수 있으나 메서드의 내부 로직에 대한 검증을 TDD라고 본다면 BDD는 사용자의 행동이 인입되고 이후의 동작들을 테스트 단위로 분리하여 검증하는 방식이라고 간단히 이해하면 될 듯 하다.

# Karma
실제 브라우저 환경에서 자바스크립트를 실행시킬 수 있는 자바스크립트 테스트 러너이다.
단위테스트용 프레임워크는 아니기 때문에 테스트 자체는 `Mocha`, `Jasmine`을 사용하고 테스트 실행만 `Karma`가 수행하게 된다.

# 프레임워크 테스트 방식

## Component 단위테스트
자바스크립트 프레임워크를 활용할 때 BDD 방식의 단위테스트 범위는 생각보다 매우 정확하다.
Front-End는 사용자가 보는 뷰 단위의 검증을 이뤄져야되는 만큼 단위테스트 범위는 뷰를 이루는 `Component` 단위로 움직이게 된다.
모든 Component 기준으로 .spec 파일을 만들고 검증하는 것을 기본으로 접근하도록 한다.

## Service, Pipe 단위테스트
Service나 Pipe의 테스트를 하고 싶다면 BDD가 아닌 TDD라고 생각하고 접근하면 된다.
이 경우에는 메서드에 대한 테스트 케이스만 만들고 검증하면 된다.

## 기초 디버깅 테스트
basic.spec.ts를 생성(기본적으로 타입스크립트를 사용)하여 기초 테스트코드를 작성해보자.

```ts
describe('기초 테스트코드 작성', () => {
  it('true is true', () => {
    expect(true).toBe(true);
  });
});
```

`expect` 문법은 검증할 값이라고 보면 되고 `toBe`는 검증 조건이라고 보면 된다.
물론 조건이 되는 부분은 여러가지가 있을 수 있다.

우선은 간단하게 expect의 true값이 true가 맞는지를 검증하는 간단한 테스트코드이다.

`npm run test`를 실행(Angular package.json scripts에 이미 선언되어 있음)하면 Karma 테스트러너가 위의 테스트코드를 실행해준다.

spec.ts로 선언된 모든 테스트코드를 검증하지만 `DEBUG` 모드를 통해서 `describe` 범위 혹은 `it` 범위에 breakpoint를 걸고 디버깅이 가능하다.