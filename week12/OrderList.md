## 목표
### - Order List

---

## 내용
### 1. Order List

#### List
```tsx
import { Link } from 'react-router-dom';

import styled from 'styled-components';

import useFetchOrders from '../hooks/useFetchOrders';

import numberFormat from '../utils/numberFormat';

import { STATUS_MESSAGES } from '../constants';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  table {
    th, td {
      padding: 1rem;
      text-align: left;
    }

    th {
      border-bottom: .1rem solid ${(props) => props.theme.colors.secondary}
    }

    img {
      width: 10rem;
      vertical-align: middle;
    }

    a {
      display: block;
      margin-bottom: 1rem;
    }
  }

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

export default function OrderListPage() {
  const { orders, loading, error } = useFetchOrders();

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
    <Container>
      <h2>Orders</h2>
      <table>
        <thead>
          <tr>
            <th>주문일</th>
            <th>주문자</th>
            <th>상품</th>
            <th>총 가격</th>
            <th>상태</th>
            <th>행동</th>
          </tr>
        </thead>
        <tbody>
          {orders.map((order) => (
            <tr key={order.id}>
              <td>{order.orderedAt}</td>
              <td>{order.orderer.name}</td>
              <td>{order.title}</td>
              <td>
                {numberFormat(order.totalPrice)}
                원
              </td>
              <td>{STATUS_MESSAGES[order.status]}</td>
              <td>
                <Link to={`/orders/${order.id}`}>
                  자세히
                </Link>
                <Link to={`/orders/${order.id}/edit`}>
                  상태 변경
                </Link>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </Container>
  );
}
```

#### Detail
```tsx
import { Link, useParams } from 'react-router-dom';

import styled from 'styled-components';

import Options from '../components/Options';

import useFetchOrder from '../hooks/useFetchOrder';

import numberFormat from '../utils/numberFormat';

import { STATUS_MESSAGES } from '../constants';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  dl {
    &::after {
      clear: left;
      display: block;
      content: "";
    }

    dt {
      clear: left;
      width: 10rem;
      margin-right: 1rem;
      text-align: right;
    }

    dt, dd {
      float: left;
      margin-block: .5rem;
    }

    span {
      margin-left: .5rem;
      font-size: 1.4rem;
    }
  }

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

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

  if (error || !order) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Container>
      <h2>Order Detail</h2>
      <dl>
        <dt>주문일시</dt>
        <dd>{order.orderedAt}</dd>
        <dt>주문자</dt>
        <dd>{order.orderer.name}</dd>
        <dt>상품</dt>
        <dd>
          <ul>
            {order.lineItems.map((lineItem) => (
              <li key={lineItem.id}>
                {lineItem.product.name}
                <Options options={lineItem.options} />
              </li>
            ))}
          </ul>
        </dd>
        <dt>총 가격</dt>
        <dd>
          {numberFormat(order.totalPrice)}
          원
        </dd>
        <dt>배송 정보</dt>
        <dd>
          <p>
            받는 사람:
            {' '}
            {order.receiver.name}
          </p>
          <p>
            연락처:
            {' '}
            {order.receiver.phoneNumber}
          </p>
          <p>
            {order.receiver.address1}
            {' '}
            {order.receiver.address2}
            {' '}
            (우편번호:
            {' '}
            {order.receiver.postalCode}
            )
          </p>
        </dd>
        <dt>결제 정보</dt>
        <dd>
          <p>
            주문번호:
            {' '}
            {order.payment.merchantId}
          </p>
          <p>
            결제고유번호:
            {' '}
            {order.payment.transactionId}
          </p>
        </dd>
        <dt>상태</dt>
        <dd>{STATUS_MESSAGES[order.status]}</dd>
      </dl>
        <p>
            <Link to={`/orders/${order.id}/edit`}>
                상태 변경
            </Link>
            {' '}
            |
            {' '}
            <Link to="/orders">
                주문 목록
            </Link>
        </p>
    </Container>
  );
}
```

#### Edit
```tsx
import { useNavigate, useParams } from 'react-router-dom';

import { Controller, useForm } from 'react-hook-form';

import styled from 'styled-components';

import Button from '../components/ui/Button';

import useFetchOrder from '../hooks/useFetchOrder';

import numberFormat from '../utils/numberFormat';

import { STATUS_MESSAGES } from '../constants';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  dl {
    &::after {
      clear: left;
      display: block;
      content: "";
    }

    dt {
      clear: left;
      width: 10rem;
      margin-right: 1rem;
      text-align: right;
    }

    dt, dd {
      float: left;
      margin-block: .5rem;
    }

    img {
      width: 10rem;
    }
  }

  [type=submit] {
    display: block;
    margin-block: 1rem;
  }
`;

export default function OrderEditPage() {
  const params = useParams();

  const orderId = String(params.id);

  const navigate = useNavigate();

  const {
    order, loading, error, updateOrder,
  } = useFetchOrder({
    orderId,
  });

  type FormValues = {
    status: string;
  };

  const { handleSubmit, control } = useForm<FormValues>();

  const onSubmit = async (data: FormValues) => {
    await updateOrder({
      status: data.status,
    });
    navigate(`/orders/${orderId}`);
  };

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error || !order) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Container>
      <h2>Order Status Transition</h2>
      <dl>
        <dt>주문일시</dt>
        <dd>{order.orderedAt}</dd>
        <dt>주문자</dt>
        <dd>{order.orderer.name}</dd>
        <dt>상품</dt>
        <dd>{order.title}</dd>
        <dt>총 가격</dt>
        <dd>
          {numberFormat(order.totalPrice)}
          원
        </dd>
      </dl>
      <form onSubmit={handleSubmit(onSubmit)}>
        <Controller
          control={control}
          name="status"
          defaultValue={order.status}
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-status">상태</label>
              <select
                id="input-status"
                value={value}
                onChange={onChange}
              >
                {Object.keys(STATUS_MESSAGES).map((status) => (
                  <option key={status} value={status}>
                    {STATUS_MESSAGES[status]}
                  </option>
                ))}
              </select>
            </div>
          )}
        />
        <Button type="submit">
          변경
        </Button>
      </form>
    </Container>
  );
}
```

#### Hooks
```ts
import useFetch from './useFetch';

import { OrderSummary } from '../types';

export default function useFetchOrders() {
  const { data, error, loading } = useFetch<{
    orders: OrderSummary[];
  }>('/orders');

  return {
    orders: data?.orders ?? [],
    error,
    loading,
  };
}
```

```ts
import useFetch from './useFetch';

import { apiService } from '../services/ApiService';

import { OrderDetail } from '../types';

export default function useFetchOrder({ orderId }: {
  orderId: string;
}) {
  const {
    data, error, loading, mutate,
  } = useFetch<OrderDetail>(`/orders/${orderId}`);

  return {
    order: data,
    error,
    loading,
    async updateOrder({ status }: {
      status: string;
    }) {
      await apiService.updateOrder({ orderId, status });

      mutate();
    },
  };
}
```

#### Constant
```ts  
export const STATUS_MESSAGES: Record<string, string> = {
  paid: '결제 완료',
  ready: '배송 준비',
  shipping: '배송 중',
  complete: '배송 완료',
  canceled: '취소',
};
```

#### API
```ts
export default class ApiService {
  // ...

    async updateOrder({ orderId, status }: {
        orderId: string;
        status: string;
    }): Promise<void> {
        await this.instance.patch(`/orders/${orderId}`, { status });
    }
}
```