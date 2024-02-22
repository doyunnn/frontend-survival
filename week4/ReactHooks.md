## 목표
### - React Hooks

- React Hook 이란
- Hooks
    - useState
    - useEffect
    - useContext
    - useRef
    - useLayoutEffect
- React StrictMode 란

---

## 내용
### 1. React의 Hook

> [Hooks API Reference](https://react.dev/reference/react/hooks)
>

- React 16부터 체계가 잡힘.
- React 16.8에서 Hooks가 도입됨.
-React 18에서는 Concurrent Mode를 강조.
- 기존 방식에 있던 몇 가지 문제를 해결.

기존 방식의 문제점:

- Wrapper Hell (HoC)
- Huge Components
- Confusing Classes

React를 쓰는 방식을 완전히 바꾼 커다란 변화.

→ 이제는 예전으로 돌아가는 게 불가능하다!

기존:

- 상태를 가진 컴포넌트는 Class Component로 만들고, props만 사용하는 재사용이 용이한 작은 컴포넌트는 Function Component로 작성.
- Redux에서도 비슷한 구분이 존재했다.
    - [Presentational and Container Components - Dan Abramov](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

현재:

- 그냥 Function Component만 사용.
- 상태 관리 유무를 바로 알기 어려움 = 신경쓰지 않아도 됨.
- 복잡한 요소는 전부 Hook으로 격리 및 재사용 가능.

대표적인 Hooks

- useState → State Hook ⇒ React의 State
    - State lets a component “remember” information like user input.
- useEffect ⇒ Side-effect
  - lets you synchronize a component with an external system.
- useContext
  - lets you read and subscribe to context from your component.
    - stores data that can be accessed by any component in the component tree.
- useRef
  -  lets you reference a value that’s not needed for rendering.
- useLayoutEffect → useEffect와 조금 다름.

### 2. useEffect

> [useEffect](https://beta.reactjs.org/reference/react/useEffect)
>

- 렌더링 이후 해야 할 일, 즉 React의 외부와 관련된 일을 정해줄 수 있다.
- 기본적으로 렌더링 때마다 실행되므로, 의존성 배열을 통해 언제 이펙트를 실행할지 지정할 수 있다(= 불필요한 경우에 건너뛸 수 있다).
- 함수를 리턴함으로써 종료 처리를 할 수 있다.

### useEffect의 사용법
- The behaviors without the dependency array and with an empty [] dependency array are different:
```jsx
useEffect(() => {
  // This runs after every render
});

useEffect(() => {
  // This runs only on mount (when the component appears)
}, []);

useEffect(() => {
  // This runs on mount *and also* if either a or b have changed since the last render
}, [a, b]);
```


#### 타이머 예제

React의 외부에 우아하게 접근. 이 정도는 useEffect를 안 쓴다고 크게 문제가 되지 않지만, 이렇게 쓰는 습관을 들이자.

```jsx
useEffect(() => {
	document.title = `Now: ${new Date().getTime()}`;
});
```
타이머를 on/off하는 기능을 그냥 만들면 문제가 발생한다.
```tsx 
function Timer() {
useEffect(() => {
    setInterval(() => {
    document.title = `Now: ${new Date().getTime()}`;
}, 100);
});
    return (
		<p>Playing</p>
	);
}

export default function TimerControl() {
    const [playing, setPlaying] = useState(false);

	const handleClick = () => {
		setPlaying(!playing);
	};

	return (
		<div>
			{playing ? (
				<Timer />
			) : (
				<p>Stop</p>
			)}
			<button type="button" onClick={handleClick}>
				Toggle
			</button>
		</div>
	);
}
```

종료 처리
```jsx
useEffect(() => {
	const savedTitle = document.title;

	const id = setInterval(() => {
		document.title = `Now: ${new Date().getTime()}`;
	}, 100);

	return () => {
		document.title = savedTitle;
		clearInterval(id);
	};
});
```

### 처음에 한번만 실행하기, 데이터 호출 예제

```jsx
const [products, setProducts] = useState<Product[]>([]);

useEffect(() => {
	const fetchProducts = async () => {
		const url = 'http://localhost:3000/products';
		const response = await fetch(url);
		const data = await response.json();
		setProducts(data.products);
	};

	fetchProducts();
}, []);
```
Fetch 함수의 위치가 고민된다면, Dan Abramov의 글을 다시 보자.

- [useEffect 완벽가이드](https://overreacted.io/a-complete-guide-to-useeffect/)

#### 3. Effect가 두 번 실행되는 문제

<React.StrictMode>로 컴포넌트 전체를 감쌀 경우, 예상치 못한 Side Effect를 찾으려고 Effect 등을 두 번씩 실행함. 평소에는 큰 문제가 없지만, API 등을 사용하면 이상하다고 느낄 수 있으니 참고할 것.

- [예상치 못한 부작용 검사](https://ko.reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects)

### 의존성 배열을 이용해 Fetch할 때 주의사항

- [Fetching data](https://beta.reactjs.org/learn/synchronizing-with-effects#fetching-data)


---

### 참고 링크
- [React Conf 2018 Hooks 소개 영상](https://youtu.be/dpw9EHDh2bM)
- [HoC (Higher-Order Components)](https://ko.reactjs.org/docs/higher-order-components.html)
- [Synchronizing with Effects](https://beta.reactjs.org/learn/synchronizing-with-effects)
- [You Might Not Need an Effect](https://beta.reactjs.org/learn/you-might-not-need-an-effect)
- [Using the Effect Hook](https://ko.reactjs.org/docs/hooks-effect.html)
- [useEffect 완벽 가이드](https://overreacted.io/a-complete-guide-to-useeffect/)
