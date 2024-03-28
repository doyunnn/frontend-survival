## 목표
### - AddToCartForm



---

## 내용
### 1. AddToCartForm

### 장바구니에 상품 담기

- 장바구니에 상품을 담는다는 게 어떤 의미일까?
- 정확히는 Product가 Cart로 들어가는 게 아니라, Product와 관련된 Option 정보, 수량 등 다양한 값이 조합돼 Cart의 Line Item을 구성하는 게 “장바구니에 상품을 담는다”는 말의 진짜 의미다.

- 실제로는 조금 복잡한 도메인 로직이 들어갈 수 있는데, 이런 처리는 백엔드에서 담당하기로 하고, 여기서는 상품과 관련된 옵션, 수량 등을 컨트롤하는데 집중해 보자.

- AddToCartForm 컴포넌트에선 다음과 같은 요소를 포함한다:
  - 옵션을 보여주고, 선택할 수 있게 한다.
  - 수량을 정하게 한다. (기본값: 1)
  - 수량에 맞는 비용을 보여준다.
  - 장바구니에 담기 버튼이 있고, 이걸 누르면 장바구니에 담았다는 메시지로 바뀐다.


#### 보여주기

- Prop Drilling을 피하기 위해 전부 개별 컴포넌트에서 Store를 가져다 쓰게 했다.

```tsx
export default function AddToCartForm() {
    return (
        <div>
            <Options />
            <Quantity />
            <Price />
            <SubmitButton />
        </div>
    );
}
```

```tsx
export default function Quantity() {
    const [{ quantity }, store] = useProductFormStore();

    const handleClickDecrease = () => {
        store.changeQuantity(quantity - 1);
    };

    const handleClickIncrease = () => {
        store.changeQuantity(quantity + 1);
    };

    return (
        <Container>
            <Button onClick={handleClickDecrease}>
                -
            </Button>
            <input type="text" value={quantity} readOnly />
            <Button onClick={handleClickIncrease}>
                +
            </Button>
        </Container>
    );
}
```

```tsx
export default function Price() {
    const [{ product }] = useProductDetailStore();
    const [{ quantity }] = useProductFormStore();

    return (
        <Container>
            {numberFormat(product.price * quantity)}
            원
        </Container>
    );
}

// 만약 ProductFormStore에 수량에 따른 금액을 계산하는 메서드 또는 Getter가 있다면 다른 형태로 접근할 수도 있다.
export default function Price() {
    // const [{ product }] = useProductDetailStore();
    const [{ price }, productFormStore] = useProductFormStore();

    // TODO: product 변경에 따른 setProduct 호출은 여기가 아니라 page 등에서 처리할 것!
    // useEffect(() => {
    //     productFormStore.setProduct(product);
    // }, [productFormStore, product]);

    return (
        <Container>
            {numberFormat(price)}
            원
        </Container>
    );
}
```

```tsx
export default function SubmitButton() {
    const [{ done }, store] = useProductFormStore();

    const handleClick = () => {
        store.addToCart();
    };

    if (done) {
        return (
            <p>장바구니에 담았습니다</p>
        );
    }

    return (
        <Button onClick={handleClick}>
            장바구니에 담기
        </Button>
    );
}
```

```tsx
export default function Options() {
  const [{ product, selectedOptionItems }, store] = useProductFormStore();

  const handleChange: ChangeFunction = ({ optionId, optionItemId }) => {
    store.changeOptionItem({ optionId, optionItemId });
  };

  return (
    <div>
      {product.options.map((option, index) => (
        <Option
          key={option.id}
          option={option}
          selectedItem={selectedOptionItems[index]}
          onChange={handleChange}
        />
      ))}
    </div>
  );
}
```

```tsx
type OptionProps = {
    option: ProductOption;
    selectedItem: ProductOptionItem;
    onChange: ChangeFunction;
}

export default function Option({
                                   option, selectedItem, onChange,
                               }: OptionProps) {
    const handleChange = (item: ProductOptionItem | null) => {
        if (!item) {
            return;
        }

        onChange({
            optionId: option.id,
            optionItemId: item.id,
        });
    };

    return (
        <ComboBox
            label={option.name}
            selectedItem={selectedItem}
            items={option.items}
            itemToId={(item) => item.id}
            itemToText={(item) => item.name}
            onChange={handleChange}
        />
    );
}
```

