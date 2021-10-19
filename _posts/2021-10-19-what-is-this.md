---
title: "this란 무엇인가?"
last_modified_at: 2021-10-19T16:20:02-05:00
categories:
  - FrontEnd
tags:
  - javascript
toc: true
toc_label: "목차"
---

this는 일반적으로 *메소드를 호출한 객체가 저장되어 있는 속성* 이다. 하지만 반드시 그런 건 아니다. MDN에 따르면, 대부분의 경우 this의 값은 함수를 **호출한 방법**에 의해 결정된다. 각각의 상황에서 this의 값을 알아보자.

## 전역 문맥(global context)에서 this

전역 실행 문맥(global execution context)에서, this는 strict 모드에 관계없이 전역 객체(window)를 반환한다. 

```javascript
a = 42;
console.log(window.a); // 42

this.b = "MDN";
console.log(window.b); // "MDN"
console.log(b); // "MDN"

console.log(this === window); // true
```

## 일반 함수에서 this

함수 내부에서 this는 strict 모드일 때는 undefined, strict 모드가 아닐 경우에는 window를 반환한다.

```javascript
/*strict 모드*/
function strMode(){
'use strict';
return this;
}

console.log(strMode() === undefined); // true
```

```javascript
function noStrMode() {
  return this;
}

console.log(noStrMode() === window); // true
```

## 객체의 메소드에서 this

함수를 객체의 메소드로 호출하면 this의 값은 그 객체를 사용한다. 

```javascript
var o = {
  prop: 37,
  f: function() {
    return this.prop;
  }
};
console.log(o.f()); // 37
```
함수를 호출한 방법에 따라 this의 값이 결정된다는 것에 유의해야 한다. 예를 들어 아래 코드는 함수를 먼저 정의한 다음 o.f를 추가했지만, 똑같이 37을 출력한다. 

```javascript
var o = {prop: 37};

function independent() {
  return this.prop;
}

o.f = independent;

console.log(o.f()); // 37
```
그럼 아래 코드는 어떨까?

```javascript
function independent() {
  return this.prop;
}
var o = {prop: 37};
o.b = {g: independent, prop: 42};
console.log(o.b.g());
```

이 경우 42가 출력되며, this는 o.b를 나타낸다.

## 화살표 함수에서 this

화살표 함수에서 this는 **호출되는 시점하고 무관하게** 선언되는 시점에 결정되며, 언제나 상위 스코프의 this를 가리키다. 

아래 코드의 결과를 예상해 보자. 

```javascript
var obj = { 
    myName: '철수', 
    logName: function() { 
        console.log(this.myName); 
    }
};

obj.logName();
```
this가 obj를 반환하니, "철수" 가 출력될 것이라 어렵지 않게 알 수 있다. 그러면 아래는 어떨까?

```javascript
var obj = { 
    myName: '철수', 
    logName: function() { 
        console.log(this.myName); 
    }
};
obj.logName();
```
이 경우 undefined가 출력된다. obj의 상위 스코프는 전역 스코프(window)이므로, window.myName을 출력하려 시도하기 때문이다. 

아래의 코드는 어떨까?

```javascript
var obj = {
  bar: function() {
    var x = (() => this);
    return x;
  }
};
var fn = obj.bar();
console.log(fn() === obj);
```

obj.bar에 할당된 함수(=함수 A)는 화살표 함수로 생성된 다른 함수(=함수 B)를 반환한다. 함수 B의 this는 함수 A의 this, 즉 obj로 설정되기에 "true" 가 출력된다. 

## 생성자에서 this

함수를 new 키워드와 함께 생성자로 사용하면, this는 새로 생긴 객체가 된다. 

```javascript
function C() {
  this.a = 37;
}

var o = new C();
console.log(o.a); // 37


function C2() {
  this.a = 37;
  return {a: 38};
}

o = new C2();
console.log(o.a); // 38
```

## 이벤트 처리기에서 this

이벤트 처리기로 사용된 함수 안의 this는 이벤트를 유발한 요소로 설정된다.

```javascript
function bluify(e) {
  console.log(this === e.currentTarget); // true
  console.log(this === e.target); // e.currentTarget === target일시 true
  this.style.backgroundColor = '#A5D9F3';
}

var elements = document.getElementsByTagName('*');

for (var i = 0; i < elements.length; i++) {
  elements[i].addEventListener('click', bluify, false);
}
```
## call,apply,bind

함수 메소드인 call,apply,bind를 사용하면, 함수를 호출한 방법에 무관하게 this에 원하는 값을 지정할 수 있다. 

call과 apply는 this로 사용할 객체와 함수 호출에 필요한 인수들을 받아, 함수가 주어진 객체의 메소드인 것처럼 사용할 수 있다. 

call은 인수를 직접 받으며, apply는 인수를 배열 형태로 받는다. 

```javascript
function add(c, d) {
  return this.a + this.b + c + d;
}

var o = {a: 1, b: 3};

//o는 this로 사용할 객체를 말한다. 
add.call(o, 5, 7); // 16
add.apply(o, [10, 20]); // 34
```

bind를 사용하면 원본 함수와 같은 코드, 같은 범위를 가졌지만 this는 다른 새로운 함수를 생성한다. 이 때, 새로운 함수의 this는 호출 방식과 상관없이 영구적으로 bind()의 첫 번째 매개변수로 고정된다. apply, call, 심지어는 bind를 다시 쓴다 해도 this는 변하지 않는다!

```javascript
function f() {
  return this.a;
}

console.log(f()); // undefined

var g = f.bind({a: 'azerty'});
console.log(g()); // azerty

var h = g.bind({a: 'yoo'}); // bind는 한 번만 동작함!
console.log(h()); // azerty

var o = {a: 37, f: f, g: g, h: h};
console.log(o.a, o.f(), o.g(), o.h()); // 37, 37, azerty, azerty
```


참고: 
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this
- https://tychejin.tistory.com/144
- https://velog.io/@grinding_hannah/JavaScript-%ED%99%94%EC%82%B4%ED%91%9C%ED%95%A8%EC%88%98arrow-function-%EC%95%8C%EA%B8%B0