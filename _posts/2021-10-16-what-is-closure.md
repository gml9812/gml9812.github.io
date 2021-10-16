---
title: "클로저란 무엇인가?"
categories:
  - FrontEnd
tags:
  - javascript
toc: true
toc_label: "목차"
---

MDN Web Docs에 따르면, 클로저(closure)란 *함수와 함수가 선언된 어휘적 환경의 조합* 이다.

더 쉽게 말하면, 클로져는 *함수 내에서 함수를 정의하고 사용하는 것이고, 이 때 정의된 함수는 만들어진 환경을 기억한다* 라고 할 수 있겠다. 클로저=함수+환경(Lexical environment)이라고 할까. 여기서 말하는 '환경' 이 무엇인지 이해하려면, 먼저 Lexical scoping에 대해 알아야 한다. 


## Lexical scoping

아래 코드를 실행하면 어떤 일이 일어날까?

```javascript
function init() {
    var name = "Mozilla";
    function displayName() { // displayName() 은 내부 함수이며, 클로저다.
      alert(name); // 부모 함수에서 선언된 변수를 사용한다.
    }
    displayName();
  }
  init();
```

이 경우, displayName()은 "Mozilla" 라는 메시지를 alert한다. displayName()은 init()안에서만 사용될 수 있는 내부 함수이다. displayName()은 부모 함수 init()에서 선언된 변수 name에 접근해 alert를 실행할 수 있다. 그렇다면 아래와 같은 경우에는 어떨까? 

```javascript
function init() {
    var name = "Mozilla";
    function displayName() { 
      var name = "Look at me!";
      alert(name); 
    }
    displayName();
  }
  init();
```
이 경우, displayName()은 "Look at me!" 라는 메시지를 alert한다. Lexical scoping이란,위 예시들과 같이 파서가 변수를 처리하는 과정에서 변수가 어디에서 호출되었는지가 아니라 '코드 내 어디에서 선언되었는지' 를 고려하여 처리하는 것을 의미한다. 

그럼 아래와 같은 경우는 어떨까?

```javascript
var x = 1;

function first() {
  var x = 10;
  second();
}

function second() {
  console.log(x);
}

first(); 
second(); 
```

10과 1이 출력될 것이라 생각할 수 있지만, 사실 위의 코드는 1과 1을 출력한다. 이는 second()가 first()안에서 '호출'되었지만, 그와 상관없이 second()는 global 범위에 '선언'되어 있으므로 global의 x값인 1을 출력한 것이다. 

클로저가 기억하는 '환경' 이란, 바로 Lexical scope를 말한다. 즉 함수를 만들고, 그 함수 내부 코드가 탐색하는 스코프를 함수 생성 당시의 Lexical scope로 고정한 것이 바로 클로저이다. 

## 클로져 예시

아래 코드에서 일어나는 일을 생각해 보자.

```javascript
var color = 'red';
function foo() {
    var color = 'blue'; // 2
    function bar() {
        console.log(color); // 1
    }
    return bar;
}
var baz = foo(); // 3
baz(); // 4
```
1. bar는 color를 찾아 출력하는 함수로 정의되었다.
2. bar는 outer environment 참조로 foo의 environment를 저장했다.
3. global에서 baz(=bar)를 호출했다.
4. bar는 자신의 스코프에서 color를 찾는다.
5. 찾을 수 없다. 자신의 outer environment 참조를 찾는다.
6. outer environment인 foo의 스코프를 뒤져 color='blue'를 찾는다. 

위 코드에서 bar는 global에서 호출되었음에도 불구하고, 현재 실행 스택과는 관련 없는 foo에서 color를 탐색한다. 이런 bar와 같은 함수를 클로저라고 부른다. 

+사진 추가하기

아래와 같은 코드에서는 어떤 일이 벌어질까?

```javascript
function count() {
    var i;
    for (i = 1; i < 10; i += 1) {
        setTimeout(function timer() {
            console.log(i);
        }, i*100);
    }
}
count();
```
놀랍게도 1,2,3,4,...9가 0.1초마다 출력되는 대신, 10이 9번 출력된다. 앞선 예제와 같이 코드에서 벌어지는 일을 해석해 보자.

1. timer는 i를 찾아 출력하는 함수로 정의되었다.
2. timer는 outer environment 참조로 count의 environment를 저장했다.
3. setTimeout으로 인한 0.1초의 대기시간이 지날 동안, for문이 종료되고 i = 10이 된다. 
4. 첫 0.1초가 지나고 timer가 호출된다.
5. timer는 자신의 스코프에서 i를 찾는다.
6. 찾을 수 없다. 자신의 outer environment 참조를 찾는다. 
7. outer environment인 foo의 스코프를 뒤져 i=10을 찾는다. 
8. 다음 0.1초가 지나고 time가 호출된다. 위의 과정을 반복하여 i=10을 찾는다. 

그럼 의도대로 1~9를 출력하고 싶다면 어떤 방법을 사용해야 할까? 

첫 번째로, 새로운 스코프를 추가하여 반복 시마다 각각 따로 값을 저장할 수 있다.

```javascript 
 function count() {
     var i;
     for (i = 1; i < 10; i += 1) {
         (function(countingNumber) {
             setTimeout(function timer() {
                 console.log(countingNumber);
             }, i * 100);
         })(i);
     }
 }
 count();
 ```
위 코드에서, timer는 outer environment 참조로 즉시 실행 함수(IIFE)의 environment를 참조한다. timer는 IIFE의 스코프에서 i의 값이 저장된 countingNumber를 찾아 출력한다. 

```javascript 
 function count() {
     'use strict';
     for (let i = 1; i < 10; i += 1) {
         setTimeout(function timer() {
             console.log(i);
         }, i * 100);
     }
 }
 count();
 ```
위 코드에서는 var 대신 let이 사용되었다. var는 함수 레벨 스코프를 따르지만, let은 블록 레벨 스코프를 따른다. 따라서, 위 코드에서 i는 for 루프의 코드 블록 내에서만 유효한 변수이다. 
<-- 추가 설명 -->

## 성능 관련 고려 사항

각각의 클로저는 환경을 기억한다. 달리 말하면, 메모리가 소모된다는 뜻이다. 클로저를 생성하고 참조를 제거하지 않는 건 C++에서 동적할당으로 생성한 객체를 delete하지 않는 것과 유사하다.  

```javascript
function hello(name) {
  var _name = name;
  return function() {
    console.log('Hello, ' + _name);
  };
}

var hello1 = hello('승민');
var hello2 = hello('현섭');
var hello3 = hello('유근');

hello1(); // 'Hello, 승민'
hello2(); // 'Hello, 현섭'
hello3(); // 'Hello, 유근'

// 여기서 메모리를 release 시키기 클로저의 참조를 제거해야 한다.
hello1 = null;
hello2 = null;
hello3 = null;
```

참고: 
- https://ljtaek2.tistory.com/145
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures
- https://meetup.toast.com/posts/86