---
title: 'Vue + Vuex + TypeScript (with Vue Cli 3.x)'
date: 2019-08-02 14:41:00 +0900
categories: Vuejs
tags:
  - vuejs
  - typescript
---

Vuejs를 TypeScript 기반으로 사용하기 위해 기본으로 Vue Cli에서 제공하는 설정 외에 추가해줘야 하는 부분들이 상당히 많았다.
공식 문서에서 단순히 제공하는 정보로는 한계가 있어서 나름의 경험을 바탕으로 기초 Template을 구성한 내용을 정리해보았다.

# **기존의 Vue Component 구조**

우선은 기존의 Vue Component 구조를 알아야 비교하기 쉬울 것이다.

```js
export default {
  name: 'App',
  component: {},
  extends: {},
  props: {},
  data() {
    return {
      msg: 'Hello World!'
    };
  },
  computed: {},
  methods: {},
  // life cycle
  created() {},
  mounted() {},
  updated() {},
  beforeDestory() {}
};
```

몇 가지를 제외했지만 가장 자주 사용하는 Vue Component 구조라고 볼 수 있다.
TypeScript를 적용하기 위해서 위의 Compoent 구조를 상당히 바꿔야만 했다.

# **Decorator**

Vue TypeScript에서는 Decorator의 활용이 가능하다.

```ts
import { Component, Prop, Vue } from 'vue-property-decorator';
```

Decorator의 활용 방식은 아래에 설명하도록 하겠다.

# **Component명, 하위 component 선언 방식 변경**  
## <br>@Component

기존 `name`에 컴포넌트명을 선언하던 방식이 `class` 선언 방식으로 변경되었다.

```ts
import { Vue, Component } from 'vue-property-decorator';

@Component
export default class Home extends Vue {}
```

class 선언부에는 `@Component` 데코레이터를 추가한다.
`@Component` 데코레이터에는 기존에 사용하던 옵션을 추가할 수 있다.

## <br>Child Component 선언

```ts
@Component({
  components: {
    // 하위 컴포넌트 선언
    HelloWorld
  }
})
export default class Home extends Vue {}
```

`@Component` 데코레이터에는 computed, components 등 기존 Vue Component 구조 선언 방식을 override 할 수 있다.
공통 Component의 `extends`에 관해서는 따로 정리하고자 한다.

# **LifeCycle, Computed, Method, data 선언 방식 변경**  
## <br>LifeCycle
TypeScript 방식에서 LifeCycle 선언부는 class내의 Method로 선언이 가능하다.

```ts
export default class Home extends Vue {
  created() {}
  mounted() {}
  updated() {}
  beforeDestory() {}
}
```

## <br>Computed
Computed는 getter 방식으로 선언하면 기존에 Computed에 선언하던 방식과 똑같이 동작한다.

```ts
export default class Home extends Vue {
  get email(): string {
    const user = this.profile && this.profile.user;
    return (user && user.email) || '';
  }
}
```

## <br>Method
Method 선언부는 return type이 지정된(꼭 지정하지 않아도 된다) method 선언을 사용하면 기존의 method와 동일하게 동작한다.

```ts
export default class Home extends Vue {
  greet(): void {} // return이 없는 경우 void
  greetMessage(): string { // return에 대한 type 선언
    return 'Hello World!';
  }
}
```

## <br>data 선언
data에 return 객체로 선언하면 data model 선언은 class 상위의 객체 선언으로 사용할 수 있다.

```ts
export default class Home extends Vue {
  id!: string;
  employNumber!: number;
  name = 'yskim'; // 리터럴이 존재하는 경우 ! 선언할 필요없음
}
```

다만, 객체 리터럴이 존재하지 않는 model 선언부에는 반드시 !를 붙여줘야 Vue TypeScript에서 초기화 선언으로 인식이 가능하다.

# **Vuex**

## <br>vuex-class
Vue TypeScript에서 vuex를 적용하는 방식은 몇 가지가 있겠으나 vuex-class를 사용하는 방식으로 정리해보았다.
Interface 기반으로 vuex store를 모듈별로 분리하는 방식은 차후에 설명을 더 추가할 예정이다.
우선은 module별 store가 존재한다는 가정으로 설명하도록 하겠다.

```
npm i vuex-class
```
먼저 depecdencies에 vue-class를 설치하면 `@State`, `@Action`, `@Getter` Annotaions을 활용 가능하다.

## <br>vuex 선언 및 활용

```ts
import { Vue, Component } from 'vue-property-decorator';
import { State, Action, Getter } from 'vuex-class';
import { ProfileState, User } from '@/store/profile/types';

// 참조할 vuex module namespace
const namespace: string = 'profile';

@Component
export default class Home extends Vue {
  @State('profile') profile!: ProfileState;
  @Action('fetchData', { namespace }) fetchData!: any;
  @Getter('fullName', { namespace }) fullName!: string;
}
```

`@State`에는 Interface 선언이 필요하다.  
관리할 state의 module interface에 맞춰 type 선언을 해주고 선언된 `@Action`과 `@Getter`에 대해 각 어노테이션 기준으로 model 선언을 해서 사용 가능하다.  
Mutations는 Action의 commit을 참조하여 호출하는 것이 바람직한 구조이다.

`@Getter`는 `mustache` 문법으로 템플릿에 바로 적용이 가능하다.
`@Action`은 가급적 Mustations을 컨트롤하는 용도로서 활용하는 게 가장 적절하다고 판단한다.

vuex의 TypeScript 활용 구조는 우선 vuex store를 구성했다는 가정으로 설명했으나 다음 포스팅으로 module기준으로 vuex store 구성하는 방법도 정리해볼 예정이다.