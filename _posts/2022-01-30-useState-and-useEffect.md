---
title: "useState와 useEffect 좀 더 깊게 알아보기"
last_modified_at: 2022-01-30T16:20:02-05:00
categories:
  - FrontEnd
tags:
  - react
toc: true
toc_label: "목차"
---

최근 React 함수 컴포넌트와 React Hook을 사용한 프로젝트를 진행하면서, useState와 useEffect가 예상치 못한 행동을 하는 경우를 만났다. 예를 들어...

- useState에서 반환된 함수 setState()를 사용해 state를 변경한 다음 console.log(state)를 하면 변경 전의 값이 출력된다.
- useState에서 반환된 함수 setState()를 연속으로 사용할 경우, 마지막으로 사용한 setState만이 반영된다.
- 가끔씩 useEffect 안에서 이전 state나 props 값을 참조한다.
- 컴포넌트가 마운트될 때만 사용하고 싶은 effect가 있어 useEffect에 deps(의존성 배열)로 []를 줬는데, linter가 경고를 날린다.

React Hook을 배울 당시 useState는 state 변수와 해당 변수를 갱신할 수 있는 함수를 만들어 주는 Hook, useEffect는 dep 안에 들어 있는 state들이 변할 때마다 effect를 실행해 주는 Hook, 정도로만 이해하고 넘어갔었다. 이제는 좀 더 깊은 이해가 필요한 시점이라는 생각이 들어 포스팅을 작성하게 되었다.

## useState

### useState에서 반환된 함수 setState()를 사용해 state를 변경한 다음 console.log(state)를 하면 변경 전의 값이 출력된다.

아래 코드를 실행하면 어떤 일이 벌어질까?

```javascript
const [movie, setMovie] = useState("Avatar");

useEffect(() => {
  console.log(movie);
  setMovie("Star wars");
  console.log(movie);
}, []);
```

'Avatar', 'Star wars'가 아니라, 'Avatar', 'Avatar' 가 출력된다. 이는

### useState에서 반환된 함수 setState()를 연속으로 사용할 경우, 마지막으로 사용한 setState만이 반영된다.

아래와 같은 코드를 실행하면 화면에는 어떤 숫자가 나타날까?

```javascript
const [count, setCount] = useState(2);
// ...
function someFunction() {
  setCount(count * 3);
  setCount(count - 1);
}
// ...
return <div>{count}</div>;
```

2 \* 3 - 1 = 5가 나타나는 대신, 2 - 1 = 1이 나타난다. setCount를 아무리 연속해서 사용해 보아도 맨 마지막으로 사용한 setCount만 적용되는 것을 확인할 수 있다.

이 현상을 이해하기 위해서는 useState의 특성을 먼저 이해해야만 한다.

## useEffect

