## 목표
### - CSS in JS

- CSS in JS
- 전통적인 CSS 방식의 문제점
- 성능 이슈

---

## 내용
### 1. CSS in JS

- CSS-in-JS란 자바스크립트를 사용하여 컴포넌트를 스타일링하는 기술.
- 자바스크립트가 파싱될 때, CSS가 style 태그에 생성이 되고, DOM에 적용이 된다. 자바스크립트를 사용하여 선언적이고 유지가능한 방식으로 스타일링을 할 수 있고,  컴포넌트 레벨에서의 CSS 추상화를 가능하게 한다. 이와 같은 컨셉으로 구현되어 있는 라이브러리들이 많이 존재한다.

- Emotion
- Styled Components
- JSS
- Tailwind CSS

위와 같은 라이브러리들은 tagged template literals 문법을 사용하여 스타일 컴포넌트를 만들 수 있게 한다.

```tsx
import styled from 'styled-components';
// Create a component that renders a <p> element with blue text
const BlueText = styled.p`
  color: blue;
`;

<BlueText>My blue text</BlueText>
```

- 기존의 전통적인 CSS 방식과는 다르게 CSS-in-JS로는 스타일을 동적으로 만들 수 있다. 
- 또한 CSS를 캡슐화하여 모듈화된 코드를 작성할 수 있다.

### 전통적인 CSS 방식의 문제점

1. Global Namespace
2. Dependencies
3. Dead Code Elimination
4. Minification
5. Sharing Constants
6. Non-deterministic Resolution
7. Isolation

[A Unified Styling Language (2017)](https://blog.rhostem.com/posts/2017-06-24-unified-styling-language)

1. Scoped styles
2. Critical CSS
3. Smarter optimisations
4. Package management
5. Non-browser styling


[All You Need To Know About CSS-in-JS (2017)](https://d0gf00t.tistory.com/22)

1. Thinking in components
2. CSS-in-JS **leverages the full power of the JavaScript ecosystem** to enhance CSS.
3. True rules isolation
4. Scoped selectors
5. Vendor Prefixing
6. Code sharing
7. **Only the styles which are currently** in use on your screen are also in the DOM (react-jss).
8. Dead code elimination
9. Unit tests for CSS

[Most popular CSS-in-JS libraries](https://npmtrends.com/aphrodite-vs-emotion-vs-glamorous-vs-jss-vs-radium-vs-styled-components-vs-styletron)
- styled-components, JSS, Emotion 순으로 사용량이 많다.

### 성능 이슈

HTML, CSS 파일은 병렬적으로 로딩이 가능하지만, JS 파일의 경우에는 모두 다운로드 되지 않으면 실행을 할 수 없다.  
CSS-in-JS 같은 경우에는 자바스크립트에 CSS를 작성하기 때문에 특히 리액트의 경우에는 SSR, Code Splitting이 적용되지 않는다면 번들의 크기가 거대해진다.

대안:

- [Linaria](https://linaria.dev/)
  - CSS를 평범한 텍스트로 작성.
  - React에 종속적이지 않지만, React Styled Component도 지원함.
- [vanilla-extract](https://vanilla-extract.style/)
  - CSS를 오브젝트 형태로 표현. React의 Inline Style과 유사함.
  - React와 무관하게 사용 가능.
