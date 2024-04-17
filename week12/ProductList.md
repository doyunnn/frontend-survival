## 목표
### - Product List

---

## 내용
### 1. Product List

#### List

```tsx
import useFetchProducts from '../hooks/useFetchProducts';

import numberFormat from '../utils/numberFormat';

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
      margin-right: 1rem;
    }
  }

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

export default function ProductListPage() {
    const { products, loading, error } = useFetchProducts();

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
            <h2>Products</h2>
            <table>
                <thead>
                <tr>
                    <th>카테고리</th>
                    <th>이미지</th>
                    <th>제품명</th>
                    <th>가격</th>
                    <th>표시</th>
                    <th>행동</th>
                </tr>
                </thead>
                <tbody>
                {products.map((product) => (
                    <tr key={product.id}>
                        <td>
                            {product.category.name}
                        </td>
                        <td>
                            {product.thumbnail?.url && (
                                <img src={product.thumbnail.url} alt="썸네일" />
                            )}
                        </td>
                        <td>
                            {product.name}
                        </td>
                        <td>
                            {numberFormat(product.price)}
                            원
                        </td>
                        <td>
                            {product.hidden ? '숨김' : '보임'}
                        </td>
                        <td>
                            <Link to={`/products/${product.id}`}>
                                자세히
                            </Link>
                            <Link to={`/products/${product.id}/edit`}>
                                수정
                            </Link>
                        </td>
                    </tr>
                ))}
                </tbody>
            </table>
            <Link to="/products/new">
                상품 추가
            </Link>
        </Container>
    );
}
```

#### Detail
    
```tsx
import Description from '../components/Description';

import useFetchProduct from '../hooks/useFetchProduct';

import numberFormat from '../utils/numberFormat';

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

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

export default function ProductDetailPage() {
    const params = useParams();

    const { product, loading, error } = useFetchProduct({
        productId: String(params.id),
    });

    if (loading) {
        return (
            <p>Loading...</p>
        );
    }

    if (error || !product) {
        return (
            <p>Error!</p>
        );
    }

    return (
        <Container>
            <h2>Product Detail</h2>
            <dl>
                <dt>이름</dt>
                <dd>{product.name}</dd>
                <dt>카테고리</dt>
                <dd>{product.category.name}</dd>
                <dt>이미지</dt>
                <dd>
                    {product.images.map((image) => (
                        <img
                            key={image.url}
                            src={image.url}
                            alt="썸네일"
                        />
                    ))}
                </dd>
                <dt>가격</dt>
                <dd>
                    {numberFormat(product.price)}
                    원
                </dd>
                <dt>옵션</dt>
                <dd>
                    {product.options.map((option) => (
                        <div key={option.id}>
                            {option.name}
                            :
                            {' '}
                            {option.items.map((item) => item.name).join(', ')}
                        </div>
                    ))}
                </dd>
                <dt>설명</dt>
                <dd>
                    <Description value={product.description} />
                </dd>
                <dt>표시</dt>
                <dd>{product.hidden ? '숨김' : '보임'}</dd>
            </dl>
            <Link to={`/products/${product.id}/edit`}>
                수정
            </Link>
        </Container>
    );
}
```

#### Create
```tsx
import { useEffect } from 'react';

import { useNavigate } from 'react-router-dom';

import ProductNewForm from '../components/product/ProductNewForm';

import useFetchProducts from '../hooks/useFetchProducts';
import useFetchCategories from '../hooks/useFetchCategories';
import useProductFormStore from '../hooks/useProductFormStore';

export default function ProductNewPage() {
  const navigate = useNavigate();

  const { refresh } = useFetchProducts();
  const { categories } = useFetchCategories();

  const [, store] = useProductFormStore();

  useEffect(() => {
    if (!categories.length) {
      return;
    }

    store.reset();
    store.changeCategory(categories[0]);
  }, [store, categories]);

  const handleComplete = () => {
    refresh();
    navigate('/products');
  };

  if (!categories.length) {
    return null;
  }

  return (
    <ProductNewForm
      categories={categories}
      onComplete={handleComplete}
    />
  );
}
```
```tsx
import ComboBox from '../ui/ComboBox';
import TextBox from '../ui/TextBox';
import Button from '../ui/Button';

import Images from './Images';
import Options from './Options';

import useProductFormStore from '../../hooks/useProductFormStore';

import { Category } from '../../types';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }
`;

type ProductNewFormProps = {
  categories: Category[];
  onComplete: () => void;
}

