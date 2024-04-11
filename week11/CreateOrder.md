## 목표
### - Create Order

---

## 내용
### 1. Create Order


#### Api

```ts
export default class ApiService {
  // ...

    async createOrder({ receiver, payment }: {
        receiver: {
            name: string;
            address1: string;
            address2: string;
            postalCode: string;
            phoneNumber: string;
        };
        payment: {
            merchantId: string;
            transactionId: string;
        };
    }): Promise<void> {
        await this.instance.post('/orders', { receiver, payment });
    }
}
```

#### store (OrderFormStore)

```ts
export default class OrderFormStore {
    // ...

    async order({ merchantId, transactionId }: {
        merchantId: string;
        transactionId: string;
    }) {
        await apiService.createOrder({
            receiver: {
                name: this.name,
                address1: this.address1,
                address2: this.address2,
                postalCode: this.postalCode,
                phoneNumber: this.phoneNumber,
            },
            payment: { merchantId, transactionId },
        });
    }
}
```

#### use (PaymentButton)

```tsx
  const [{ valid }, store] = useOrderFormStore();

  // ...

  const handleClick = async () => {
    setError('');

    try {
      const { merchantId, transactionId } = await requestPayment();

      // TODO: B/E로 주문 및 결제 정보 전달.
      await store.order({ merchantId, transactionId });

      navigate('/order/complete');
    } catch (e) {
      if (e instanceof Error) {
        setError(e.message);
      }
    }
  };
```