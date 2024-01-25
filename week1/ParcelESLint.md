## 목표
### - Parcel과 ESLint를 이용하여 프로젝트 환경을 구성한다.

- Bundler(번들러)
    - Parcel
- Lint(린트)
    - ESLint

---

## 강의 내용
### 1. Parcel


#### **Zero Configuration**

- 특별한 설정 없이 바로 사용 가능한 빌드 도구. 내부적으로 SWC를 사용해 기존 도구보다 빠르다(ES Module을 적극 활용하는 Vite도 엄청나게 빠름).

```shell
npx parcel index.html
npx parcel index.html --port 8080
```

- 설정이 필요 없다고 했지만, 다음 두가지 작업은 하는 게 좋다.

`package.json` 파일에 source 속성 추가.

```json
"source": "./index.html",
-> npx parcel --port 8080
```

- [parcel-reporter-static-files-copy](https://github.com/elwin013/parcel-reporter-static-files-copy) 패키지 설치 후 `.parcelrc` 파일 작성.   
- 이렇게 하면 static 폴더의 파일을 정적 파일로 Serving할 수 있다(이미지 등 Assets).

```shell
touch .parcelrc
```
```json
{
  "extends": ["@parcel/config-default"],
  "reporters":  ["...", "parcel-reporter-static-files-copy"]
}
```

- 빌드 + 정적 서버 실행

```bash
npx parcel build

npx servor ./dist
```
- dist 파일 지우고 start 
- [servor](https://github.com/lukejacksonn/servor)

### 2. ESLint

#### 무엇을 위해서?

- 스타일 통일 (일관성 있는)
- 잠재적 문제 발견
- 베스트 프랙티스 추천 → 최신 트렌드를 학습하는데 활용할 수 있다.
- 신기능 사용 → 최신 트렌드를 학습하는데 활용할 수 있다.

`.vscode/settings.json`파일을 추가해 JS/TS 파일을 저장할 때마다 ESLint를 실행하고 문제점을 고치게 설정할 수 있다.

```json
{
  "editor.rulers": [
    80
  ],
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "trailing-spaces.trimOnSave": true
}
```

```shell
npm run lint && npm run check 
```

### 참고 링크
- 🚀 [**Parcel 공식문서**](https://parceljs.org/)
- [parcel](https://github.com/ahastudio/til/tree/main/parcel)
- [vite](https://github.com/ahastudio/til/tree/main/vite)
- 🚀 [**ESLint 공식문서**](https://eslint.org/)
- [린트](https://ko.wikipedia.org/wiki/린트_(소프트웨어))
- [정적 프로그램 분석](https://ko.wikipedia.org/wiki/정적_프로그램_분석)
- [Coding_conventions](https://en.wikipedia.org/wiki/Coding_conventions)
- [VS Code ESLint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
- [VS Code 기본 세팅](https://github.com/ahastudio/CodingLife/blob/main/20211008/react/.vscode/settings.json)
- [Trailing Spaces](https://marketplace.visualstudio.com/items?itemName=shardulm94.trailing-spaces)