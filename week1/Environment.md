## 목표
### - 개발 환경 설정

- Node.js
- NPM(Node Package Manager)
  - package.json / package-lock.json
  - node_modules
  - npx
- ES Modules vs CommonJS

---

## 강의 내용
### 1. JavaScript 개발 환경 (Node.js) 설정
  
- nvm 보다는 최근에 fnm을 쓴다. (Rust로 만들어져서 빠르다고 한다.)  

- node.js 페이지에서 버전 확인 가능. [node](https://nodejs.org/en)
  - 버전 정보
  - LTS (Long Term Support)
  - 노드 버전 앞이 홀수인 것은 최신버전, 짝수인 것은 LTS 버전

### 2. TypeScript + React + Jest + ESLint + Parcel 설정


#### 작업 폴더 준비

```
- mkdir my-all
- cd my-app
```

#### 편집기 열기
```
- code . (VSCode)
- webstorm . (웹스톰)
```

#### npm 패키지 준비
```
- npm init
- npm init -y (한번에 package.json 생성)
```
- name은 kebab-case, Lisp-case

#### .gitignore 세팅
```
- touch .gitignore
```
- /node_modules/
- /dist/
- github/gitignore에서 찾아 사용.
    - [ex) node](https://github.com/github/gitignore/blob/main/Node.gitignore)

#### 타입스크립트 설정
```
- npm i -D typescript
```
- -D 옵션은 package.json의 devDependencies에 설치. 
- --save-dev와 같다.
```
- npx tsc --init
```
- node_modules/.bin/tsc --init 명령어와 같다.
- npx는 node_modules에 해당하는 패키지가 설치되어 있으면 찾아서 실행하고, 만약 설치되어 있지 않더라도 npm 패키지들을 캐시하는 곳에 다운로드를 받아서 설치하지 않아도 사용할 수 있도록 해준다.
    - macOS의 경우에 ~/.npm/\_npx 에 존재한다.
- "jsx" : "react-jsx" 설정을 맞춰준다.
    - .tsx 파일을 사용하게 해준다.
    - import React를 하지 않아도 사용할 수 있게 해준다.

#### ESLint 설정

```
- npm i -D eslint
- npx eslint --init
```
- env에 jest:true를 미리 잡아줄 것.
- .eslintignore 작성
- React import 없이 사용할 수 있게 해줄 것. (plugin:react/jsx-runtime)
- eslint --fix 명령어로 자동으로 수정해줄 수 있다. -> npm run lint로 설정

#### 리액트 설치
```
- npm i react react-dom
- npm i -D @types/react @types/react-dom
```

#### 테스팅 도구 설치
```
- npm i -D jest @types/jest @swc/core @swc/jest \
  jest-environment-jsdom \
  @testing-library/react @testing-library/jest-dom
```

####  jest.config.js 설정

- 성능을 위해 테스트에서 SWC를 사용할 수 있도록 세팅

#### Parcel 설치
```
- npm i -D parcel
- package.json scripts 수정
```


### 참고 링크
- [jest.config.js](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/jest.config.js)
- [package.json](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/package.json)
