## 목표
### - 타입스크립트

- REPL
- TypeScript
    - Interface vs Type
    - 타입 추론
    - Union Type vs Intersection Type
    - Optional Parameter

---

## 강의 내용
### 1. TypeScript

#### 실행

```bash
npx ts-node
```
- 간단히 REPL을 쓰고 싶다면 ts-node를 실행하면 된다.

#### 타입 지정

- 선언된 타입에 타입이 맞는 값을 할당 해야 한다.
```tsx
let name: string;
let age: number;

name = '홍길동';
age = 13;

name = 13;
age = '홍길동';

let human: {
  name: string;
  age: number;
};

human = { name: '홍길동', age: 13 };
```

- 복잡한 오브젝트의 타입을 재사용하기 위해 타입을 정의할 수 있다.


```tsx
type Human = {
  name: string;
  age: number;
};

let boy: Human;

boy = { name: '홍길동', age: 13 };

interface Person {
  name: string;
  age: number;
};

let girl: Person;

girl = { name: '홍길동', age: 13 };

function add(x: number, y: number): number {
  return x + y;
}

add(1, 2)

add('Hello', 'World')

function sub(x: number, y: number): string {
  return x - y;
}
```

- 정해진 값으로 타입을 지정할 수도 있다. 이런 타입은 Union에서 유용하게 쓰인다.
  - 'food' 만 가능

```tsx
let category: 'food';

category = 'food';
```

- 배열은 타입 뒤에 대괄호를 붙여주면 된다.

```tsx
let numbers: number[];

numbers = [1, 2, 3];
```

- 배열보다 깐깐하게 타입을 관리하고 싶다면 Tuple을 쓴다.
  - 배열의 길이가 정해져 있고, 각각의 인덱스에 타입이 정해져 있다.

```tsx
let anythings: any[];

anythings = ['hp', 256];

let pair: [string, number];

pair = ['hp', 256];
```

#### [타입 추론](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html#%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0-types-by-inference)

```tsx
const name: string = '홍길동';

const name = '홍길동';
```
- 모든 것을 타입으로 지정할 필요는 없다.
- 그래도 다 검사한다.

#### [Union Type](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html#%EC%9C%A0%EB%8B%88%EC%96%B8-unions)

- 여러 타입 중 하나. 예를 들어 boolean은 true | false라고 볼 수 있다.
  - ex) myBool = true | false

```tsx
type bool = true | false;

let flag: bool;

flag = true;

flag = false;

flag = 3;
```

- 매개변수를 제한하거나 할 때 매우 유용하게 쓸 수 있다.

```tsx
type Category = 'food' | 'toy' | 'bag';

function fetchProducts({ category }: { category: Category }) {
  console.log(`Fetch ${category}`);
}
```

- 레거시 환경 또는 코드 때문에 안 쓸 수가 없다. ReactNode 같은 게 대표적이다.

- [React Types](https://github.com/facebook/react/blob/main/packages/shared/ReactTypes.js)

```tsx
let target: string | number;

target = '홍길동';

target = 13;

target = null;

target = undefined;

let targetName: string | undefined;

targetName = '홍길동';

targetName = undefined;
```

- [Optional Parameter](https://www.typescriptlang.org/docs/handbook/2/functions.html#optional-parameters)로 undefined를 허용할 수 있다. 

```tsx
function greeting(name?: string): string {
  return `Hello, ${name || 'world'}`;
}
```

- 기본값을 잡아주면 더 좋다.

```tsx
function greeting(name: string = 'world'): string {
  return `Hello, ${name}`;
}
```

- 매개변수가 오브젝트일 때도 활용할 수 있다.

```tsx
function greeting({ name, age }: {
  name: string;
  age?: number;  
}): string {
  return age ? `${name} (${age})` : name;
}
```

- 이렇게 쓰면 ts-node에선 아쉽게도 해석 불가하다.
  - 아래처럼 한 줄로 붙여서 쓰거나, type 등으로 따로 잡아주면 된다.

```tsx
function greeting({ name, age }: { name: string; age?: number; }): string {
  return age ? `${name} (${age})` : name;
}
```

```tsx
type Human = {
  name: string;
  age?: number;
};

function greeting({ name, age }: Human): string {
  return age ? `${name} (${age})` : name;
}

greeting()

greeting({ name: '홍길동' })

greeting({ name: '홍길동', age: 13 })
```

#### Intersection Type

- [교집합](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes-func.html#%EA%B5%90%EC%A7%91%ED%95%A9)
- [Intersection Types](https://www.typescriptlang.org/docs/handbook/2/objects.html#intersection-types)

- 타입을 확장하는 간단한 방법.

```tsx
type Human = {
  name: string;
  age: number;
};

type Creature = {
  hp: number;
  mp: number;
};

type Person = Human & Creature;

let person: Person;

person = { name: '홍길동', age: 13, hp: 256, mp: 16 };
```




### 참고 링크
- [Typescript Docs](https://www.typescriptlang.org/docs/)
- [Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
- [더 좋은 타입스크립트 프로그래머로 만드는 11가지 팁](https://velog.io/@lky5697/11-tips-that-help-you-become-a-better-typescript-programmer)

- [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)
- [DefinitelyTyped/types](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types)
- [DefinitelyTyped/types/react](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react)
  - → React는 여기서 확인할 수 있다. React 18이 바로 나오고(index.d.ts), 이전 버전은 하위 폴더(디렉터리)로 따로 관리되고 있음.