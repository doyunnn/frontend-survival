## 목표
### - Product List



---

## 내용
### 1. Product List

### 상품 목록

- 상품 목록을 얻어서 표시하는 화면을 만들자.
- 말 그대로 다음과 같이 두 가지로 나눌 수 있다:
  - 상품 목록 얻기
  - 상품 목록 보여주기

#### 상품 목록 얻기

```tsx
import Products from '../components/product-list/Products';

import useFetchProducts from '../hooks/useFetchProducts';

export default function ProductListPage() {
  const { products } = useFetchProducts();

  return (
    <div>
      <h2>Products</h2>
      <Products products={products} />
    </div>
  );
}
```

```tsx
const apiBaseUrl = 'https://shop-demo-api-01.fly.dev';

export default function useFetchProducts() {
  type Data = {
    products: ProductSummary[];
  };

  const { data } = useFetch<Data>(`${apiBaseUrl}/products`);

  return {
    products: data?.products ?? [],
  };
}
```

#### 상품 목록 보여주기

```tsx
const Container = styled.div`
  ul {
    display: flex;
    flex-wrap: wrap;
  }

  li {
    width: 20%;
    padding: 1rem;
  }

  a {
    display: block;
    text-decoration: none;
  }
`;

type ProductsProps = {
  products: ProductSummary[];
}

export default function Products({ products }: ProductsProps) {
  if (!products.length) {
    return null;
  }

  return (
    <Container>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            <Link to={`/products/${product.id}`}>
              <Product product={product} />
            </Link>
          </li>
        ))}
      </ul>
    </Container>
  );
}
```

```tsx
const Thumbnail = styled.img.attrs({
  alt: 'Thumbnail',
})`
  display: block;
  width: 100%;
  aspect-ratio: 1/1;
`;

type ProductProps = {
  product: ProductSummary;
}

export default function Product({ product }: ProductProps) {
  return (
    <div>
      <Thumbnail src={product.thumbnail.url} />
      <div>{product.name}</div>
      <div>
        {numberFormat(product.price)}
        원
      </div>
    </div>
  );
}
```

```tsx
export default function ProductListPage() {
  const [params] = useSearchParams();

  const categoryId = params.get('categoryId') ?? undefined;

  const { products } = useFetchProducts({ categoryId });

  return (
    <div>
      <h2>Products</h2>
      <Products products={products} />
    </div>
  );
}

```

#### Store

```ts
@singleton()
@Store()
export default class ProductsStore {
    products: ProductSummary[] = [];

    async fetchProducts({ categoryId }: {
        categoryId?: string;
    }) {
        this.setProducts([]);

        const products = await apiService.fetchProducts({ categoryId });

        this.setProducts(products);
    }

    @Action()
    setProducts(products: ProductSummary[]) {
        this.products = products;
    }
}
````

#### hooks
    
```ts
export default function useFetchProducts({ categoryId }: {
    categoryId?: string;
}): {
    products: ProductSummary[];
} {
    const store = container.resolve(ProductsStore);

    const [{ products }] = useStore(store);

    useEffect(() => {
        store.fetchProducts({ categoryId });
    }, []);

    return { products };
}
```





#### 유틸 함수
    
```ts
export default function numberFormat(value: number) {
  return new Intl.NumberFormat().format(value);
}
```

#### API

```ts
const API_BASE_URL = process.env.API_BASE_URL
                     || 'https://shop-demo-api-01.fly.dev';

export default class ApiService {
  private instance = axios.create({
    baseURL: API_BASE_URL,
  });

  async fetchCategories(): Promise<Category[]> {
    const { data } = await this.instance.get('/categories');
    const { categories } = data;
    return categories;
  }

  async fetchProducts({ categoryId }: {
      categoryId?: string;
  } = {}): Promise<ProductSummary[]> {
    const { data } = await this.instance.get('/products');
    const { products } = data;
    return products;
  }
}

export const apiService = new ApiService();
```