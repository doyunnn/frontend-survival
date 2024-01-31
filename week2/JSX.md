## 목표
### - JSX

- React에서 JSX를 사용하는 목적
- React.createElement
- React Element
- VDOM(Virtual DOM)이란?
- Reconciliation(재조정) 과정은 무엇인가?

---

## 내용
### 1. JSX
리액트는 JS문법이 아닌 JSX라는 문법을 사용한다.

- JSX란 Javascript + XML 의 약자로써 자바스크립트와 XML이 합쳐진 문법이다.
- 아래의 예제들 처럼 본래 div 태그를 만들고 싶다면 React.createElement를 사용해야한다.
```jsx
import './App.css';
import React from 'react';

function App() {
  return (
        React.createElement('div',null,'Hello World')
  );
}

export default App;
```

- 하지만 이렇게 코드를 작성한다면 상당히 복잡해지고 가독성이 떨어지므로,
- JSX 문법을 사용해서 코드를 작성후 브라우저에서 실행 되기전에 바벨이 자바스크립트 형태로 변환을 시켜준다.
- 아래 예시들이 JSX를 변환한 것들이다.

### Example 1

```jsx
<p>Hello, world!</p>;

React.createElement('p', null, 'Hello, world!');

// @jsx를 코드의 상단에 작성하면 JSX를 JS로 변환할 때 아래와 같이 생성된다.
// jsx를 리액트 외부에서 사용하고 싶을 때 이런 방식으로 사용할 수 있다.

/* @jsx r.createElement */
<p>Hello, world!</p>;

r.createElement('p', null, 'Hello, world!');
```

### Example 2

```jsx
<Greeting name="world" />;

React.createElement(Greeting, { name: 'world' });
```

### Example 3

```jsx
<Button type="submit">Send</Button>;

React.createElement(Button, { type: 'submit' }, 'Send');
```

### Example 4

JSX에서는 아래와 같이 작성할 수 없다.  
React Element는 React.createElement 메소드를 실행하기 위해 1개의 부모만 가질 수 있기 때문이다.  
`<div>` 같은 태그로 감싸주거나 렌더링되고 나서 돔에 태그를 삽입하고 싶지 않다면, React.Fragment를 이용한다.  
`<React.Fragment></React.Fragment>`는 아예 생략해서 `<></>` 이렇게 쓸 수 있다.

```jsx
  <p>Hello, world!</p>
  <Button type="submit">Send</Button>

  <>
    <p>Hello, world!</p>
    <Button type="submit">Send</Button>
  </>

  React.createELement(
    React.Fragment,
    null,
    React.createElement('p', null, 'Hello, world!'),
    React.createElement('Button', {type: "submit"}, 'Send'),
  )
```

```jsx
<div className="test">
  <p>Hello, world!</p>
  <Button type="submit">Send</Button>
</div>;

React.createElement(
  'div',
  { className: 'test' },
  React.createElement('p', null, 'Hello, world!'),
  React.createElement(Button, { type: 'submit' }, 'Send'),
);
```

### Example 5

jsx는 태그안에서 text와 JavaScript 값으로만 구분을 한다, 임의로 공간을 주려면  
`{' '}`을 이용하면 createElement에 추가된다.

```jsx
<div>
  <p>Count: {' '} {count}!</p>
  <button type="button" onClick={() => setCount(count + 1)}>
    Increase
  </button>
</div>;

React.createElement(
  'div',
  null,
  React.createElement('p', null, 'Count: ', ' ', count, '!'),
  React.createElement(
    'button',
    { type: 'button', onClick: () => setCount(count + 1) },
    'Increase',
  ),
);
```

### React Element

```js
// 명령형 방식의 코드

document.createElement('div');

const element = document.createElement('div');

// 태그에 단일 클래스명을 지정할 때
element.className = 'test';

// 태그에 복수의 클래스명을 지정할 때
element.classList = 'test, good';
element.classList = ['test', 'good'].join(' ');

element.appendChild(document.createElement('p'));
```

