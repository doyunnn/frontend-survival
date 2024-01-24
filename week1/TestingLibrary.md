## 목표
### - 테스트 코드 작성 방법을 익힌다.

- Jest
- Describe-Context-It 패턴
- React Testing Library

---

## 강의 내용
### 1. Jest
- 거의 모든 것을 갖춘 테스팅 도구.
- Mocha와 Chai처럼 RSpec의 describe-it을 지원하고, expect로 단언(assertion)할 수 있다. Mocking도 다양한 레벨에서 쉽게 사용할 수 있다.



#### 기본적인 테스트 코드
```shell
touch src/main.test.ts
npm test
npx jest --watchAll -> watch:test
```

```jsx
function add(a: number, b: number): number {
  return a + b;
}

test('add', () => {
  expect(add(1, 2)).toBe(3);
});
```
- 테스트를 먼저 만들고 구현부를 만들어 하나씩 테스트를 통과시키는 코드를 작성하는 방식을 TDD(Test Driven Development)라고 한다.

#### BDD 스타일의 테스트 코드

```jsx
describe('add 함수', () => {
  it('returns sum of two numbers', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

```jsx
const context = describe;
describe('add 함수', () => {
    context('하나의 양수와 음수가 주어지면', () => {
        it('항상 하나의 양수보다 작은 값을 돌려준다.', () => {
            expect(add(1, -2)).toBeLessThan(1);
        });
    });
});
```
- Describe-Context-It 패턴
- 표현력이 좋아지고, 좀 더 고민할 기회를 제공.
  - add 함수는 두 숫자의 합을 반환한다.
- 할인 쿠폰과 같은 종류의 복잡한 코드를 테스트할 때 용이.

### 2. React Testing Library

- UI 테스트에 특화된 라이브러리. 거의 E2E Test처럼 쓸 수 있다.
- 단, “F/E 테스트 = only React 컴포넌트 테스트”가 되는 상황은 최대한 피하는 게 좋다. 본질에 집중하지 못하고 너무 많은 테스트 코드를 작성할 위험이 있다. 유지보수를 돕기 위해 테스트 코드를 작성하는데, 테스트 코드를 잘못 작성하면 오히려 유지보수를 저해할 수 있다.

### 간단한 테스트 코드

```jsx
import {render, screen} from '@testing-library/react';
test('Greeting', () => {
  render(<Greeting name="world" />);

  screen.getByText('Hello, world!');

  screen.getByText(/Hello/);
  
  expect(screen.queryByText(/Hello/)).toBeInTheDocument();
  expect(screen.queryByText(/Hi/)).not.toBeInTheDocument();
});
```
- getByText: 해당 텍스트를 가진 엘리먼트를 찾는다. 없으면 에러를 발생시킨다.
- toBeInTheDocument: 해당 엘리먼트가 존재하는지 확인한다. (존재를 기대)

### 참고 링크
- 🚀 [**Jest 공식문서**](https://jestjs.io/)
- [Given-When-Then](https://www.notion.so/Given-When-Then-deee38000805476fa2ab10adc20424ed?pvs=21)
- 🚀 [**React Testing Library 공식문서**](https://testing-library.com/docs/react-testing-library/intro)
- [**jest-dom**](https://testing-library.com/docs/ecosystem-jest-dom/)
- [프론트엔드(Front-end)도 테스트해야 하나요?](https://youtu.be/-kUmsKRmOnA)
- [Mocking 때문에 테스트 코드를 작성하기 어렵나요](https://youtu.be/RoQtNLl-Wko)