```tsx
const Container = styled.div`
  label {
    margin-right: .5rem;
  }
`;

type ComboBoxProps<T> = {
  label: string;
  selectedItem: T;
  items: T[];
  itemToId: (item: T) => string;
  itemToText: (item: T) => string;
  onChange: (item: T | null) => void;
}

export default function ComboBox<T>({
  label, selectedItem, items, itemToId, itemToText, onChange,
}: ComboBoxProps<T>) {
  const id = useRef(`combobox-${Math.random().toString().slice(2)}`);

  const handleChange = (event: React.ChangeEvent<HTMLSelectElement>) => {
    const { value } = event.target;
    const selected = items.find((item) => itemToId(item) === value);
    onChange(selected ?? null);
  };

  return (
    <Container>
      <label htmlFor={id.current}>
        {label}
      </label>
      <select
        id={id.current}
        onChange={handleChange}
        value={itemToId(selectedItem)}
      >
        {items.map((item) => (
          <option key={itemToId(item)} value={itemToId(item)}>
            {itemToText(item)}
          </option>
        ))}
      </select>
    </Container>
  );
}
```

#### Component
```tsx
import styled from 'styled-components';

const Button = styled.button.attrs({
    type: 'button',
})`
  border: .1rem solid #888;
  background: transparent;
  color: ${(props) => props.theme.colors.primary};
  cursor: pointer;
`;

export default Button;
```

#### Store

```ts
@singleton()
@Store()
export default class ProductFormStore {
    product: ProductDetail = nullProductDetail;

    selectedOptionItems: ProductOptionItem[] = [];

    quantity = 1;

    done = false;


    async addToCart() {
        this.resetDone();

        await apiService.addProductToCart({
            product: this.product.id,
            options: this.product.options.map((option, index) => ({
                id: option.id,
                itemId: this.selectedOptionItems[index].id,
            })),
            quantity: this.quantity,
        });

        this.complete();
    }
    
    @Action()
    changeQuantity(quantity: number) {
        if (quantity <= 0) {
            return;
        }
        if (quantity > 10) {
            return;
        }
        this.quantity = quantity;
    }

    @Action()
    changeOptionItem({ optionId, optionItemId }: {
        optionId: string;
        optionItemId: string;
    }) {
        this.selectedOptionItems = this.product.options.map((option, index) => {
            const item = this.selectedOptionItems[index];
            return option.id !== optionId
                ? item
                : option.items.find((i) => i.id === optionItemId) ?? item;
        });
    }

    @Action()
    resetDone() {
        this.done = false;
    }

    @Action()
    complete() {
        this.quantity = 1;
        this.done = true;
    }
}
````

#### hooks
    
```ts
export default function useProductFormStore() {
    const store = container.resolve(ProductFormStore);
    return useStore(store);
}

```

- useFetchProduct 훅을 아래와 같이 변경 product 호출 위치 변경
```ts
export default function useFetchProduct({ productId }: {
    productId: string;
}) {
    const [{ product ,loading, error }, productDetailStore] = useProductDetailStore();
    const [, productFormStore] = useProductFormStore();

    useEffect(() => {
        productDetailStore.fetchProduct({ productId });
    }, [productDetailStore, productId]);

    useEffect(() => {
        productFormStore.setProduct(product);
    }, [product, productFormStore]);

    return { loading, error };
}
```


#### test
```tsx
describe('ProductFormStore', () => {
  let store: ProductFormStore;

  beforeEach(() => {
    store = new ProductFormStore();
  });

  describe('changeQuantity', () => {
    context('with correct value', () => {
      it('changes quantity', () => {
        store.changeQuantity(3);

        expect(store.quantity).toBe(3);
      });
    });

    context('with incorrect value', () => {
      it("doesn't changes quantity", () => {
        store.changeQuantity(-1);
        store.changeQuantity(11);

        expect(store.quantity).toBe(1);
      });
    });
  });
});
```

#### API

```ts
export default class ApiService {
  // ...

    async addProductToCart({ productId, options, quantity }: {
        productId: string;
        options: {
            id: string;
            itemId: string;
        }[];
        quantity: number;
    }): Promise<void> {
        await this.instance.post('/cart/line-items', {
            productId, options, quantity,
        });
    }
}

export const apiService = new ApiService();
```
