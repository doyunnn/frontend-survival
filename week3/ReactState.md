## 목표
### - React State

- React의 state
  - DRY
  - SSOT
- React State의 조건
- useState
- Lifting State Up
- 1급 객체(first-class object)란?

---

## 내용

### React로 사고하기
> [Thinking in React](https://react.dev/learn/thinking-in-react)

- Step 3: Find the minimal but complete representation of UI state
- Step 4: Identify where your state should live
- Step 5: Add inverse data flow

#### React의 State

React의 state는 “변경”을 다루기 위한 요소. “Re-rendering”이란 주제에서 다뤄진다.
거칠게 이야기하면, 어떤 컴포넌트의 state가 바뀌면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하게 된다.

아무렇게나 막 만들어도 되지만, 일관성과 효율을 위해 DRY 원칙을 따르는 SSOT를 만든다.

- [DRY (Don’t Repeat Yourself)](https://ko.wikipedia.org/wiki/중복배제)
- [SSOT (Single Source of Truth)](https://ko.wikipedia.org/wiki/단일_진실_공급원)

ex)
```tsx
import { useState } from 'react';
const [filterText, setFilterText] = useState('');
const [inStockOnly, setInStockOnly] = useState(false);
```

#### React State의 조건:

1. 변경돼야 함. 변경되지 않는 건 state로 다룰 가치가 없다.
2. 부모 컴포넌트가 props를 통해 전달한다면 state가 아님.
3. 다른 state나 props를 이용해 계산 가능하다면 state가 아님.

- 다루는 상태가 너무 많으면 복잡함.
ex)
```tsx
   <div>
      <SearchBar 
        filterText={filterText} 
        inStockOnly={inStockOnly} 
        onFilterTextChange={setFilterText} 
        onInStockOnlyChange={setInStockOnly} />
      <ProductTable 
        products={products} 
        filterText={filterText}
        inStockOnly={inStockOnly} />
    </div>
```
- TypeScript를 잘 쓰면 직접 관리하는 상태의 수를 줄여줄 수 있다. 
- 객체로 만들어서 여러개를 넘길 필요 없이 store를 만들어서 하나를 넘기면 된다. 
  - ex) external store

#### 상태를 어떻게 관리해야 하는가?
- (React만 쓴다면) 해당 상태에 의존적인 컴포넌트를 모두 포함하는 컴포넌트가 상태를 소유해야 한다.
- 사용하는 컴포넌트의 교집합이 되는 부모 컴포넌트를 찾아야 한다. 
- 여러 컴포넌트에서 props로 중복되는 값을 받게 되면 중복되는 값 자체를 타입으로 분리하거나, 아니면 구현부를 타입으로 분리해주면 좋다.
- [“Lifting State Up”](https://ko.reactjs.org/docs/lifting-state-up.html)
- [Sharing State Between Components](https://beta.reactjs.org/learn/sharing-state-between-components)

#### External Store
 
- State가 많을수록 복잡해진다. 이를 해결하기 위해 사용.
- 리액트가 아닌곳에서 상태를 관리하려고 하는 것이 External Store이다.



#### Inverse Data Flow

하위 컴포넌트의 props로 함수를 전달. 흔히 콜백 함수라고 부름.

TypeScript(정확히는 JavaScript)는 함수가 일급(first-class) 객체다. 즉, 어떤 함수를 다른 함수에 인자로 넘겨주거나, 어떤 함수를 리턴값으로 사용할 수 있다. 익명 함수, 클로저 등과 함께 사용하면 시너지가 큼.

- [일급 함수](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)
- 다시 [“Lifting State Up”](https://ko.reactjs.org/docs/lifting-state-up.html)

