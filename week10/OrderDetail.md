## 목표
### - Order Detail

---

## 내용
### 1. Order Detail

#### 주문 상세 보여주기

```tsx
export default function OrderDetailPage() {
  const params = useParams();

  const { order, loading, error } = useFetchOrder({
    orderId: String(params.id),
  });

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Order order={order} />
  );
}
```

```tsx
import styled from 'styled-components';

import { OrderDetail } from '../../types';

import Table from '../line-item/Table';

const STATUS_MESSAGES: Record<string, string> = {
    pending: '결제 대기 중',
    paid: '결제 완료',
    shipped: '배송 중',
    delivered: '배송 완료',
};

const Container = styled.div`
  dl {
    display: flex;
    flex-wrap: wrap;
    line-height: 1.7;

    dt {
      width: 8rem;
    }

    dd {
      width: calc(100% - 8rem);
    }
  }
`;

type OrderProps = {
    order: OrderDetail;
};

export default function Order({ order }: OrderProps) {
    if (!order.lineItems.length) {
        return null;
    }

    return (
        <Container>
            <dl>
                <dt>주문 일시</dt>
                <dd>{order.orderedAt}</dd>
                <dt>주문 코드</dt>
                <dd>{order.id}</dd>
                <dt>처리 상태</dt>
                <dd>{STATUS_MESSAGES[order.status]}</dd>
            </dl>
            <Table
                lineItems={order.lineItems}
                totalPrice={order.totalPrice}
            />
        </Container>
    );
}
```

- LineItem을 이용해 상품 목록을 보여주는 부분은 장바구니 보여줄 때 만든 LineItemView와 Options 컴포넌트를 재사용하면 좋을 것 같다. CartView 컴포넌트에서 Table 컴포넌트를 추출하자.

```tsx
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
    <Table
      lineItems={cart.lineItems}
      totalPrice={cart.totalPrice}
    />
  );
}
```

```tsx
const Container = styled.div`
  table {
    margin-block: 1rem;
    width: 100%;
  }

  th, td {
    padding: .5rem;
    text-align: left;
  }
`;

type TableProps = {
  lineItems: LineItem[];
  totalPrice: number;
};

export default function Table({ lineItems, totalPrice }: TableProps) {
  if (!lineItems.length) {
    return null;
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
          {lineItems.map((lineItem) => (
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
              {numberFormat(totalPrice)}
              원
            </td>
          </tr>
        </tfoot>
      </table>
    </Container>
  );
}
```

### Store

```tsx
@singleton()
@Store()
export default class OrderDetailStore {
  order: OrderDetail = nullOrderDetail;

  loading = true;

  error = false;

  async fetchOrder({ orderId }: {
    orderId: string;
  }) {
    this.startLoading();

    try {
      const order = await apiService.fetchOrder({ orderId });
      this.setOrder(order);
    } catch {
      this.setError();
    }
  }

  @Action()
  private startLoading() {
    this.order = nullOrderDetail;
    this.loading = true;
    this.error = false;
  }

  @Action()
  private setOrder(order: OrderDetail) {
    this.order = order;
    this.loading = false;
    this.error = false;
  }

  @Action()
  private setError() {
    this.order = nullOrderDetail;
    this.loading = false;
    this.error = true;
  }
}
```

#### Hook

```tsx
export default function useFetchOrder({ orderId }: {
  orderId: string;
}) {
  const store = container.resolve(OrderDetailStore);

  const [{ order, loading, error }] = useStore(store);

  useEffect(() => {
    store.fetchOrder({ orderId });
  }, [store]);

  return { order, loading, error };
}
```

#### API
```tsx
export default class ApiService {
  // ...

    async fetchOrder({ orderId }: {
        orderId: string;
    }): Promise<OrderDetail> {
        const { data } = await this.instance.get(`/orders/${orderId}`);
        return data;
    }
}
```

#### routes

```js
{ path: '/orders/:id', element: <OrderDetailPage /> },
```
