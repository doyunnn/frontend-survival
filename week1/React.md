## 목표
### - React란?

- React 컴포넌트
- React 리렌더링
- IoC(Inversion of Control)
- Library vs Framework

---

## 강의 내용
### 1. React

#### 렌더링

- [createRoot](https://beta.reactjs.org/reference/react-dom/client/createRoot)
    - 한국어로 읽고 싶다면 [예전 문서](https://ko.reactjs.org/docs/react-dom-client.html#createroot)를 아주 살짝 참고하자.

```tsx
function Greeting() {
  return (
    <p>Hello, world!</p>
  );
}

function main() {
  const element = document.getElementById('root');

  if (!element) {
    return;
  }

  const root = ReactDOM.createRoot(element);

  root.render(<Greeting />);
}

main();
```

```tsx
function Demo({count} : {count: number}) {
  return (
          <p>Demo {count}</p>
  );
}
function Greeting() {
  return (
          <p>Hello, world!</p>
  );
}

function main() {
  const element = document.getElementById('root');
  const element2 = document.getElementById('demo');

  if (!element || !element2) {
    return;
  }

  const root = ReactDOM.createRoot(element);
  const root2 = ReactDOM.createRoot(element2);

  root.render(<Greeting />);

  let i = 0;
  setInterval(() => {
    root.render(<Demo count={i} />);
    i++;
  }, 1_000);
}

main();
```
- 여러 개의 Root를 만들거나, 여러 번 render해도 된다.
- 여러 번 render하는 카운터 예제. 변경된 부분만 업데이트하기 때문에 하단 입력 컨트롤에 입력한 값이 그대로 유지된다.


#### 리렌더링

```tsx
export default function App() {
  const [count, setCount] = useState(0);
    const handleClick = () => {
        setCount(count + 1);
    }
    return (
        <div>
            <p>Hello, wolrd!</p>
            <p>Count: {count}</p>
            <button type={'button'} onClick={handleClick}>클릭+</button>
        </div>
    );
}

```
- State가 바뀔 때 리렌더링 
- 효율적으로 상태를 관리해주는 것이 핵심
- 각 컴포넌트의 영향에 따라 리렌더링이 다르게 진행


### 참고 링크
-  [**🚀 React 공식문서**](https://react.dev)
- React로 작업하는 프로세스는 [Thinking in React](https://beta.reactjs.org/learn/thinking-in-react)를 참고. “상태”를 골라내는 게 핵심이다.
- 한국어로 읽고 싶다면 [예전 문서의 설명](https://ko.reactjs.org/docs/thinking-in-react.html)만 살짝 참고하자(코드는 참고하지 말 것!).
- [React 코어 개발자가 쓴 React에 대한 이해를 돕는 글](https://overreacted.io/ko/react-as-a-ui-runtime/) (필독!)
- [updating-a-root-component](https://beta.reactjs.org/reference/react-dom/client/createRoot#updating-a-root-component)
- [React는 컴포넌트를 언제 다시 리렌더링 할까요?](https://velog.io/@surim014/react-rerender)
- [왜 리액트에서 리렌더링이 발생하는가.](https://medium.com/@yujso66/%EB%B2%88%EC%97%AD-%EC%99%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C-%EB%A6%AC%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%B4-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94%EA%B0%80-74dd239b0063)
- [React 렌더링 동작에 대한 (거의) 완벽한 가이드](https://velog.io/@superlipbalm/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior)