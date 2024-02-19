## 목표
### - JSX

- Express 란
- URL 구조
- REST API
- HTTP Method(CRUD)

---

## 내용
### 1. Express 란
- Node.js를 위한 웹 프레임워크
- 빠르고, 간편하게 웹 서버를 만들 수 있음

Express 설치
```bash
ts-node
mkdir express-demo-app
cd mkdir express-demo-app
```

잊지 말고 .gitignore 파일 준비!
```bash
touch .gitignore
echo "/node_modules/" > .gitignore
```

패키지 초기화
```bash
npm init -y
```

TypeScript
```bash
npm i -D typescript
npx tsc --init
```

ESLint
```bash
npm i -D eslint
npx eslint --init
```

ts-node 설치
```bash
npm i -D ts-node
```

Express 설치
```bash
npm i express
npm i -D @types/express
```

Hello World 예제
Express 예제
TypeScript에 맞춰서 app.ts 파일 작성.
```javascript
import express from 'express';

const port = 3000;

const app = express();

app.get('/', (req, res) => {
res.send('Hello, world!');
});

app.listen(port, () => {
console.log(`Server running at http://localhost:${port}`);
});
```

ts-node로 실행.
```bash
npx ts-node app.ts
npm start # package.json에 추가
```

코드를 수정할 때마다 서버를 재실행해야 하는 문제를 피하기 위해 nodemon 사용.
```bash
npm i -D nodemon
npx nodemon app.ts
```

### 2. URL 구조
localhost:3000을 주소창에 입력했을 때 완전한 형태는 <http://localhost:3000/>.
localhost는 127.0.0.1 (자기 자신을 가리키는 주소)


### 3. REST API

> Roy Fielding - “[Architectural Styles and the Design of Network-based Software Architectures](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)” (2000)

> [Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html)

대개는 "필딩 제약 조건" 4가지를 모두 만족하지 않고, Resource와 HTTP Verb만 도입하는 수준으로 사용.

- /write-post 와 같이 작성하지 않고
- /posts 처럼 작성한다. (CRUD)

CRUD에 대해 HTTP Method를 대입한다. Read는 Collection(복수)과 Item(Element)(단수)로 나뉜다.

기본 리소스 URL: /products

1. Read (Collection) -> GET /products => 상품 목록 확인
2. Read (Item) -> GET /products/{id} => 특정 상품 정보 확인
3. Create (Collection Pattern 활용) -> POST /products => 상품 추가 (JSON 정보 함께 전달)
4. Update (Item) -> PUT 또는 PATCH /products/{id} => 특정 상품 정보 변경 (JSON 정보 함께 전달)
5. Delete (Item) -> DELETE /products/{id} => 특정 상품 삭제

- GET은 request body에 값을 넣어줄 수 없다.
- PUT은 overwrite, 덮어쓰는 느낌. 없으면 추가하고, 있으면 덮어씀.
- PATCH는 일부만 수정하는 업데이트를 하는 느낌.
- DELETE는 DB의 row를 hard delete하지 않고 soft delete를 할 때도 DELETE Method를 사용한다.
  - 실제로는 row의 특정 컬럼값을 변경하는 것이지만, 리소스 입장에서 볼 때는 삭제되는 것이기 때문에 DELETE Method를 사용한다.

Thinking in React 예제

```ts
app.get('/products', (req, res) => {
  const products = [
    {
      category: 'Fruits',
      price: '$1',
      stocked: true,
      name: 'Apple',
    },
    {
      category: 'Fruits',
      price: '$1',
      stocked: true,
      name: 'Dragonfruit',
    },
    {
      category: 'Fruits',
      price: '$2',
      stocked: false,
      name: 'Passionfruit',
    },
    {
      category: 'Vegetables',
      price: '$2',
      stocked: true,
      name: 'Spinach',
    },
    {
      category: 'Vegetables',
      price: '$4',
      stocked: false,
      name: 'Pumpkin',
    },
    {
      category: 'Vegetables',
      price: '$1',
      stocked: true,
      name: 'Peas',
    },
  ];

  res.send({ products });
});
```

- res.({ products }); 안에 메타 정보들을 같이 줄 경우도 있다. (ex. total, limit, offset, next, previous)
  - 때문에 Collection의 경우에는 JSON Object로 응답을 보내는 것이 좋다.