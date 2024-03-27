## 목표
### - Cart List



---

## 내용
### 1. Cart List

### 장바구니 보기

- 장바구니에 상품 담기 기능을 만들기 전에, 비교적 쉬운 장바구니 보기 작업을 먼저 끝내보자.

#### 장바구니 보여주기

```tsx
export default function CartPage() {
  const { cart } = useFetchCart();

  if (!cart) {
    return null;
  }

  return (
          <div>
            <h2>장바구니</h2>
            <CartView cart={cart} />
          </div>
  );
}
```

```tsx
const Container = styled.div`
  table {
    width: 100%;
  }

  th, td {
    padding: .5rem;
    text-align: left;
  }
`;

type CartViewProps = {
  cart: Cart;
};

export default function CartView({ cart }: CartViewProps) {
  if (!cart.lineItems.length) {
    return (
            <p>장바구니가 비었습니다</p>
    );
  }

  return (
          <Container>
            <table>
              <thead>
              <tr>
                <th>제품</th>
                <th>단가</th>
                <th>수량</th>
                <th>금액</th>
              </tr>
              </thead>
              <tbody>
              {cart.lineItems.map((lineItem) => (
                      <LineItemView
                              key={lineItem.id}
                              lineItem={lineItem}
                      />
              ))}
              </tbody>
              <tfoot>
              <tr>
                <th colSpan={3}>
                  합계
                </th>
                <td>
                  {numberFormat(cart.totalPrice)}
                  원
                </td>
              </tr>
              </tfoot>
            </table>
          </Container>
  );
}
```

```tsx
type LineItemViewProps = {
  lineItem: LineItem;
};

export default function LineItemView({ lineItem }: LineItemViewProps) {
  return (
          <tr>
            <td>
              <Link to={`/products/${lineItem.product.id}`}>
                {lineItem.product.name}
              </Link>
              <Options options={lineItem.options} />
            </td>
            <td>
              {numberFormat(lineItem.unitPrice)}
              원
            </td>
            <td>{lineItem.quantity}</td>
            <td>
              {numberFormat(lineItem.totalPrice)}
              원
            </td>
          </tr>
  );
}
```
```tsx
const Container = styled.div`
  margin-top: .5rem;
  font-size: 1.4rem;
`;

type OptionsProps = {
  options: OrderOption[];
}

export default function Options({ options }: OptionsProps) {
  if (!options.length) {
    return null;
  }

  const text = options
          .map((option) => `${option.name}: ${option.item.name}`)
          .join(', ');

  return (
          <Container>
            (
            {text}
            )
          </Container>
  );
}
```
#### Store

```ts
@singleton()
@Store()
export default class CartStore {
  cart: Cart | null = null;

  async fetchCart() {
    this.setCart(null);

    const cart = await apiService.fetchCart();

    this.setCart(cart);
  }

  @Action()
  setCart(cart: Cart | null) {
    this.cart = cart;
  }
}
````

#### hooks
    
```ts
export default function useFetchCart() {
  const store = container.resolve(CartStore);

  const [{ cart }] = useStore(store);

  useEffectOnce(() => {
    store.fetchCart();
  });

  return { cart };
}
```

#### API

```ts
export default class ApiService {
  // ...

  async fetchCart(): Promise<Cart> {
    const { data } = await this.instance.get('/cart');
    return data;
  }
}

export const apiService = new ApiService();
```

#### cURL을 이용해서 임의로 장바구니에 상품 담기

```shell
curl -X POST https://shop-demo-api-01.fly.dev/cart/line-items \
  -H "Content-Type: application/json" \
  --data '
    {
      "productId": "0BV000PRO0001",
      "options": [
        {
          "id": "0BV000OPT0001",
          "itemId": "0BV000ITEM001"
        },
        {
          "id": "0BV000OPT0002",
          "itemId": "0BV000ITEM005"
        }
      ],
      "quantity": 1
    }
  '
```