[아주 좋은 가이드](https://overreacted.io/a-complete-guide-to-useeffect/)가 있어 이해가 빨랐다.

### 가끔씩 useEffect 안에서 이전 state나 props 값을 참조한다.

이 현상을 이해하기 위해서는 함수 컴포넌트가 동작하는 방식을 이해해야 한다.

**React 함수 컴포넌트는 클로저와 유사한 방식으로 동작한다!**

다시 말해,

**컴포넌트 안에 있는 모든 함수(이벤트 핸들러, effect, effect의 클린업, setTimeout과 그 안에서 호출되는 API 등등...)는 렌더될 때 정의된 props와 state 값을 사용한다!**

아래 예시를 보자.

```javascript
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setTimeout(() => {
      console.log(`You clicked ${count} times`);
    }, 10000);
  });
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

1초 간격으로 버튼을 5번 누르고, console에 메시지가 출력될 때까지 기다린다고 가정해 보자. 어떤 일이 일어날까?

1. 출력 시점의 count가 반영되어 'You clicked 5 times' 가 5번 출력된다.
2. setTimeout이 수행될 때의 count가 반영되어 'You clicked 1 times', 'You clicked 2 times' ... 가 출력된다.

클로저가 어떻게 작동하는 지 알고 있다면 답이 2라는 것을 어렵지 않게 짐작할 수 있을 것이다. 각각의 렌더는 하나의 props와 state를 계속 공유하는 것이 아니라, 각자마다 props와 state를 가지고 있는 것이다.

좀 더 자세한 이해를 위해 위의 함수 컴포넌트가 마운트될 때 React, 컴포넌트, 브라우저 사이에서 일어나는 일을 요약해 보자면...

1. React는 컴포넌트에게 state가 0일 때 화면에 표시될 내용을 요청한다.
2. 컴포넌트는 <p>You clicked 0 times</p>를 return한다. 동시에, 렌더링이 끝나고 () => {document.title = 'You clicked 0 times' } 라는 effect를 실행하라 전달한다.
3. React는 컴포넌트가 return한 내용에 맞추어 브라우저에게 DOM을 업데이트하라 지시한다.
4. 브라우저는 그에 맞추어 렌더링을 실행한다.
5. React는 컴포넌트가 전달해 준 effect를 실행한다.

버튼을 클릭할 때 일어나는 일을 요약해 보자면...

1. 컴포넌트는 setState()를 사용해 React에게 state를 1로 변경해 달라고 요청한다.
2. React는 컴포넌트에게 state가 1일 때 화면에 표시될 내용을 요청한다.
3. 컴포넌트는 <p>You clicked 1 times</p>를 return한다. 동시에, 렌더링이 끝나고 () => {document.title = 'You clicked 1 times' } 라는 effect를 실행하라 전달한다.
4. 브라우저는 그에 맞추어 렌더링을 실행한다.
5. React는 컴포넌트가 전달해 준 effect를 실행한다.

이러한 React의 동작 방식 때문에 useEffect가 과거의 props, state를 참조하는 것처럼 보이는 현상이 발생했던 것이다.

### 컴포넌트가 마운트될 때만 사용하고 싶은 effect가 있어 useEffect에 deps(의존성 배열)로 []를 줬는데, linter가 경고를 날린다.

무언가 의도하지 않은 현상이 발생할 수 있기에 linter가 경고를 보낼 텐데, 과연 그 '의도하지 않은 현상' 이 뭘까?

타이머의 count를 1초 간격으로 1씩 증가시키려 하는 상황을 가정해 보자.

```javascript
const [count, setCount] = useState(0);

useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, []);
```

겉보기에는 전혀 문제가 없어 보인다. 그러나 실제로 이 함수를 실행하면, count가 0에서 1로 딱 한 번 증가하고는, 계속 1에서 변하지 않는 것을 확인할 수 있다.

위 섹션에 설명된 대로, **React 함수 컴포넌트는 클로저와 유사한 방식으로 동작한다!** 이 코드에서 useEffect()는 컴포넌트가 처음 렌더링될 때 단 한 번만 effect를 실행한다. 처음 렌더링될 때 count는 0이다. effect는 count = 0을 기억하고 1초 간격으로 setCount(0 + 1)을 수행한다...

deps에 effect에서 사용되는 **모든** 값을 적어넣지 않으면 의도치 않게 이와 같은 오류를 만들 확률이 높기에 linter가 경고를 보내는 것이다. 그럼 위 코드가 의도한 대로 작동하게 바꾸려면 어떻게 해야 할까?

일단은 count를 deps에 넣어 보자.

```javascript
const [count, setCount] = useState(0);

useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, [count]);
```

이러면 1초마다 count는 정상적으로 1씩 증가한다. 하지만 위 코드는 count가 바뀔 때마다 setInterval을 해제하고 다시 설정하는 방식으로 동작하기에 상당히 비효율적이고, 직관적이지도 않다. 함수 형태의 업데이터를 setState에 사용해 deps를 아예 사용하지 않는 형태로 effect를 바꾸면 이 문제를 해결할 수 있다.

```javascript
useEffect(() => {
  const id = setInterval(() => {
    setCount((c) => c + 1);
  }, 1000);
  return () => clearInterval(id);
}, []);
```

[여기](https://medium.com/@sdolidze/the-iceberg-of-react-hooks-af0b588f43fb)서 함수형 업데이터를 쓰는 방법 외에도 counter를 수정하는 여러 가지 예시를 볼 수 있다.

### 번외: effect에서 '현재' state와 props를 이용하는 법

useRef()을 사용하면 effect가 실행된 렌더가 아니라 '현재' 렌더의 state나 props를 사용할 수 있다. 아래 코드를 실행하고, 1초 간격으로 5번 버튼을 클릭하면 'You clicked 5 times' 가 5번 출력되는 걸 확인할 수 있다.

```javascript
function Example() {
  const [count, setCount] = useState(0);
  const latestCount = useRef(count);
  useEffect(() => {
    // 변경 가능한 값을 최신으로 설정한다
    latestCount.current = count;
    setTimeout(() => {
      // 변경 가능한 최신의 값을 읽어 들인다
      console.log(`You clicked ${latestCount.current} times`);
    }, 10000);
  });
  // ...
```

참고:

- https://overreacted.io/a-complete-guide-to-useeffect/
- https://velog.io/@devjade/React%EC%9D%98-setState-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0