export default function ProductNewForm({
  categories, onComplete,
}: ProductNewFormProps) {
  const [{
    category, images, name, price, options, description, valid, error, done,
  }, store] = useProductFormStore();

  const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    await store.create();
  };

  useEffect(() => {
    if (done) {
      onComplete();
    }
  }, [done]);

  return (
    <Container>
      <h2>New Product</h2>
      <form onSubmit={handleSubmit}>
        <ComboBox
          label="카테고리"
          selectedItem={category}
          items={categories}
          itemToId={(item) => item?.id || ''}
          itemToText={(item) => item?.name || ''}
          onChange={(value) => value && store.changeCategory(value)}
        />
        <Images images={images} store={store} />
        <TextBox
          label="상품명"
          value={name}
          onChange={(value) => store.changeName(value)}
        />
        <TextBox
          type="number"
          label="가격"
          value={price.toString()}
          onChange={(value) => store.changePrice(value)}
        />
        <Options options={options} store={store} />
        <TextBox
          label="설명"
          value={description}
          onChange={(value) => store.changeDescription(value)}
          multiline
        />
        <Button type="submit" disabled={!valid} leftPad>
          등록
        </Button>
        {error && (
          <p>상품 등록 실패</p>
        )}
      </form>
    </Container>
  );
}
```

#### Edit
```tsx
import { useEffect } from 'react';

import { useNavigate, useParams } from 'react-router-dom';

import ProductEditForm from '../components/product/ProductEditForm';

import useFetchProduct from '../hooks/useFetchProduct';
import useFetchCategories from '../hooks/useFetchCategories';
import useProductFormStore from '../hooks/useProductFormStore';

export default function ProductEditPage() {
  const navigate = useNavigate();

  const params = useParams();
  const productId = String(params.id);

  const { product, refresh } = useFetchProduct({ productId });
  const { categories } = useFetchCategories();

  const [, store] = useProductFormStore();

  useEffect(() => {
    if (!product) {
      return;
    }

    store.setProduct(product);
  }, [store, product]);

  const handleComplete = () => {
    refresh();
    navigate(`/products/${productId}`);
  };

  if (!product || !categories.length) {
    return null;
  }

  return (
    <ProductEditForm
      categories={categories}
      onComplete={handleComplete}
    />
  );
}
```

```tsx
import ComboBox from '../ui/ComboBox';
import TextBox from '../ui/TextBox';
import CheckBox from '../ui/CheckBox';
import Button from '../ui/Button';

import Images from './Images';
import Options from './Options';

import useProductFormStore from '../../hooks/useProductFormStore';

import { Category } from '../../types';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }
`;

type ProductEditFormProps = {
  categories: Category[];
  onComplete: () => void;
}

export default function ProductEditForm({
  categories, onComplete,
}: ProductEditFormProps) {
  const [{
    category, images, name, price, options, description, hidden,
    valid, error, done,
  }, store] = useProductFormStore();

  const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    await store.update();
  };

  useEffect(() => {
    if (done) {
      onComplete();
    }
  }, [done]);

  return (
    <Container>
      <h2>Edit Product</h2>
      <form onSubmit={handleSubmit}>
        <ComboBox
          label="카테고리"
          selectedItem={category}
          items={categories}
          itemToId={(item) => item?.id || ''}
          itemToText={(item) => item?.name || ''}
          onChange={(value) => value && store.changeCategory(value)}
        />
        <Images images={images} store={store} />
        <TextBox
          label="상품명"
          value={name}
          onChange={(value) => store.changeName(value)}
        />
        <TextBox
          type="number"
          label="가격"
          value={price.toString()}
          onChange={(value) => store.changePrice(value)}
        />
        <Options options={options} store={store} />
        <TextBox
          label="설명"
          value={description}
          onChange={(value) => store.changeDescription(value)}
          multiline
        />
        <CheckBox
          label="감추기"
          checked={hidden}
          onChange={() => store.toggleHidden()}
        />
        <Button type="submit" disabled={!valid} leftPad>
          변경
        </Button>
        {error && (
          <p>상품 수정 실패</p>
        )}
      </form>
    </Container>
  );
}
```

#### component
```tsx
import React, { useRef } from 'react';

import styled from 'styled-components';

const Container = styled.div`
  margin-block: .5rem;

  label,
  input {
    vertical-align: middle;
  }

  label {
    display: inline-block;
    width: ${(props) => props.theme.sizes.labelWidth};
    padding-right: .5rem;
    text-align: right;
  }
`;

type CheckBox = {
  label: string;
  checked: boolean;
  onChange: (checked: boolean) => void;
}

export default function CheckBox({
  label, checked, onChange,
}: CheckBox) {
  const id = useRef(`checkbox-${Math.random().toString().slice(2)}`);

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    onChange(event.target.checked);
  };

  return (
    <Container>
      <label htmlFor={id.current}>
        {label}
      </label>
      <input
        id={id.current}
        type="checkbox"
        checked={checked}
        onChange={handleChange}
      />
    </Container>
  );
}
```

