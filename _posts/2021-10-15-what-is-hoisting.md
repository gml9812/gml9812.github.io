---
title: "호이스팅이란 무엇인가?"
last_modified_at: 2021-10-15T16:20:02-05:00
categories:
  - FrontEnd
tags:
  - javascript
toc: true
toc_label: "목차"
---

MDN Web Docs에 따르면, 호이스팅(hoisting)이란 *인터프리터가 변수와 함수의 메모리 공간을 선언 전에 미리 할당하는 것* 이다.

여기까지만 들으면 어떤 개념인지 아리송하다. 좀 더 쉽게 풀어서 설명하자면, 호이스팅은 *변수의 선언만 유효 범위의 최상단으로 옮기는 것* 이라고 할 수 있겠다. 예시를 보자.


## 호이스팅의 예시

```javascript
function myName(name) {
  console.log("제 이름은 " + name + "입니다");
}
var name = "영희";

myName("철수");
myName(name);
/*
결과: 
"제 이름은 철수입니다."
"제 이름은 영희입니다."
*/
```

당연한 결과다. 그렇지 않은가? 하지만 자바스크립트보다 C나 Python과 같은 언어에 익숙한 사람이라면, 아래 결과는 퍽 이상하게 보일 것이다. 

```javascript
myName("철수");
myName(name);

function myName(name) {
  console.log("제 이름은 " + name + "입니다");
}
var name = "영희";


/*
결과: 
"제 이름은 철수입니다"
"제 이름은 undefined입니다"
*/
```
함수 자체보다 함수 호출이 앞서 존재하고, 선언하기도 전에 변수를 사용하고 있지만, 첫 번째 myName 호출은 물론, 두 번째 myName 호출조차도 "영희" 대신 "undefined"가 출력될 뿐 오류 없이 동작한다. 

이런 현상은 자바스크립트 Parser가 함수 실행 전 필요한 변수 및 함수를 유효 범위의 최상단에 선언하기 때문에 일어난다. 

```javascript
/*자바스크립트 Parser 내부의 호이스팅 결과*/
var name = undefined;

function myName(name) {
  console.log("제 이름은 " + name + "입니다");
}

myName("철수");
myName(name);

name = "영희";
```
변수의 선언만 코드 최상단으로 옮겨지고, 초기화는 원래 위치에 남아 있기에 위와 같은 결과가 나오는 것이다. 

## 더 많은 예제 

아래와 같은 코드가 있을 때, 무엇이 출력될 지, 어떤 변수에 대해 호이스팅이 발생할지 맞춰 보자.  

```javascript
x = 1; 
console.log(x + " " + y); 
var y = 2; 
```

이 경우에는 "1 undefined" 이 출력되며, y에 호이스팅이 발생한다. x는 var를 사용하지 않고 초기화하였으므로 호이스팅이 발생하지 않는다.

```javascript
/*자바스크립트 Parser 내부의 호이스팅 결과*/
var y = undefined;

x = 1; 
console.log(x + " " + y); 
y = 2; 
```

아래와 같은 코드라면 어떨까. 

```javascript
a = '크랜'; 
b = '베리'; 

console.log(a + "" + b); 
```

이 경우 "크랜베리" 가 출력된다. 변수 초기화 시 변수 선언이 자동으로 병행되므로 변수 사용은 가능하기 때문이다. 다만 var를 사용하지 않았으므로 호이스팅은 발생하지 않는다.

```javascript
/*자바스크립트 Parser 내부의 호이스팅 결과*/
a = '크랜'; 
b = '베리'; 

console.log(a + "" + b); 
```
그렇다면 아래와 같은 경우에는 어떨까? 

```javascript
function sum (a, b) {
  var x = add(a,b);
  return x;

  function add (c, d) {
    var result = c+d;
    return result;
  }
}
```
함수선언문에서도 아래와 같이 호이스팅이 일어난다. 

```javascript
/*자바스크립트 Parser 내부의 호이스팅 결과*/
function sum (a, b) {
  var x = undefined;
  function add (c, d) {
    var result = c+d;
    return result;
  }

  x = add(a,b);
  return x;
}
```

마지막으로 함수표현식의 경우 어떤 일이 일어날까?

```javascript
 function printName(firstname) { 
     console.log(inner); 
     var result = inner(); 
     console.log("name is " + result);

     var inner = function() { 
         return "inner value";
     }
 }
 printName(); 
```

이 경우 TypeError가 발생한다. result에 inner가 할당되는 순간, inner는 아직 함수가 아니라 undefined이기 때문이다. 

```javascript
/** --- JS Parser 내부의 호이스팅(Hoisting)의 결과 --- */
 function printName(firstname) { 
     var inner = undefined; 
     var result = undefined;

     console.log(inner); 
     result = inner(); 
     console.log("name is " + result);

     inner = function() { 
         return "inner value";
     }
 }
 printName();
```

## let과 const

코드의 가독성 및 유지보수를 위해 호이스팅은 가급적 피해야 한다. 이를 위해 var 대신 사용되는 것이 let과 const이다. 
let과 const를 사용할 때 역시 호이스팅은 일어나나, let과 const의 경우 초기화하기 전에는 읽거나 쓸 수 없다. 초기화 전에 접근을 시도할 경우 ReferenceError가 발생한다. 변수 스코프의 맨 위부터 변수 초기화 완료 시점까지의 변수는 "시간상 사각지대(Temporal Dead Zone, TDZ)" 라고 표현한다.

```javascript
function do_something() {
  //TDZ 시작
  console.log(bar); // undefined
  console.log(foo); // ReferenceError
  var bar = 1;
  //TDZ 끝
  let foo = 2;
}
```
"시간상" 사각지대라 부르는 이유는, 사각지대가 코드의 위치가 아니라 코드의 실행 순서에 의해 형성되기 때문이다. 아래 코드는 letVar가 선언되기 이전에 func가 letVar에 접근하지만, 함수의 호출 지점이 TDZ 바깥이기 때문에 정상적으로 동작한다. 

```javascript
{
    const func = () => console.log(letVar); // OK

    let letVar = 3; //TDZ 끝
    func(); // TDZ 밖에서 호출
}
```

참고: 
- https://developer.mozilla.org/ko/docs/Glossary/Hoisting
- https://gmlwjd9405.github.io/2019/04/22/javascript-hoisting.html
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/let