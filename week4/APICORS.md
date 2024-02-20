## 목표
### - Fetch API & CORS

- Fetch API 란
- Promise
- CORS 란

---

## 내용
### 1. Fetch API

## Fetch API

> [Fetch API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)
>

> [Fetch 사용하기](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)
>

> [ReadableStream](https://developer.mozilla.org/ko/docs/Web/API/ReadableStream)
>

> [텍스트 디코더와 텍스트 인코더](https://ko.javascript.info/text-decoder)
>

**기본적인 사용법 실험**

```jsx
fetch('http://localhost:3000/products');
// → Promise

await fetch('http://localhost:3000/products');
// → Response

const response = await fetch('http://localhost:3000/products');
// → response.body는 ReadableStream

const reader = response.body.getReader();

const chunk = await reader.read();
// → chunk.value는 Uint8Array 타입.
// → 원래는 chunk.done이 true일 때까지 반복해야 한다.

const body = new TextDecoder().decode(chunk.value);

const data = JSON.parse(body);
```

실제로는 JSON을 기본적으로 지원.

```jsx
const response = await fetch('http://localhost:3000/products');
const data = await response.json();
```

다른 HTTP Method를 쓰고 싶다면?

```jsx
const response = fetch(url, {
	method: 'POST',
});
```

React에서 fetch 사용

```ts
const url = 'http://localhost:3000/products';
const response = await fetch(url);
const data = await response.json();
const { products } = data;
```

## CORS

> [동일 출처 정책](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)
>

> [CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
>

- 웹 브라우저는 Same Origin Policy에 따라 웹 페이지와 리소스를 요청한 곳(여기서는 REST API 서버)이 서로 다른 출처(포트까지 포함)일 때 서버에서 얻은 결과를 사용할 수 없게 막는다. 
- 주의할 점은 서버에 요청이 갔고, 서버가 응답을 해줬지만 브라우저에서 동일 출처가 아니기 때문에 막는 것이다.
- REST API 서버에서 Headers에 “Access-Control-Allow-Origin” 속성을 추가하면 된다.
  - Access-Control-Allow-Origin: * (모든 출처 허용)



#### 같은 출처인지 다른 출처인지 구분하는 조건 3가지.  
> Scheme, Host, Port
> 

#### CORS가 동작하는 방식은 세 가지.

1. Preflight Request
    1. 예비 요청과 본 요청을 나누어서 서버에 전송한다.
    2. OPTIONS 메소드를 이용하여 예비 요청을 보내게 되는데 이때 CORS 정책을 위반했는지 판단한다.
2. Simple Request
    1. 예비 요청을 보내지 않고 바로 서버에 요청을 보내는 방식이다.
    2. 특정 상황에서만 발생하고, 조건이 매우 까다롭다.
3. Credentialed Request
    1. 쿠키와 같은 인증 정보를 헤더에 포함하는 방식이다.
    2. credentials 옵션을 사용하는데, 이때 서버에서도 이에 맞는 설정을 해줘야 한다.

#### CORS를 해결을 위한 2가지 방법.

1. 먼저 서버에서 Access-Control-Allow-Origin 세팅을 해준다.  
   Node.js 기반 서버 프레임워크(Express, NestJS등)에서는 Cors 라이브러리를 이용한다.

2. 프론트단에서 프록시 서버를 둔다.


- Express에선 그냥 [CORS 미들웨어](https://expressjs.com/en/resources/middleware/cors.html)를 설치해서 사용하면 됨.

패키지 설치

```bash
npm i cors
npm i -D @types/cors
```

CORS 미들웨어 사용

```jsx
import express from 'express';
**import cors from 'cors';**

const app = express();

**app.use(cors());**
```

정교한 설정은 공식 문서 참고.