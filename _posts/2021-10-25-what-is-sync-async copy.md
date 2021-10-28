---
title: "Blocking-Nonblocking-Synchronous-Asynchronous의 차이"
last_modified_at: 2021-10-25T16:20:02-05:00
categories:
  - Network
tags:
  - general
toc: true
toc_label: "목차"
---

![image](https://user-images.githubusercontent.com/28294925/138689115-55e393a5-cb73-4475-8615-a5dacfc5e70a.png)

사진으로만 보아선 잘 이해가 가지 않는다. 먼저 Blocking-Nonblocking-Synchronous-Asynchronous 가 무엇인지 확인하고, 사진 속 매트릭스의 각 사분면이 뜻하는 바를 알아내 보도록 하자.

## Synchronous/Asynchronous란?

함수 A가 함수 B를 호출한다고 가정하자. 동기/비동기란 호출되는 함수(B)의 작업 완료 여부를 호출하는 함수(A)가 신경쓰는지, 그렇지 않은지에 따라 결정된다.

A가 B의 리턴 값을 계속 확인하며 신경쓴다면 동기(Synchronous),

A가 B를 호출할 때 콜백 함수를 함께 전달해, B의 작업이 완료되면 콜백 함수를 실행하는 방식을 사용한다면(그래서 A가 B의 완료 여부를 신경쓰지 않는다면) 비동기(Asynchronous) 라고 한다.

## Blocking/Nonblocking이란?

이번에도 역시 함수 A가 함수 B를 호출한다고 가정하자. Block/nonblock은 호출된 함수(B)가 자신의 일이 끝날 때까지 제어권을 가지고 있는지, 호출한 함수(A)에게 넘겨주는지에 따라 결정된다.

B가 할 일을 마칠 때까지 A가 다른 작업을 하지 못한다면 Blocking,

B가 할 일을 마치지 않아도 A가 다른 작업을 수행하면서 B를 기다릴 수 있다면 Nonblocking 이다.


## Synchronous-Blocking

![image](https://user-images.githubusercontent.com/28294925/139254579-71f9a43b-d55e-446b-bb50-41f0d35173dd.png)

이 경우, A는 B의 리턴값을 필요로 한다(Synchronous). A는 B가 작업을 마칠 때까지 다른 작업을 하지 못하며(Blocking), B가 작업을 마치고 제어권과 리턴값을 넘기면 실행을 재개한다. 

## Synchronous-Nonblocking

![image](https://user-images.githubusercontent.com/28294925/139255868-7aa2b996-df16-4387-8c21-15eb0cb375a8.png)

이 경우 역시, A는 B의 리턴값을 필요로 한다(Synchronous). 그러나 A는 B가 작업을 마칠 때까지 다른 작업을 할 수 있다(Nonblocking). 따라서 A는 실행되는 중간중간 B의 리턴값이 들어왔는지 확인하게 된다. 

## Asynchronous-Blocking

![image](https://user-images.githubusercontent.com/28294925/139257598-b1b98e72-e489-4250-ac6c-6cdd4d825ee1.png)

이 경우, A는 B의 리턴값에 관심이 없다(Asynchronous). 하지만 A는 B가 작업을 마칠 때까지 다른 작업을 할 수 없다(Blocking). Asynchronous-Blocking의 경우 사실상 Synchronous-Blocking과 비교했을 때 별다른 이점이 없어 의도적으로 사용되는 경우는 드물고, Asynchronous-Nonblocking 방식을 사용하는 시스템에 Blocking으로 동작하는 것이 포함되어 있어 의도치 않게 이 방식으로 동작하는 경우가 존재한다. 


## Asynchronous-Nonblocking

![image](https://user-images.githubusercontent.com/28294925/139257533-c6c4784e-d9e9-43d6-bf34-f7f122f3cba6.png)

이 경우, A는 B의 리턴값에 관심이 없다(Asynchronous). 그리고 A는 B가 작업을 마칠 때까지 다른 작업을 할 수 있다(Nonblocking). 따라서, 사실상 A가 B를 호출한 시점부터 둘의 관계는 끝난다. A는 혼자 작업을 이어 나가며, B역시 A를 신경쓰지 않고 콜백 함수를 사용해 작업을 계속한다.


참고: 
- http://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/
- https://velog.io/@nittre/%EB%B8%94%EB%A1%9C%ED%82%B9-Vs.-%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EB%8F%99%EA%B8%B0-Vs.-%EB%B9%84%EB%8F%99%EA%B8%B0
- https://baek-kim-dev.site/38