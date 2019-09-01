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
단위 테스트용 프레임워크는 아니기 때문에 테스트 자체는 `Mocha`, `Jasmine`을 사용하고 테스트 실행만 `Karma`가 수행하게 된다.

# 프레임워크 테스트 방식

## Component 단위 테스트
자바스크립트 프레임워크를 활용할 때 BDD 방식의 단위 테스트 범위는 생각보다 매우 정확하다.
Front-End는 사용자가 보는 뷰 단위의 검증을 이뤄져야되는 만큼 단위 테스트 범위는 뷰를 이루는 `Component` 단위로 움직이게 된다.
모든 Component 기준으로 .spec 파일을 만들고 검증하는 것을 기본으로 접근하도록 한다.

## Service, Pipe 단위 테스트
Service나 Pipe의 테스트를 하고 싶다면 BDD가 아닌 TDD라고 생각하고 접근하면 된다.
이 경우에는 메서드에 대한 테스트 케이스만 만들고 검증하면 된다.

# 기초 디버깅 테스트
basic.spec.ts를 생성(기본적으로 타입스크립트를 사용)하여 기초 테스트 코드를 작성해보자.

```ts
describe('기초 테스트 코드 작성', () => {
  it('true is true', () => {
    expect(true).toBe(true);
  });
});
```

`expect` 문법은 검증할 값이라고 보면 되고 `toBe`는 검증 조건이라고 보면 된다.
물론 조건이 되는 부분은 여러가지가 있을 수 있다.

우선은 간단하게 expect의 true값이 true가 맞는지를 검증하는 간단한 테스트 코드이다.

`npm test`를 실행(package.json scripts에 이미 선언되어 있음)하면 Karma 테스트러너가 위의 테스트 코드를 실행해준다.

FAILED 상황은 간단하게 테스트 코드를 바꿔보면 확인할 수 있다.

```ts
describe('기초 테스트 코드 작성', () => {
  it('true is true', () => {
    expect(true).toBe(false);
  });
});
```

`break point`를 잡아가며 디버깅 테스트를 하고 싶을 경우
1. Karma의 DEBUG 모드로 들어간다.
2. `shift + ctrl + i`로 개발자 모드를 연다.
3. `ctrl + p`로 파일 검색을 실행하고 디버깅 하고자 하는 파일명을 찾는다. (위 기준이라면 basic.spec.ts)
4. it 영역을 기준으로 break point를 잡아가며 코드를 디버깅한다.

# Component 테스트

## 대상 Component 생성

Component 테스트 코드를 작성 전에는 대상이 되는 Component를 미리 만들어야 한다.

```ts
// src/app/convension/hello/hello.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-hello',
  template: '<h1>{{title}}</h1>',
})
export class HelloComponent {
    title = 'Hello World';
}
```
## 테스트 코드 내용
HelloComponent <h1> 태그에 title 속성이 정상적으로 들어갔는지 검증하고 title 속성이 변경됐을 경우 정상적으로 변경되는지 검증한다.

## 테스트 코드 작성
```ts
// src/app/convension/hello/hello.component.spec.ts
import { ComponentFixture, TestBed } from "@angular/core/testing";
import { By } from "@angular/platform-browser";
import { DebugElement } from "@angular/core";

import { HelloComponent } from "./hello.component";

describe('HelloComponent', () => {
  let comp: HelloComponent;
  let fixture: ComponentFixture<HelloComponent>;
  let de: DebugElement;
  let el: HTMLElement;

  /**
   * 테스트 케이스 실행 전 세팅 동작
   */
  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [HelloComponent] // module에 component 선언
    });

    fixture = TestBed.createComponent(HelloComponent); // 테스트 component 생성 (디버깅과 테스트가 가능한 component)

    comp = fixture.componentInstance; // 객체와 메서드 정보

    de = fixture.debugElement.query(By.css('h1')); // template의 h1 tag 정보
    el = de.nativeElement; // h1의 native dom 속성
  });

  it('<h1> 내용이 view model인 title과 같아야 한다.', () => {
    fixture.detectChanges();
    expect(el.textContent).toContain(comp.title);
  });
 
  it('view model에 변경이 일어나면 <h1> 내용은 변경된 view model과 같아야 한다.', () => {
    comp.title = 'Hello World Change!!'
    fixture.detectChanges();
    expect(el.textContent).toContain('Hello World Change!!');
  });
});
```
## 테스트 코드 설명