- createElement는 React Element를 만들어낸다.
- Browser DOM에서도 element를 만드는 createElement가 있다.

JSX는 React Element 트리를 갱신하는데 쓸 수 있다.

React.createElement를 직접 쓰면 힘들기 때문에  
jsx-runtime에서는 \_jsx, Preact는 h란 함수를 지원한다.

### React Developer Tools

- chrome에서 설치할 수 있으며 컴포넌트 구조나 state의 변화를 확인할 수 있고, 리렌더링 되는 컴포넌트도 확인할 수 있다.

### VDOM (Virtual DOM)

- DOM은 HTML같은 document를 표현하기 위해서 트리구조를 이용한다.
- 그러나 script에 의해서 DOM의 속성이 변경이 되어야 한다면 전체 app를 리렌더링 해야하는 단점이 있다.
- VDOM은 전체 app을 리렌더링 하는 대신 최소한의 DOM만 조작할 수 있게 해준다.
- 여기서 실제 DOM과 VDOM을 비교하는 것을 `Reconcilation` 이라고 한다.

- VDOM을 쓰면 일을 2번 하는 것이다, 바로 DOM에 반영해주면 될탠데 왜 VDOM을 쓸까?

### Reconciliation(재조정)

- React의 render() 함수는 state나 props가 갱신되면 새로운 React Element Tree를 반환 한다.
- 이 때 React는 이전 트리를 방금 만들어진 트리에 맞게 가장 효과적으로 UI를 갱신하는 방법을 알아 낸다. 
- 하나의 트리를 다른 트리로 변환하기 위한 최소한의 연산 수를 구하는 알고리즘의 일반적인 복잡도는 O(n^3)의 복잡도이다.
- React에서 이 알고리즘을 적용하면, 1000개의 Element가 있다면 10억번의 비교 연산을 해야 합니다. 
- 이는 비싼 연산 -> React는 대신 두가지 가정을 기반하여 O(n) 복잡도를 가진 알고리즘을 구현.
  - 1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.
  - 2.  key prop를 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해 줄 수 있다.

#### 엘리먼트 타입이 다른 경우

```jsx
<div>
  <Counter />
</div>

<span>
  <Counter />
</span>
```

- 두 root 엘리먼트의 타입이 다르면 React는 이전 트리를 버리고 완전히 새로운 트리를 구축.

#### Key prop

```jsx
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```
- DOM 노드의 자식들을 재귀적으로 처리할떄, React는 기본적으로 동시에 두 리스트를 순회하고 차이점이 있으면 변경을 생성한다.
- 위와 같이 마지막 항목이 추가되면, React는 첫번째 리스트의 첫번째 두번째 항목을 재사용하고 세번째 항목을 추가한다.

```jsx
<ul>
  <li>1</li>
  <li>2</li>
</ul>

<ul>
  <li>3</li>
  <li>1</li>
  <li>2</li>
</ul>
```
- 그러나 첫번째 항목이 추가되면, React는 종속 트리를 그대로 유지하는 대신 모든 자식을 변경한다.
- 이러한 비효율은 문제가 될 수 있다.


```jsx
<ul>
  <li key={'1'}>1</li>
  <li key={'2'}>2</li>
</ul>

<ul>
  <li key={'3'}>3</li>
  <li key={'1'}>1</li>
  <li key={'2'}>2</li>
</ul>
```

- 이러한 문제를 해결하기 위해, React는 key속성을 지원한다.
- 자식들이 key를 가지고 있다면, React는 key를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인.
- React는 '3'key를 가진 엘리먼트가 새로 추가되었다는 것을 인지하고 '1'와 '2'key를 가진 엘리먼트는 그저 이동만 하면 되는 것을 인지한다.

### 성능 최적화

리액트에서의 성능 최적화는 방식이 다양하다.  
제대로된 이해를 바탕으로 상황에 맞는 최적화를 진행해야 한다.  
`useMemo`, `useCallback` 등의 캐싱 없이도 컴포넌트 구조의  
분리만으로도 최적화가 가능하다.
