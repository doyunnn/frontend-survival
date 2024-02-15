## 목표
### - React Component

- REST API 와 GraphQL
- JSON
- 선언형 프로그래밍
- 명령형 프로그래밍
- React component
  - SRP(단일 책임 원칙)
  - Atomic Design
- props

---

## 내용
### React로 사고하기
> [Thinking in React](https://react.dev/learn/thinking-in-react)
- “Step 1: Break the UI into a component hierarchy”
- “Step 2: Build a static version in React”

#### 데이터를 어떻게 가져올 것인가?

- B/E에서 JSON 형태의 데이터를 돌려주는 API를 제공한다고 가정(대부분은 REST API 또는 GraphQL).

**REST API**

- GET, POST, PUT/PATCH, DELETE (CRUD)
- Resource 중심

**GraphQL**

- Graph 자료 구조
- Query에서 얻고자 하는 걸 지정
- Query(Read), Mutation(Command: Create, Update, Delete), Subscription(Event)

- [JSON](https://ko.wikipedia.org/wiki/JSON)
- [JSON 개요](https://www.json.org/json-ko.html)
- [JSON으로 작업하기](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/JSON)

- JSON은 JavaScript Object Notation의 약자로, 데이터를 교환하는 형식으로 사용된다.
  - JSON.stringify -> JSON 문자열로 변환.
  - JSON.parse -> 문자열을 자바스크립트 객체로 변환.

#### 데이터를 어떻게 보여줄 것인가?

- F/E는 위 데이터를 사용자가 볼 수 있도록 UI를 구성한다.
- React는 선언형(HTML과 유사한 모양의 DSL을 사용)으로 UI를 구성할 수 있다. 정확하게는 XML이다.
- DSL은 도메인 특화 언어라고 해서 특정 도메인의 문제를 풀기위해 사용하는 언어.

- [명령형 프로그래밍](https://ko.wikipedia.org/wiki/명령형_프로그래밍)
- [선언형 프로그래밍](https://ko.wikipedia.org/wiki/선언형_프로그래밍)

- Json으로 데이터를 받아서, React로 UI를 구성하는 것이 목표.
- React는 선언형 프로그래밍을 지향한다.

### 컴포넌트 계층 구조
#### 리엑트의 특징
1. Declarative(선언적)
2. Component-Based(컴포넌트 기반)
3. Learn Once, Write Anywhere(한번 배워서 어디에나 쓰자)

#### 컴포넌트를 나누는 기준
- 객체지향에서 말하는 5가지 원칙, SOLID

- SRP
    - 모든 클래스는 하나의 책임만 가지며 캡슐화해야한다. (캡슐화된 컴포넌트)
    - 하나의 컴포넌트가 변경되는 이유는 단 하나여야 한다.
    - 컴포넌트를 봤는데 하는 일도 너무 많고, 변경이 생길 때 많은 부분을 고쳐야 하면 컴포넌트를 쪼개야 한다.
    - 컴포넌트는 html이 길어질수도 있고, 자바스크립트 코드가 많아질수도 있다.
    - 자바스크립트 코드가 많아지는 경우는 다른 방법을 알아볼 것. 아마 커스텀 훅을 얘기하시는 것 같다.
    - 리액트 컴포넌트는 view만 담당하는게 역할에 맞다고 생각하기 때문에, 커스텀 훅을 적극적으로 사용하는 것이 좋다고 생각한다.
- CSS
    - html 코드가 많아지는 경우에는 css를 위한 class를 잘 활용해야 한다.
    - css를 만들어야겠다고 생각할 때 우리는 머리속으로 컴포넌트를 쪼갠다.
    - 알게 모르게 css를 작성할 때도 컴포넌트를 분리하고 있던 것이다.
- Design's Layer
    - 디자인을 만들때부터 레이어를 나눠놓고 있다. 이것도 그대로 컴포넌트 구조로 활용할 수 있다.
    - 리액트 공식문서에서 hierarchy를 만드는 과정은 아샬님은 디자인에 가깝다고 하셨다.
- Information Architecture (JSON Schema의 영향)
    - JSON 구조가 체계적으로 잘 되어 있다면 JSON 구조를 활용할 수도 있다.
    - JSON 구조를 그대로 보여주기에 조금 애매하다면 프론트 단에서 가공을 해서 화면에 뿌려줄 수도 있고, 백엔드에 요청해서 데이터를 변환해달라고 할 수도 있다.
    - 실제로 아샬님은 이 방법을 엄청 많이 쓰게 된다, 만약 데이터 구조가 좋지 않으면 백엔드에서 수정하는 편이라고 하셨다.
    - 뭔가 BFF가 생각나기도 했다. BFF가 이러한 역할도 할 수 있을 것 같다.
    - Redux를 빡세게 사용하는 경우에는 Normalize를 많이 하게 된다. 데이터 오는 것들을 어떻게 할지, 캐시 관련된 것들을 어떻게 할지. gql은 캐싱을 알아서 해준다.
    - 자연스러운 SRP가 된다, 사실상 강제가 된다. product 하나를 보여주는 컴포넌트를 만들게 된다.

- 작은 컴포넌트=부품을 만들어서 조립. 조합은 가지수를 폭발적으로 늘릴 수 있는 가장 전형적인 방법.
- [Atomic Design](https://bradfrost.com/blog/post/atomic-web-design/)은 우리가 잘 알고 있는 계층형 구조를 몇 가지 카테고리로 묶은 방법.
>_ATOMS -> MOLECULES -> ORGANISMS -> TEMPLATES -> PAGES_

#### Props

> [Passing Props to a Component](https://beta.reactjs.org/learn/passing-props-to-a-component)
> 
> [Components와 Props](https://ko.reactjs.org/docs/components-and-props.html)
> 
- 나눠진 컴포넌트를 서로 연결하는 방법.
- TypeScript를 잘 쓰거나 잘못 쓰게 되는 포인트 중 하나. 적절한 균형점을 잡는 게 중요하다.
- 테스트 코드를 작성하면 재사용성을 평가하기 쉬워짐.


