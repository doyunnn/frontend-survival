## 목표
### - Init Admin

- 기능
- 화면
- REST API
- 라이브러리
    - SWR
    - React Hook Form
- Types
- Api Service

---

## 내용
### 1. 기능

1. 로그인
2. 로그아웃
3. 회원 관리
    - 회원 목록
4. 카테고리 관리
    - 카테고리 목록
    - 카테고리 등록
    - 카테고리 수정
5. 주문 관리
    - 주문 목록
    - 주문 상세
    - 주문 상태 변경
6. 상품 관리
    - 상품 목록
    - 상품 상세
    - 상품 등록
    - 상품 수정

### 2. 화면

1. 홈 - /
2. 로그인 - /login
3. 회원 목록 - /users
4. 카테고리 목록 - /categories
5. 카테고리 등록 - /categories/new
6. 카테고리 수정 - /categories/{id}/edit
7. 상품 목록 - /products
8. 상품 상세 - /products/{id}
9. 상품 등록 - /products/new
10. 상품 수정 - /products/{id}/edit
11. 주문 목록 - /orders
12. 주문 상세 - /orders/{id}
13. 주문 상태 변경 - /orders/{id}/edit
    - 주문 상태 변경을 넘어서, 배송지 수정이나 송장 번호 입력 등을 해볼 수 있다.
    - 제품화하려면 해볼 수 있는 게 아니라, 해야 한다.

### REST API

> API Base URL : https://shop-demo-api-04.fly.dev


### 준비

- 기존에 작업한 코드를 재활용하면서 상품 등록/수정을 제외한 대부분을 단순 CRUD로 구성할 예정.
- 여기선 단순 작업을 위해 SWR과 React Hook Form을 사용한다.

#### [SWR](https://swr.vercel.app/ko)
```shell
npm i swr
```
- F/E에서 상태 관리를 아주 적극적으로 하는 흐름이 있다면, 반대로 F/E에선 캐시에 집중하고 백엔드와의 동기화에 집중하는 흐름이 있다.
- 여기서는 B/E에 의존적인 아주 단순한 CRUD 애플리케이션을 구축할 거라 SWR를 써보도록 하겠다.
- 좀 더 복잡한 걸 다루고 싶다면 TanStack Query에도 도전해 보자.

#### [React Hook Form](https://react-hook-form.com)
```shell
npm i react-hook-form
```
- 단순한 CRUD 애플리케이션을 구축한다면 Uncontrolled Component를 사용하거나, 그에 준하는 편의성을 제공하는 도구를 활용할 수 있다.
- React Hook Form은 이 모두를 지원한다.
- 특히 [Uncontrolled](https://ko.legacy.reactjs.org/docs/uncontrolled-components.html) 방식에 집중해서 활용하면 리렌더링이 줄어들어 커다란 폼의 성능 문제에서 유리하다.


### Types

```ts
export type User = {
  id: string;
  name: string;
  email: string;
  role: string;
}

export type Category = {
  id: string;
  name: string;
  hidden?: boolean;
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
  hidden: boolean;
}

export type ProductOptionItem = {
  id: string;
  name: string;
  deleted: boolean;
};

export type ProductOption = {
  id: string;
  name: string;
  items: ProductOptionItem[];
  deleted: boolean;
};

export type ProductDetail = {
  id: string;
  category: Category;
  images: Image[];
  name: string;
  price: number;
  options: ProductOption[];
  description: string;
  hidden: boolean;
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

export type OrderSummary = {
  id: string;
  orderer: User;
  title: string;
  totalPrice: number;
  status: string;
  orderedAt: string;
}

type Receiver = {
  name: string;
  address1: string;
  address2: string;
  postalCode: string;
  phoneNumber: string;
}

type Payment = {
  merchantId: string;
  transactionId: string;
}

export type OrderDetail = {
  id: string;
  orderer: User;
  title: string;
  lineItems: LineItem[];
  totalPrice: number;
  receiver: Receiver;
  payment: Payment;
  status: string;
  orderedAt: string;
}
```

### Api Service

- 여기선 관리자 API에 맞게 ApiService가 준비되었다고 가정한다.
- CRUD 중 Read에 관한 건 대부분 SWR를 사용하기 때문에 대부분은 CUD, 즉 Command(또는 Mutation)에 대한 코드만 있다.