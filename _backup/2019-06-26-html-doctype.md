---
title:  "DOCTYPE이란?"
date:   2019-06-26 13:37:23 +0900
categories: Javascript
tags:
  - javascript
  - html
  - doctype
---
HTML에 관련된 질문 중 아마 가장 많다면 많을 질문이 DOCTYPE에 대한 질문이고 가장 기본적인 질문이기도 하다.

## **DOCTYPE이란 무엇인가요?**
HTML의 DOCTYPE은 크게 HTML, XHTML(`XML+HTML`), HTML5 크게 세가지로 나눌 수 있다.
여기서 HTML5는 DTD(`Document Type Definition`)의 영향을 받지 않는다.

HTML과 XHTML의 몇 가지 차이로는
- XHTML은 XML 문법 규칙을 따르므로 HTML 문법 규칙과 약간의 차이가 있다.
- 당연한 얘기이겠으나 XHTML에서는 되고 HTML에서는 안되는 또는 그 반대인 일들이 있다.
- CSS와 자바스크립트를 해석하는 부분에 차이가 있다.

상세하게 정확히 뭐가 되고 안되는지까지 들어가야될 부분인지는 애매하다.

같은 HTML4.01, XHTML1.0과 같은 DOCTYPE을 선언했더라도 DTD의 선언 방식에 따라 또다시 문서의 해석 기준이 달라지게 된다.
DTD는 HTML, XHTML이 동일하게 Strict, Transitional, Frameset 세가지로 분류된다.

Strict는 말 그대로 그 DOCTYPE에 요소와 속성을 매우 엄격하게 검증한다.
즉, Deprecate되는 요소와 속성에 호환성을 제공하진 않는다는 말이다.

Transitional은 가장 폭 넓게 사용됐었으며 Deprecate되는 요소와 속성에 대한 호환성을 제공한다.

Frameset은 거의 사용되지 않아 의미는 없지만 굳이 의미론적으로 보자면 페이지 내 Frame 요소를 활용한 문서 타입을 뜻한다.

DOCTYPE은 말 그대로 DOCTYPE의 종류와 DTD의 종류만 설명할 수 있다면 이론적인 접근에서는 충분하지 않나 생각한다.