#### Hooks
```ts
export default function useFetchProducts() {
  const {
    data, error, loading, mutate,
  } = useFetch<{
    products: ProductSummary[];
  }>('/products');

  return {
    products: data?.products ?? [],
    error,
    loading,
    async refresh() {
      mutate();
    },
  };
}
```

```ts
export default function useFetchProduct({ productId }: {
  productId: string;
}) {
  const {
    data, error, loading, mutate,
  } = useFetch<ProductDetail>(`/products/${productId}`);

  return {
    product: data,
    error,
    loading,
    async refresh() {
      mutate();
    },
  };
}
```
```ts
import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import ProductFormStore from '../stores/ProductFormStore';

export default function useProductFormStore() {
  const store = container.resolve(ProductFormStore);
  return useStore(store);
}
```
#### Store

```ts
import { singleton } from 'tsyringe';

import { Store, Action } from 'usestore-ts';

import { apiService } from '../services/ApiService';

import {
  Category, Image, ProductDetail, ProductOption,
} from '../types';

import { append, remove, update } from '../utils';

@singleton()
@Store()
export default class ProductFormStore {
  productId = '';

  category: Category | null = null;

  images: Image[] = [];

  name = '';

  price = '';

  options: ProductOption[] = [];

  description = '';

  hidden = false;

  error = false;

  done = false;

  get valid() {
    const price = parseInt(this.price, 10);
    return !!this.category?.id
      && this.images.length && this.images.every((i) => i.url)
      && !!this.name.trim() && Number.isInteger(price)
      && this.options.every((option) => (
        option.name && option.items.length
          && option.items.every((item) => item.name)
      ))
      && this.description.trim();
  }

  @Action()
  reset() {
    this.productId = '';
    this.category = null;
    this.images = [{ url: '' }];
    this.name = '';
    this.price = '';
    this.options = [];
    this.description = '';
    this.error = false;
    this.done = false;
  }

  @Action()
  setProduct(product: ProductDetail) {
    this.productId = product.id;
    this.category = product.category;
    this.images = product.images;
    this.name = product.name;
    this.price = product.price.toString();
    this.options = product.options;
    this.description = product.description;
    this.hidden = product.hidden;
    this.error = false;
    this.done = false;
  }

  @Action()
  changeCategory(category: Category) {
    this.category = category;
  }

  @Action()
  addImage() {
    this.images = append(this.images, { url: '' });
  }

  @Action()
  removeImage(index: number) {
    this.images = remove(this.images, index);
  }

  @Action()
  changeImageUrl(index: number, url: string) {
    this.images = update(this.images, index, (image) => ({
      ...image,
      url,
    }));
  }

  @Action()
  changeName(name: string) {
    this.name = name;
  }

  @Action()
  changePrice(price: string) {
    this.price = price;
  }

  @Action()
  addOption() {
    const option = {
      name: '',
      items: [{ name: '' }],
    };

    this.options = append(this.options, option);
  }

  @Action()
  removeOption(index: number) {
    this.options = remove(this.options, index);
  }

  @Action()
  changeOptionName(index: number, name: string) {
    this.options = update(this.options, index, (option) => ({
      ...option,
      name,
    }));
  }

  @Action()
  addOptionItem(optionIndex: number) {
    this.options = update(this.options, optionIndex, (option) => ({
      ...option,
      items: append(option.items, { name: '' }),
    }));
  }

  @Action()
  removeOptionItem(optionIndex: number, itemIndex: number) {
    this.options = update(this.options, optionIndex, (option) => ({
      ...option,
      items: remove(option.items, itemIndex),
    }));
  }

  @Action()
  changeOptionItemName(optionIndex: number, itemIndex: number, name: string) {
    this.options = update(this.options, optionIndex, (option) => ({
      ...option,
      items: update(option.items, itemIndex, (item) => ({
        ...item,
        name,
      })),
    }));
  }

  @Action()
  changeDescription(description: string) {
    this.description = description;
  }

  @Action()
  toggleHidden() {
    this.hidden = !this.hidden;
  }

  @Action()
  private setError() {
    this.error = true;
  }

  @Action()
  private setDone() {
    this.done = true;
  }

  async create() {
    try {
      await apiService.createProduct({
        categoryId: this.category?.id || '',
        images: this.images,
        name: this.name,
        price: parseInt(this.price, 10),
        options: this.options,
        description: this.description,
      });

      this.setDone();
    } catch (e) {
      this.setError();
    }
  }

  async update() {
    try {
      await apiService.updateProduct({
        productId: this.productId,
        categoryId: this.category?.id || '',
        images: this.images,
        name: this.name,
        price: parseInt(this.price, 10),
        options: this.options,
        description: this.description,
      });

      this.setDone();
    } catch (e) {
      this.setError();
    }
  }
}
```

