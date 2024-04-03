## 목표
### - Order List

---

## 내용
### 1. Order List


#### 주문 목록 보여주기

```tsx
export default function OrderListPage() {
  const { orders } = useFetchOrders();

  return (
    <div>
      <h2>주문 목록</h2>
      <Orders orders={orders} />
    </div>
  );
}
```

```tsx
const Container = styled.div`
  li {
    margin-block: .5rem;
    padding: 1rem;
    background: #EEE;
  }

  a {
    display: block;
    text-decoration: none;
  }
`;

type OrdersProps = {
  orders: OrderSummary[];
}

export default function Orders({ orders }: OrdersProps) {
  if (!orders.length) {
    return null;
  }

  return (
    <Container>
      <ul>
        {orders.map((order) => (
          <li key={order.id}>
            <Link to={`/orders/${order.id}`}>
              <Order order={order} />
            </Link>
          </li>
        ))}
      </ul>
    </Container>
  );
}
```

```tsx
const Container = styled.div`
  line-height: 1.5;
`;

type OrderProps = {
  order: OrderSummary;
}

export default function Order({ order }: OrderProps) {
  return (
    <Container>
      <div>
        주문 일시:
        {' '}
        {order.orderedAt}
      </div>
      <div>
        주문 코드:
        {' '}
        {order.id}
      </div>
      <div>
        상품:
        {' '}
        {order.title}
      </div>
      <div>
        결제 금액:
        {' '}
        {numberFormat(order.totalPrice)}
        원
      </div>
    </Container>
  );
}
```

#### Store

```tsx
@singleton()
@Store()
export default class OrderListStore {
  orders: OrderSummary[] = [];

  async fetchOrders() {
    this.setOrders([]);

    const orders = await apiService.fetchOrders();

    this.setOrders(orders);
  }

  @Action()
  setOrders(orders: OrderSummary[]) {
    this.orders = orders;
  }
}
```

#### hook

```tsx
export default function useFetchOrders() {
  const store = container.resolve(OrderListStore);

  const [{ orders }] = useStore(store);

  useEffect(() => {
    store.fetchOrders();
  }, [store]);

  return { orders };
}
```

#### API

```tsx
export default class ApiService {
  // ...

  async fetchOrders(): Promise<OrderSummary[]> {
      const { data } = await this.instance.get('/orders');
      const { orders } = data;
    return orders;
  }
}
```

#### routes

```js
{ path: '/orders', element: <OrderListPage /> },
```

#### Header

```tsx
<li>
  <Link to="/orders">Orders</Link>
</li>
```