### TestBed
Angular에서 Component가 동작하기 위해서는 `@NgModule` Declaration에 Component가 선언되어야 한다.
정상적인 구현 경로를 따라가자면 Module에 Component가 선언되고 LifeCycle이 이뤄지기 전에 Module에 Component가 생성된다.
TestBed.configureTestingModule은 실제 `@NgModule`이 아닌 테스트를 위한 `@NgModule`을 생성하고 테스트 대상 Component를 분리하기 위한 구성이라고 보면 된다.

### createComponent (* synchronous)
기본 개념을 이해하고 있다면 왜 fixture에 createComponent가 대입되는지 알 수 있다.
Component가 생성된 이후에 Module Configuration은 변경될 수 없다.
그래서 TestingModule이 선언된 이후에 fixture에 테스트 대상 Component가 생성되는 것이다.

fixture의 타입은 [ComponentFixture](https://angular.io/api/core/testing/ComponentFixture){:target="_blank"}이며 해당 객체는 componentInstance와 debugElement를 가지고 있다.
디버깅과 테스트를 지원하기 위한 객체라고 보면 된다.

### detectChanges
data binding이 이뤄지면 dom element에 data를 전달해주기 위해 호출한다.
Component가 init되고 life cycle이 활성화 되면서 자동으로 auto change detection 되는 부분을 직접 호출하는 개념으로 보면 된다.
이것은 Module에 Component Declaration 되고 create 되는 것과는 다른 의미다.
빈 껍데기에 속을 채워넣는 과정으로 이해하면 되겠다.

아래의 테스트 코드로 detectChanges가 이뤄지지 않을 경우 어떻게 동작하는지 확인해보자.

```ts
it('data binding이 되기 전에는 <h1> 내용이 비어있어야 한다.', () => {
  expect(el.textContent).toEqual('');
});
```

주의할 부분은 view model의 data가 변경되면 이것을 dom element로 전달해주기 위해 반드시 다시 호출해줘야 한다는 것이다.
아래 코드로 init 단계에서 detectChanges를 한번만 호출하고 view model data가 변경됐을 때 수정하지 않으면 어떻게 되는지 살펴보자.

```ts
it('title이 변경됐어도 detectionChanges를 호출하지 않으면 이전 타이틀 정보로 남아있어야 한다.', () => {
  const prevTitle = comp.title;
  fixture.detectChanges(); // title이 변경되기 전 호출
  comp.title = 'Hello World Change!!';
  expect(el.textContent).toContain(prevTitle);
});
```

테스트 케이스가 comp.title이 변경되기 전의 title일 경우에 테스트 케이스가 통과되는 것을 볼 수 있다.

### Auto detectChanges
Component Init 단계에서 data binding이 dom element를 바로 갱신하게 하고 싶다면 아래 providers를 추가하면 된다.

```ts
import { ComponentFixtureAutoDetect } from '@angular/core/testing';
...

beforeEach(() => {
  TestBed.configureTestingModule({
    declarations: [HelloComponent], // module에 component 선언
    providers: [{ // providers에 ComponentFixtureAutoDetect 추가
      provide: ComponentFixtureAutoDetect, useValue: true
    }]
  });
});
```

주의할 점은 init 단계에서 자동 data binding이라는 점이다.
view model data가 변경되는 경우에는 detectChanges를 다시 호출해줘야 한다.

이제 위에 작성했던 테스트 케이스들에 변경이 필요하다.

```ts
it('data binding이 되기 전에는 <h1> 내용이 비어있어야 한다.', () => {
  expect(el.textContent).toEqual(''); // 이 케이스는 이제 무조건 실패
});

it('<h1> 내용이 view model인 title과 같아야 한다.', () => {
  // fixture.detectChanges(); --> ComponentFixtureAutoDetect이 적용되면 호출하지 않아도 통과
  expect(el.textContent).toContain(comp.title);
});

it('view model에 변경이 일어나면 <h1> 내용은 변경된 view model과 같아야 한다.', () => {
  comp.title = 'Hello World Change!!'
  fixture.detectChanges(); // 여전히 호출해야 통과
  expect(el.textContent).toContain('Hello World Change!!');
});

it('title이 변경됐는데도 detectionChanges를 호출하지 않으면 이전 타이틀 정보로 남아있어야 한다.', () => {
  const prevTitle = comp.title;
  // fixture.detectChanges(); --> title이 변경되기 전 호출. ComponentFixtureAutoDetect이 적용되면 호출하지 않아도 통과
  comp.title = 'Hello World Change!!';
  expect(el.textContent).toContain(prevTitle);
});
```
개인적으로는 강제로 init data binding이 되어 dom element를 갱신하기 때문에 약간 귀찮은 면이 있더라도 detectChanges를 테스트 케이스마다 사용하여 내가 원하는 타이밍에 dom element를 갱신하는 것을 추천하고 싶다.

# templateUrl을 사용하는 Component 테스트
Component에 템플릿을 삽입하는 방식은 2가지 방식이 있다.
하나는 코드 내에 템플릿을 바로 작성하는 template, 또 하나는 외부에 html파일을 만들고 링크하여 사용하는 templateUrl 방식이다.

```ts
@Component({
  selector: "app-hello",
  template: "<h1>{{title}}</h1>" // 방법1
})

@Component({
  selector: "app-hello",
  templateUrl: "./hello.component.html" // 방법2
})

```

왜 이 부분을 설명하냐면 테스트 코드를 작성할 때 templateUrl 방식으로 만들어진 component에는 해당 html이 compile 되는 시간이 필요하기 때문이다.
이 경우에는 beforeEach 부분을 async 함수로 감싸줘야한다.

```ts
// hello.component.spec.ts
import { async, ComponentFixture, TestBed, ComponentFixtureAutoDetect } from "@angular/core/testing";

beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [HelloComponent], // module에 component 선언
    providers: [
      {
        // providers에 ComponentFixtureAutoDetect 추가
        provide: ComponentFixtureAutoDetect,
        useValue: true
      }
    ]
  }).compileComponents().then(() => { // compile template and css;
    fixture = TestBed.createComponent(HelloComponent); // test component 생성 (디버깅과 테스트가 가능한 component)

    comp = fixture.componentInstance; // 객체와 메서드 정보

    de = fixture.debugElement.query(By.css("h1")); // template의 h1 tag 정보
    el = de.nativeElement; // h1의 native dom 속성
  });
}));
```
### compileComponents
TestBed.compileComponents 메서드는 비동기적으로 module에 선언된 component들을 compile한다.
TestBed.configureTestingModule의 다음 단계로 진행되며 compile된 이후의 구성을 다시 할 순 없다.

promise의 resolve와 reject을 반환하기 때문에 성공적으로 compile이 됐다면 then 메서드를 통해 다음 단계의 진행이 가능하다.
compile 다음 단계의 진행으로 createComponent를 하는 것이 좋다.

# Component가 Service와 의존 관계를 가질 경우의 테스트
일반적으로 Angular에서 Serivce의 사용 빈도는 높다.
때문에 component 테스트 코드 작성에 있어서 service의 의존 관계 선언과 테스트는 거의 필수적으로 포함된다고 봐야 한다.

## Service 생성
의존 관계 테스트를 위해 welcome 폴더 내의 services에 user.service.ts 파일을 생성해준다.
welcome 폴더에서 ng g service user로 생성해줘도 되고 직접 파일 생성을 해도 된다.
generate를 사용하여 생성할 경우 기본 구조를 가진 service.ts와 service.spec.ts 파일 2개가 생성된다.

```ts
// user.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class UserService {

  constructor() { }
}
```

```ts
// user.service.spec.ts
import { TestBed } from '@angular/core/testing';

import { UserService } from './user.service';

describe('UserService', () => {
  beforeEach(() => TestBed.configureTestingModule({}));

  it('should be created', () => {
    const service: UserService = TestBed.get(UserService);
    expect(service).toBeTruthy();
  });
});
```
UserService의 테스트 코드는 should be created만 검증하는 수준에서 일단 넘어가도록 한다.

## 테스트 코드 내용
UserService에 저장된 사용자 정보를 바탕으로 아직 사용자의 로그인이 이뤄지지 않았다면 `로그인 해주세요.` 문구를 보여주고 이미 로그인된 사용자라면 `환영합니다, 홍길동님.`과 같이 사용자 이름과 환영 문구를 보여준다.

## 테스트 코드 작성

```ts
// welcome.component.ts
import { Component, OnInit } from "@angular/core";
import { UserService } from "./services/user.service";

@Component({
  selector: "app-welcome",
  templateUrl: "./welcome.component.html"
})
export class WelcomeComponent implements OnInit {
  welcome = '';

  constructor(private userService: UserService) {

  }

  ngOnInit() {
    this.welcome = this.userService.isLoggedIn() ? `${'환영합니다, ' + this.userService.userName() + '님.'}` : '로그인 해주세요.';
  }
}
```

Component의 OnInit LifeCycle이 활성화 되는 시점에 로그인한 유저라면 환영 문구를 view model에 넣어주고 아니라면 로그인 유도 문구를 넣어주는 코드를 작성했다.

다만, UserService를 import하고 isLoggedIn 메서드와 userName 메서드를 활용해야 하기 때문에 UserService에 아래와 같이 메서드 타입을 잡아줘야 한다.

```ts
// services/user.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class UserService {

  constructor() { }

  isLoggedIn: () => boolean;
  userName: () => string;
}
```

Service 메서드는 타입만 선언하고 provide로 useValue를 사용하거나 작성된 테스트 코드 기반으로 코드를 재작성 해주면 된다.

```ts
// welcome.component.spec.ts
import {
  async,
  ComponentFixture,
  TestBed
} from "@angular/core/testing";
import { By } from "@angular/platform-browser";
import { DebugElement } from "@angular/core";

import { WelcomeComponent } from "./welcome.component";
import { UserService } from "./services/user.service";

describe("WelcomeComponent", () => {
  let comp: WelcomeComponent;
  let fixture: ComponentFixture<WelcomeComponent>;
  let de: DebugElement;
  let el: HTMLElement;
  let userServiceStub;
  let userService;

  beforeEach(async(() => {
    userServiceStub = { // UserService 추가하고자 하는 메서드는 메서드 스텁 방식으로 진행
      isLoggedIn: () => {
        return true;
      },
      userName: () => {
        return '홍길동';
      }
    }

    TestBed.configureTestingModule({
      declarations: [WelcomeComponent],
      providers: [
        // UserService, --> 메서드 스텁을 사용할 경우 UserService를 providers에 선언하지 않는다.
        { provide: UserService, useValue: userServiceStub } // 메서드 스텁을 useValue로 사용하여 테스트
      ]
    }).compileComponents().then(() => {
      fixture = TestBed.createComponent(WelcomeComponent);
      comp = fixture.componentInstance;
      userService = TestBed.get(UserService);

      de = fixture.debugElement.query(By.css('.welcome')); // 문구가 표시되는 element
      el = de.nativeElement;
    });
  }));
  
  it('Component가 활성화 상태여야 한다.', () => {
    expect(comp).toBeTruthy();
  });

  it('사용자에게 환영 문구를 보여줘야 한다. (환영합니다, 홍길동님.)', () => {
    fixture.detectChanges();
    const content = el.textContent;
    expect(content).toContain('환영합니다');
    expect(content).toContain('홍길동');
  });

  it('사용자명 홍길동이 표시되어야 한다.', () => {
    fixture.detectChanges();
    expect(el.textContent).toContain('홍길동');
  });
  
  it('로그인하지 않았을 경우, 로그인 유도 문구를 보여줘야 한다.', () => {
    userService.isLoggedIn = () => { // 로그인이 되지 않은 상태로 메서드 스텁 변경
      return false;
    }
    fixture.detectChanges();
    const content = el.textContent;
    expect(content).not.toContain('환영합니다', '홍길동'); // 로그인 되지 않았으니 환영 문구 표시가 되지 않아야 한다.
    expect(content).toContain('로그인 해주세요.'); // 로그인 유도 문구 표시
  });
});
```

## 테스트 코드 설명
실제 UserService에 메서드를 선언하고 가져와서 쓰는 방식이 아닌 메서드 스텁 구현으로 provide 처리하여 테스트 한다는 점을 주의하자.

AutoDetectChange를 추천하고 싶지 않은 이유는 위의 3번째 테스트 케이스를 예로 들 수 있다. userService의 메서드 스텁을 변경해주고 그 후에 dom element 변경 호출을 하고 싶으나 AutoDetectChange를 활용해버리면 계속 메서드 스텁 초기에 선언된 return 값이 적용되서 테스트가 어렵다.

때문에 AutoDetectChange는 없애고 메서드 스텁을 갱신한 후 다시 detectChanges를 호출하는 방식으로 개선하였다.

Component compile 단계에서 Service 의존성 선언은 아래 2가지 방식으로 가능하다.

```ts
userService = TestBed.get(UserService);
```

```ts
userService = fixture.debugElement.injector.get(UserService);
```

