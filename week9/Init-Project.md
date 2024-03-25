## 목표
### - Init Project

- 용어
- 기능
- 화면
- REST API
- 라이브러리
- E2E Test
- Test Helper
- Types
- MSW 세팅

---

## 내용
### 1. 용어

- Product: 상품 
  - Summary: 상품에 대한 요약 정보
  - Detail: 상품에 대한 상세 정보
  - Image: 상품 이미지
  - Option: 상품에 대한 상세 옵션 종류 (색상, 크기 등)
    - OptionItem: 옵션에 대한 상세 옵션 값 (옵션이 색상이라면 이건 Blue, Red 같은 걸 의미함)
- Category: 상품에 대한 분류
- Cart: 장바구니
  - LineItem: 장바구니에 담긴 것 (상품, 옵션, 수량 등을 동시에 다룸)
    - 여기서도 Option과 OptionItem을 사용한다.
    - 용어는 동일하지만 Product와 다른 구성을 갖기 때문에, 여기서는 Product와 Order라는 접두어를 활용한다.
    - 시스템을 분리할 수 있다면, 근본적으로 나누는 걸 추천(상품 정보 확인 / 장바구니 / 주문).
- Order: 주문
  - 여기서도 동일한 LineItem 활용.
- User: 사용자

### 2. 기능

- 상품 목록 확인
- 상품 상세 정보 확인
- 장바구니에 상품 담기
- 주문하기 → 배송지 입력, 결제
- 주문 목록 확인
- 주문 상세 확인
- 로그인
- 회원 가입

### 화면

- 홈 페이지 - /
- 상품 목록 페이지 - /products
- 상품 상세 페이지 - /products/{id}
- 장바구니 페이지 - /cart
- 주문 페이지 - /order
- 주문 완료 페이지 - /order/complete
- 주문 목록 페이지 - /orders
- 주문 상세 페이지 - /orders/{id}
- 로그인 페이지 - /login
- 회원 가입 페이지 - /signup
- 회원 가입 완료 페이지 - /signup/complete

### REST API

> [REST API](https://docs.google.com/document/d/1bGYl3IDoX53cNBbZHNlsRhPLZQ3Qiu-Jm3gpqyu_xI0/view)
> 
> API Base URL : https://shop-demo-api-01.fly.dev


### 사용하는 라이브러리

- [React Router](https://github.com/remix-run/react-router)
- [styled-components](https://github.com/styled-components/styled-components)
- [styled-reset](https://github.com/zacanger/styled-reset)
- [usehooks-ts](https://github.com/juliencrn/usehooks-ts)
- [Axios: REST API 사용을 위한 HTTP 클라이언트.](https://github.com/axios/axios)
- [tsyringe](https://github.com/microsoft/tsyringe)
- [reflect-metadata](https://github.com/rbuckton/reflect-metadata)
- [usestore-ts](https://github.com/seed2whale/usestore-ts)
- [jest-dom: React Testing Library에서 활용할 수 있는 DOM 확인용 Matcher 모음.](https://github.com/testing-library/jest-dom)
- [MSW]

### E2E Test

#### CodeceptJS로 E2E 테스트 준비하고, 여기 있는 기능 테스트를 모두 통과시키는 걸 목표로 삼는다.

- 상품 목록
  - 모든 상품 보기
  - 특정 카테고리의 상품 보기
- 상품 상세
- 장바구니
  - 장바구니가 비어있는 경우
  - 장바구니에 상품을 담은 경우

### Styles

- styles-components를 사용하기 위해 defaultTheme과 GlobalStyles를 준비.

### Routes

- React Router로 여러 페이지를 표현하기 위해 routes와 Layout을 준비.

### Test Helper

- 테스트 코드에서 styled-components의 Theme과 React Router의 Link 등을 사용할 때 문제가 발생하지 않도록, React Testing Library의 render를 한번 감싼 테스트용 헬퍼 함수를 준비.

```tsx
import { render as originalRender } from '@testing-library/react';

import React from 'react';

import { MemoryRouter } from 'react-router-dom';

import { ThemeProvider } from 'styled-components';

import defaultTheme from './styles/defaultTheme';

export function render(element: React.ReactElement) {
  return originalRender((
    <MemoryRouter initialEntries={['/']}>
      <ThemeProvider theme={defaultTheme}>
        {element}
      </ThemeProvider>
    </MemoryRouter>
  ));
}
```

### Types


<details>
<summary>Types</summary>
<div markdown="1">

```tsx
    export type Category = {
      id: string;
      name: string;
    }

    export type Image = {
      url: string;
    }
    
    export type ProductSummary = {
      id: string;
      category: Category;
      thumbnail: Image;
      name: string;
      price: number;
    }
    
    export type ProductOptionItem = {
      id: string;
      name: string;
    };
    
    export type ProductOption = {
      id: string;
      name: string;
      items: ProductOptionItem[];
    };
    
    export type ProductDetail = {
      id: string;
      category: Category;
      images: Image[];
      name: string;
      price: number;
      options: ProductOption[];
      description: string;
    }
    
    export type OrderOptionItem = {
      name: string;
    };
    
    export type OrderOption = {
      name: string;
      item: OrderOptionItem;
    };
    
    export type LineItem = {
      id: string;
      product: {
        id: string;
        name: string;
      };
      options: OrderOption[];
      unitPrice: number;
      quantity: number;
      totalPrice: number;
    }
    
    export type Cart = {
      lineItems: LineItem[];
      totalPrice: number;
    }
```

</div>
</details>


### MSW 세팅

- REST API 스펙에 맞춰서 MSW 핸들러를 준비한다.

```tsx
import { rest } from 'msw';

import { ProductSummary } from '../types';

import fixtures from '../../fixtures';

const BASE_URL = 'https://shop-demo-api-01.fly.dev';

const productSummaries: ProductSummary[] = fixtures.products
    .map((product) => ({
        id: product.id,
        category: product.category,
        thumbnail: product.images[0],
        name: product.name,
        price: product.price,
    }));

const handlers = [
    rest.get(`${BASE_URL}/categories`, (req, res, ctx) => (
        res(ctx.json({ categories: fixtures.categories }))
    )),
    rest.get(`${BASE_URL}/products`, (req, res, ctx) => (
        res(ctx.json({ products: productSummaries }))
    )),
    rest.get(`${BASE_URL}/products/:id`, (req, res, ctx) => {
        const product = fixtures.products.find((i) => i.id === req.params.id);
        if (!product) {
            return res(ctx.status(404));
        }
        return res(ctx.json(product));
    }),
    rest.get(`${BASE_URL}/cart`, (req, res, ctx) => (
        res(ctx.json(fixtures.cart))
    )),
    rest.post(`${BASE_URL}/cart/line-items`, (req, res, ctx) => (
        res(ctx.status(201))
    )),
];

export default handlers;
```