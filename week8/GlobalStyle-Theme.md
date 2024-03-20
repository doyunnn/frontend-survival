## 목표
### - Global Style & Theme

- Global Style
- Theme

---

## 내용
### 1. Global Style

> [createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle)

> [CSS box model](https://developer.mozilla.org/ko/docs/Learn/CSS/Building_blocks/The_box_model#대체_css_box_model)

> [box-sizing](https://developer.mozilla.org/ko/docs/Web/CSS/box-sizing)

> [The 62.5% Font Size Trick](https://www.aleksandrhovhannisyan.com/blog/62-5-percent-font-size-trick/) 

> [keep-all-villain](https://megaptera.notion.site/keep-all-villain-923a4b6f23eb45b99aef4d8bc0865f9c?pvs=4)

> [word-break](https://developer.mozilla.org/ko/docs/Web/CSS/word-break)


#### 전역 스타일 지정.

```tsx
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  html {
    box-sizing: border-box;
  }

  *,
  *::before,
  *::after {
    box-sizing: inherit;
  }

  html {
    font-size: 62.5%;
  }

  body {
    font-size: 1.6rem;
  }

  :lang(ko) {
    h1, h2, h3 {
      word-break: keep-all;
    }
  }
`;

export default GlobalStyle;
```

```tsx
import { Reset } from 'styled-reset';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
  return (
    <>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </>
  );
}
```


### 2. Theme

> [Theming](https://styled-components.com/docs/advanced#theming)

> [Create a declarations file](https://styled-components.com/docs/api#create-a-declarations-file)

- 디자인 시스템의 근간을 마련하는데 활용.
- 잘 정의하면 다크 모드 등에 대응하기 쉬움.
- 눈에 보이는 단편적인 정보를 넘어서, “의미”에 집중할 수 있게 됨.

```tsx
const defaultTheme = {
  colors: {
    background: '#FFF',
    text: '#000',
    primary: '#F00',
    secondary: '#00F',
  },
};

export default defaultTheme;
```

```tsx
import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
  return (
    <ThemeProvider theme={defaultTheme}>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </ThemeProvider>
  );
}
```

```tsx
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  body {
    background: ${(props) => props.theme.colors.background};
    color: ${(props) => props.theme.colors.text};
  }

  a {
    color: ${(props) => props.theme.colors.text};
  }

  button,
  input,
  select,
  textarea {
    background: ${(props) => props.theme.colors.background};
    color: ${(props) => props.theme.colors.text};
  }
`;

export default GlobalStyle;
```

```tsx
import 'styled-components';

declare module 'styled-components' {
  export interface DefaultTheme extends Theme {
    colors: {
      background: string;
      text: string;
      primary: string;
      secondary: string;
    }
  }
}
```

```tsx
import defaultTheme from './defaultTheme';

type Theme = typeof defaultTheme;

export default Theme;
```

```tsx
import 'styled-components';

import Theme from './Theme';

declare module 'styled-components' {
  export interface DefaultTheme extends Theme {}
}
```
- 타입 문제를 해결하기 위해 styled.d.ts 파일을 작성.
```tsx
import Theme from './Theme';

const darkTheme: Theme = {
  colors: {
    background: '#000',
    text: '#FFF',
    primary: '#F00',
    secondary: '#00F',
  },
};

export default darkTheme;
```
- 다른 theme을 추가할 때 Theme 타입을 사용. 항상 defaultTheme에 먼저 항목을 추가/삭제하고, 나머지를 여기에 맞추면 된다.
```tsx
import { useDarkMode } from 'usehooks-ts';

import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';
import darkTheme from './styles/darkTheme';

import GlobalStyle from './styles/GlobalStyle';

import Greeting from './components/Greeting';
import Button from './components/Button';

export default function App() {
  const { isDarkMode, toggle } = useDarkMode();

  const theme = isDarkMode ? darkTheme : defaultTheme;

  return (
    <ThemeProvider theme={theme}>
      <Reset />
      <GlobalStyle />
      <Greeting />
      <Button onClick={toggle}>
        Dark Theme Toggle
      </Button>
    </ThemeProvider>
  );
}
```


### Jest 테스트 쪽에서 “window.matchMedia” 문제 발생.
- src/setupTests.ts 파일에 공식 문서에 나온 코드를 넣으면 해결된다.
- [Mocking methods which are not implemented in JSDOM](https://jestjs.io/docs/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)

```ts
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(), // deprecated
    removeListener: jest.fn(), // deprecated
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});
```

### 참고
[VSCode Theme Color](https://code.visualstudio.com/api/references/theme-color)
[Bootstrap Theme Color](https://getbootstrap.com/docs/5.3/customize/color/)