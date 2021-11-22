---
title: "프로토타입(Prototype)이란?"
last_modified_at: 2021-11-03T16:20:02-05:00
categories:
  - FrontEnd
tags:
  - javascript
toc: true
toc_label: "목차"
---

console 창에서 Object를 다루다 보면 가끔 이런 화면을 볼 때가 있다. 

![image](https://user-images.githubusercontent.com/28294925/142824305-797815c5-1cd0-4c91-b97c-28ec4da957de.png)

여기 나와 있는 __proto__란 대체 무엇을 의미하는 것일까? 


## 자바스크립트에서 Object를 생성하는 방법

본론에 들어가기에 앞서, 자바스크립트에서 Object를 생성하는 세 가지 방법을 먼저 짚고 넘어가자. 

첫 번째로 new Object()를 사용하는 방법이 있다. 

```javascript
var test = new Object()
console.log(test);// 텅 빈 Object를 생성한다.
```

다음으로 Object literal을 사용하는 방법이 있다.

```javascript
var test = {
  first: "Hello",
  second: "World",
  sayHello: function() {
    return this.first + " " + this.second
  }
}
```

마지막으로, Object Constructor를 사용하는 방법이 있다.

```javascript
function Test(first, second) {
	this.first = first,
	this.second = second,
	this.sayHello = function() {
		return this.first + " " + this.last;
	}
}

var test = new Test("Hello", "World")
```

Java나 C++과 같은 언어에서 Object를 생성하기 위해 Class가 사용되는 것처럼, 자바스크립트에서는 Object 생성을 위해 위와 같은 Object constructor function이 사용된다. 

## Constructor function의 문제점

아래와 같은 코드를 실행한다면, 어떤 일이 벌어질까?

```javascript
var test1 = new Test("Hello", "World")
var test2 = new Test("Nice to", "Meet")
```
정답: 자바스크립트 엔진은 construction function을 두 개 복제해 test1과 test2에 건네 준다. 

![image](https://user-images.githubusercontent.com/28294925/142826972-e6348040-eb78-416b-9ac8-55bf3703eac3.png)

문제는, 두 instance에서 sayHello 함수는 정확히 같은 역할을 하는 데도 불구하고 test1, test2 각각에 sayHello 함수가 복제되어 저장된다는 점이다. 

## 프로토타입 

### 함수
자바스크립트에서 A란 이름의 함수를 만드는 상황을 가정해 보자. 이 때, 자바스크립트 엔진은 prototype이란 property를 함수 A에 자동적으로 추가한다. 

property는 하나의 object로,constructor이란 property를 가지고 있다. constructor는 prototype을 property로 가진 함수, 즉 A를 가리킨다. 말로 설명하기에는 복잡하지만, 그림을 보면 쉽게 이해할 수 있다. 

![image](https://user-images.githubusercontent.com/28294925/142828852-10b7e5cd-bac3-4c43-9584-c8d2c2dbc211.png)

아래 코드를 보면서 좀 더 확실하게 이해해 보자. 

```javascript
function Human(firstName, lastName) {
	this.firstName = firstName,
	this.lastName = lastName,
	this.fullName = function() {
		return this.firstName + " " + this.lastName;
	}
}

var person1 = new Human("Virat", "Kohli");
```
여기서 console.log(person1)을 하면, 아래와 같은 결과를 얻을 수 있다. 

![image](https://user-images.githubusercontent.com/28294925/142829141-99db6191-05d3-43dd-8c1b-1b3330da7c1b.png)

그러면 console.log(Human.prototype)을 하면 어떨까?

![image](https://user-images.githubusercontent.com/28294925/142829305-f58695bc-581e-4da2-803c-aee6c9e41bdc.png)


### 객체(Object)
자바스크립트에서 Object를 생성할 때, 자바스크립트 엔진은 __proto__property(dunder proto)를 object에 추가한다. 그리고 __proto__는 constructor function의 prototype object를 가리킨다. 

![image](https://user-images.githubusercontent.com/28294925/142830167-f3328541-2314-486b-918a-d858f47e47e0.png)

따라서 아래 코드 역시 true를 반환한다. 

```javascript
function Human(firstName, lastName) {
	this.firstName = firstName,
	this.lastName = lastName,
	this.fullName = function() {
		return this.firstName + " " + this.lastName;
	}
}

var person1 = new Human("Virat", "Kohli");
Human.prototype === person1.__proto__ // true
```

위의 내용을 정리하자면, constructor function의 prototype object는 constructor function을 사용해 만들어진 모든 object 사이에서 공유된다고 말할 수 있다. 

![image](https://user-images.githubusercontent.com/28294925/142830928-8f313158-3cce-4711-9097-96b84107d6da.png)

이런 특성을 이용하면, prototype object에 property와 method를 추가함으로서 constructor function을 통해 만들어진 모든 object가 해당 property와 method를 사용할 수 있게 만들 수 있다. 아래 예시를 보자. 

```javascript
//Create an empty constructor function
function Person(){

}
//Add property name, age to the prototype property of the Person constructor function
Person.prototype.name = "Ashwin" ;
Person.prototype.age = 26;
Person.prototype.sayName = function(){
	console.log(this.name);
}

//Create an object using the Person constructor function
var person1 = new Person();

//Access the name property using the person object
console.log(person1.name)// Output" Ashwin
```
위 코드를 실행하면 "Ashwin"이 출력된다. 그러나 이상하게도, console.log(person1)을 실행하면 어디에도 name이란 property가 존재하지 않는 것을 볼 수 있다. 

![image](https://user-images.githubusercontent.com/28294925/142831785-b8684d7d-8de8-4193-ac47-c68de2323864.png)

자바스크립트 엔진은 object 내에 property가 없을 시, __proto__를 뒤져 property를 찾는다. 만약 그곳에서도 찾고자 하는 property가 없다면, __proto__의 __proto__를 뒤지기 시작한다. 이 과정은 __proto__값이 null이 될 때까지 계속되는데, 만약 null이라면 undefined가 반환된다. 

이해를 돕기 위한 또 하나의 예시를 보자. 

```javascript
var person2 = new Person();
//Access the name property using the person2 object
console.log(person1.name)//Output: Ashwin
console.log(person2.name)//Output: Ashwin
person1.name = "Anil"
console.log(person1.name)//Output: Anil
console.log(person2.name)//Output: Ashwin
```

위의 예시에서 console.log(person1.name)을 처음 수행할 경우에는 person1 내에 name property가 없으므로, __proto__에서 name을 찾아 출력했지만, console.log(person1.name)을 두 번째로 수행할 경우에는 person1내에 name property가 있으므로 Anil을 출력하게 된다. 

## 프로토타입의 문제점

prototype object가 reference type, 예를 들어 리스트를 property로 가지고 있다고 하자. 그럼 아래와 같은 경우, 문제가 생길 수 있다. 

```javascript
//Create an empty constructor function
function Person(){
}
//Add property name, age to the prototype property of the Person constructor function
Person.prototype.name = "Ashwin" ;
Person.prototype.age = 26;
Person.prototype.friends = ['Jadeja', 'Vijay'],//Arrays are of reference type in JavaScript
Person.prototype.sayName = function(){
	console.log(this.name);
}

//Create objects using the Person constructor function
var person1= new Person();
var person2 = new Person();

//Add a new element to the friends array
person1.friends.push("Amit");

console.log(person1.friends);// Output: "Jadeja, Vijay, Amit"
console.log(person2.friends);// Output: "Jadeja, Vijay, Amit"

```
아마 위 코드를 작성한 사람은, person1의 friends 목록에만 "Amit"을 추가하고, person2의 friends는 ['Jadeja', 'Vijay']로 유지하고자 했을 것이다. 그러나 prototype이 처리되는 방식 때문에, person1과 person2의 friends 모두 바뀌어 버리게 되었다.

Constructor와 Prototype을 융합하는 것으로 이런 문제를 해결할 수 있다. 

```javascript
//Define the object specific properties inside the constructor
function Human(name, age){
	this.name = name,
	this.age = age,
	this.friends = ["Jadeja", "Vijay"]
}
//Define the shared properties and methods using the prototype
Human.prototype.sayName = function(){
	console.log(this.name);
}
//Create two objects using the Human constructor function
var person1 = new Human("Virat", 31);
var person2 = new Human("Sachin", 40);

//Lets check if person1 and person2 have points to the same instance of the sayName function
console.log(person1.sayName === person2.sayName) // true

//Let's modify friends property and check
person1.friends.push("Amit");

console.log(person1.friends)// Output: "Jadeja, Vijay, Amit"
console.log(person2.friends)//Output: "Jadeja, Vijay"
```
위 예제를 통해 생성된 각각의 Object는 저만의 name, age, friends를 가지지만, sayName 함수는 prototype에 정의되어 있어 모든 Object가 공유한다. 이 방법을 사용하면 한 Object의 property를 변경했을 때 다른 Object의 property까지 영향이 가는 것을 방지할 수 있고, 모든 Object가 불필요한 함수 instance를 복제해 가짐으로서 메모리를 낭비하는 것을 막을 수 있다. 


참고: 
- https://betterprogramming.pub/prototypes-in-javascript-5bba2990e04b
- https://medium.com/tech-tajawal/javascript-classes-under-the-hood-6b26d2667677