#### API
```ts
export default class ApiService {
  // ...

    async createProduct({
                            categoryId, images, name, price, options, description,
                        }: {
        categoryId: string;
        images: Image[];
        name: string;
        price: number;
        options: {
            name: string;
            items: { name: string }[];
        }[];
        description: string;
    }): Promise<void> {
        await this.instance.post('/products', {
            categoryId, images, name, price, options, description,
        });
    }

    async updateProduct({
                            productId, categoryId, images, name, price, options, description,
                        }: {
        productId: string;
        categoryId: string;
        images: Image[];
        name: string;
        price: number;
        options: {
            name: string;
            items: { name: string }[];
        }[];
        description: string;
    }): Promise<void> {
        await this.instance.patch(`/products/${productId}`, {
            categoryId, images, name, price, options, description,
        });
    }
}
```

#### Utils
```ts
export function append<T>(items: T[], item: T) {
  return [...items, item];
}

export function remove<T>(items: T[], index: number) {
  return [
    ...items.slice(0, index),
    ...items.slice(index + 1),
  ];
}

export function update<T>(items: T[], index: number, f: (value: T) => T) {
  return items.map((item, i) => (i === index ? f(item) : item));
}
```
#### Test
```ts
import ProductFormStore from './ProductFormStore';

import fixtures from '../../fixtures';

const createProduct = jest.fn();
const updateProduct = jest.fn();

jest.mock('../services/ApiService', () => ({
  get apiService() {
    return {
      createProduct,
      updateProduct,
    };
  },
}));

const context = describe;

describe('ProductFormStore', () => {
  let store: ProductFormStore;

  beforeEach(() => {
    jest.clearAllMocks();

    store = new ProductFormStore();
  });

  describe('toggleHidden', () => {
    it('changes “hidden” to true and false alternately', () => {
      expect(store.hidden).toBeFalsy();

      store.toggleHidden();

      expect(store.hidden).toBeTruthy();

      store.toggleHidden();

      expect(store.hidden).toBeFalsy();
    });
  });

  describe('create', () => {
    const [category] = fixtures.categories;

    beforeEach(() => {
      store.reset();

      store.changeCategory(category);
      store.changeName('New Product');
      store.changePrice('123400');
      store.changeDescription('What is this?');

      store.changeImageUrl(0, 'http://example.com/images/01.jpg');
      store.addImage();
      store.changeImageUrl(1, 'http://example.com/images/02.jpg');
      store.removeImage(1);

      expect(store.images).toHaveLength(1);

      store.addOption();
      store.addOption();
      store.addOption();
      store.removeOption(2);
      store.changeOptionName(0, 'Color');
      store.changeOptionName(1, 'Size');

      expect(store.options).toHaveLength(2);

      store.addOptionItem(0);
      store.addOptionItem(0);
      // 숫자만 연속되는 부분이  혼란스러우면 object를 이용해 Named Parameter처럼 꾸며주자.
      store.removeOptionItem(0, 2);
      store.changeOptionItemName(0, 0, 'Black');
      store.changeOptionItemName(0, 1, 'White');

      expect(store.options[0].items).toHaveLength(2);

      store.changeOptionItemName(1, 0, 'Free');

      expect(store.valid).toBeTruthy();

      store.toggleHidden();
    });

    context('when API responds with success', () => {
      it('sets done is true', async () => {
        await store.create();

        expect(createProduct).toBeCalled();

        expect(store.done).toBeTruthy();
        expect(store.error).toBeFalsy();
      });
    });

    context('when API responds with error', () => {
      beforeEach(() => {
        createProduct.mockRejectedValue(Error('Create Product API error!'));
      });

      it('sets error is true', async () => {
        await store.create();

        expect(createProduct).toBeCalled();

        expect(store.done).toBeFalsy();
        expect(store.error).toBeTruthy();
      });
    });
  });

  describe('update', () => {
    const [product] = fixtures.products;

    beforeEach(() => {
      store.setProduct(JSON.parse(JSON.stringify(product)));

      store.changeName('New Name');
    });

    context('when API responds with success', () => {
      it('sets done is true', async () => {
        await store.update();

        expect(updateProduct).toBeCalled();

        expect(store.done).toBeTruthy();
        expect(store.error).toBeFalsy();
      });
    });

    context('when API responds with error', () => {
      beforeEach(() => {
        updateProduct.mockRejectedValue(Error('Update Product API error!'));
      });

      it('sets error is true', async () => {
        await store.update();

        expect(updateProduct).toBeCalled();

        expect(store.done).toBeFalsy();
        expect(store.error).toBeTruthy();
      });
    });
  });
});
```