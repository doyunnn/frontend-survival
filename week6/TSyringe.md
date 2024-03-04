## 목표
### - TSyringe

- TSyringe
- 의존성 주입(Dependency Injection)
- reflect-metadata
- singleton (싱글톤)

---

## 내용
### 1. TSyringe

> [TSyringe](https://github.com/microsoft/tsyringe)
>

> [reflect-metadata](https://github.com/rbuckton/reflect-metadata)
>

> [The problem with passing props](https://beta.reactjs.org/learn/passing-data-deeply-with-context#the-problem-with-passing-props)
>

- TypeScript용 DI 도구(IoC Container).
- External Store를 관리하는데 활용할 수 있다.
- React 컴포넌트 입장에서는 “전역”처럼 여겨진다.
- “Prop Drilling” 문제를 우아하게 해결할 수 있는 방법 중 하나(React로 한정하면 Context도 쓸 수 있다).

의존성 설치

```bash
npm i tsyringe reflect-metadata
```

`src/main.tsx` 파일과 `src/setupTests.ts` 파일에서 reflect-metadata 임포트.

```tsx
import 'reflect-metadata';
```

싱글톤으로 관리할 CounterStore 클래스를 준비:

```tsx
import { singleton } from 'tsyringe';

type Listener = () => void;

@singleton()
class CounterStore {
    count = 0;
    
    listeners: Set<Listener> = new Set();
    
    increase() {
        this.count += 1;
        this.publish();
    }
    decrease() {
        this.count -= 1;
        this.publish();
    }
    
    publish() {
        this.listeners.forEach((listener) => listener());
    }
    
    addListener(listener: Listener) {
        this.listeners.add(listener);
    }
    
    reomveListener(listener: Listener) {
        this.listeners.delete(listener);
    }
   
}
```

싱글톤 CounterStore 객체를 사용:

```tsx
import { container } from 'tsyringe';

const counterStore = container.resolve(CounterStore);
```

테스트에서 TSyringe에서 관리하는 객체를 초기화할 수 있다.

```tsx
beforeEach(() => {
    container.clearInstances();
});
```

## 상태 변경 알림

- Store는 어떤 식으로든 action을 처리하고, 상태가 바뀌면 연결된 컴포넌트를 forceUpdate한다.

- 컴포넌트는 해당 Store에서 상태를 얻어서 UI를 업데이트하게 되는데, 선언형 UI가 얼마나 편한지 절실히 느낄 수 있다.

```tsx
import { container } from 'tsyringe';


export default function Counter() {
    const store = useCounterStore();
    // const counterStore = container.resolve(CounterStore);
    //
    // const forceUpdate = useForceUpdate();
    //
    // useEffect(() => {
    //     // counterStore.forceUpdate.add(forceUpdate);
    //     counterStore.addListener(forceUpdate);
    //     return () => counterStore.removeListener(forceUpdate);
    //     // return () => {
    //     //     counterStore.forceUpdate.delete(forceUpdate);
    //     // };
    // }, [counterStore, forceUpdate]);
    // hook을 사용해서 컴포넌트에서 Store를 사용할 수 있다.
    
    
    const handleClick = () => {
        // increase();
        // forceUpdate();
        
        store.increase();
    }
    return (
        <div>
            {/*<p>{state.count}</p>*/}
            <p>{store.count}</p>
            <button type="button" onClick={handleClick}>Increase</button>
        </div>
    );
}
```


hook을 사용해서 컴포넌트에서 Store를 사용할 수 있다.

```tsx
import { container } from 'tsyringe';
export default function useCounterStore() {
    const store = container.resolve(CounterStore);

    const forceUpdate = useForceUpdate();

    useEffect(() => {
        // counterStore.forceUpdate.add(forceUpdate);
        store.addListener(forceUpdate);
        return () => store.removeListener(forceUpdate);
        // return () => {
        //     counterStore.forceUpdate.delete(forceUpdate);
        // };
    }, [store, forceUpdate]);
    return store;
}

```