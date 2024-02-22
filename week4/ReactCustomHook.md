## 목표
### - useRef & Custom Hook

- useRef
- Custom Hook
- Hook의 규칙


---

## 내용
### 1. useRef

> [beta 문서의 useRef](https://beta.reactjs.org/reference/react/useRef)
>
> useRef - lets you reference a value that’s not needed for rendering.
> 
- 컴포넌트의 생애주기 전체에 걸쳐서 유지되는 객체. 즉, 컴포넌트가 없어질 때까지 동일한 객체가 유지된다.
- 객체 자체가 값은 아니고, 값을 참조하기 위한 객체. 즉, 언제든지 값을 변경할 수 있다.
- 상태(state)가 변경되면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하지만, 레퍼런스 객체의 현재 값(current)이 바뀌더라도 렌더링에 영향을 주지 않는다.
  - 렌더링 시에도 일관되게 유지되는 state와 달리, ref는 계산되는 시점에만 유효한 값이라고 볼 수 있다.

주요 용도:

1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우. ⇒ input 등의 ID 관리.
2. (특히 useEffect 등과 함께 쓰면서 만나게 되는) 비동기 상황에서 현재 값을 제대로 쓰고 싶은 경우.

Closure → 변수를 capture, bind를 깜빡하는 문제가 종종 일어남.

#### 보옽의 사용 에제

```tsx 
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```
#### 사용 주의 사항

```tsx 
function MyComponent() {
  // ...
  // 🚩 Don't write a ref during rendering
  myRef.current = 123;
  // ...
  // 🚩 Don't read a ref during rendering
  return <h1>{myOtherRef.current}</h1>;
}
```
```tsx 
function MyComponent() {
    // ...
    useEffect(() => {
        // ✅ You can read or write refs in effects
        myRef.current = 123;
    });
    // ...
    function handleClick() {
        // ✅ You can read or write refs in event handlers
        doSomething(myOtherRef.current);
    }
    // ...
}
```

#### 유의 사항

```tsx
const inputRef = useRef(null);

return <MyInput ref={inputRef} />;
```
- 다음과 같이 사용할 때 아래의 경고 메시지가 나타난다.
> ex) Warning: Function components cannot be given refs. Attempts to access this ref will fail. Did you mean to use React.forwardRef()?
>

- useRef 훅은 주로 컴포넌트 안에서 DOM 노드나 다른 값을 참조하는 데 사용된다.
- 이는 컴포넌트의 렌더링과는 별도로 값이 유지되어야 할 때 유용.
- 하지만 `useRef`를 통해 생성된 `ref` 객체는 일반적으로 컴포넌트 자체의 로컬 상태로 취급되며, React에서 컴포넌트 간의 데이터 전달에 주로 사용되는 props와는 별개로 동작.
- 따라서 `useRef`를 사용하여 생성된 `ref` 객체는 일반적으로 컴포넌트 내부에서만 사용되며, props를 통해 다른 컴포넌트로 전달되지 않는다.
- 만약 ref를 다른 컴포넌트로 전달하고 싶다면, `forwardRef`를 사용하여야 한다.


#### forwardRef

>   [forwardRef](https://react.dev/reference/react/forwardRef)
>

```tsx
// 두번째 파라미터로 'ref'를 참조할 수 있게 된다.
function ChildComponent(props, ref) {
  return <span ref={ref}>children ref 테스트</span>
}

// 'forwardRef'로 감싸기
const ForwardedChild = forwardRef(ChildComponent)

function ParentComponent() {
  const childRef = useRef(null);
  
  useEffect(() => {
    console.log(childRef.current?.textContent); // 'children ref 테스트'
  }, []);

  return (
    <div>
      <ForwardedChild ref={childRef} />
    </div>
  );
}

```

- 추가로 `useImperativeHandle` 전달된 `ref`를 DOM 노드가 아닌 컴포넌트 내부에서 커스텀해 사용할 수 있다.

#### useImperativeHandle

```tsx 
export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
    // This won't work because the DOM node isn't exposed:
    // ref.current.style.opacity = 0.5;
  }

  return (
    <form>
      <MyInput placeholder="Enter your name" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

```tsx
const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});

export default MyInput;
```

- React에서는 항상 선언형 컴포넌트를 작성하는 것이 좋다.
- 종종 선언적으로 props를 전달하는게 아니라, useImperativeHandle를 이용해서 자식 컴포넌트의 내부 함수를 호출하여 사용할 수 있다. 
- 클래스 컴포넌트 시절에 자주 사용하던 방식이기도 하고, 이 방식이 때론 복잡도를 낮춰주기도 하기 때문이다. 
- 그러나 명령형 함수는 사용하면 할수록 데이터의 흐름이 잘 보이지 않게 된다.
- props와 내부의 state만으로 컴포넌트의 상태를 제어하는 선언형과 달리, 외부에 노출한 커스텀 ref가 언제 어떻게 사용될지 예측하기 힘들기 때문이다.
- 따라서, ref의 커스터마이징은 DOM 객체를 제어하기 위한 함수 실행의 목적으로만 사용하는 것이 좋다.

### 2. Custom Hook

> [Reusing Logic with Custom Hooks](https://beta.reactjs.org/learn/reusing-logic-with-custom-hooks)
>

> [자신만의 Hook 만들기](https://ko.reactjs.org/docs/hooks-custom.html)
>

- 로직을 재사용하기 위한 제일 쉬운 방법.
- 평범하게 Extract Function을 수행하면 된다. 컴포넌트가 대문자로 시작하는 PascalCase로 이름을 붙인다면, Hook은 “use”로 시작하는 camelCase로 이름을 붙이면 된다.

```jsx
function useFetchProducts() {
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

	return products;
}
```

- 컴포넌트 코드도 명확해지고, setProducts가 실수로 잘못 쓰일 문제까지 해소됨.


#### useRef를 이용한 Custom Hook

```tsx
function useMount() {
  const ref = useRef(false);

  useEffect(() => {
    if (!ref.current) {
      ref.current = true;
    }
  }, []);

  return { isMount: ref.current }
}
```
- 마운트 여부를 확인하고 싶을 때 사용할 수 있다.
  - 예를 들어 최초 API 호출 시에는 스켈레톤 UI을 표시했다가 API 응답에 따라 목록이나 대체 UI를 표시하고자 하는 경우.


### 3. Hook 규칙

> [Hook의 규칙](https://ko.reactjs.org/docs/hooks-rules.html)
>

Hook 호출은 규칙이 있어서 단순하게 쓰도록 노력해야 한다.

1. Function Component 바로 안쪽(함수의 최상위)에서만 호출.
2. Function Component 또는 Custom Hook에서만 호출.

처음에는 콜백 함수나 조건문 안에서 Hook을 호출하는 실수를 저지르기 쉽다.

```jsx
if (playing) {
	const products = useFetchProducts();
	console.log(products);
}
```