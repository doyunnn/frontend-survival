## 목표
### - Categories

---

## 내용
### 1. Categories

#### List
```tsx
import { Link } from 'react-router-dom';

import styled from 'styled-components';

import useFetchCategories from '../hooks/useFetchCategories';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  table {
    th, td {
      padding: .5rem;
    }
  }

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

export default function CategoryListPage() {
  const { categories, loading, error } = useFetchCategories();

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
      <h2>Categories</h2>
      <table>
        <thead>
          <tr>
            <th>이름</th>
            <th>표시</th>
            <th>행동</th>
          </tr>
        </thead>
        <tbody>
          {categories.map((category) => (
            <tr key={category.id}>
              <td>{category.name}</td>
              <td>{category.hidden ? '감춤' : '보임'}</td>
              <td>
                <Link to={`/categories/${category.id}/edit`}>수정</Link>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
      <Link to="/categories/new">
        카테고리 추가
      </Link>
    </Container>
  );
}
```

#### Create
```tsx
import { useNavigate } from 'react-router-dom';

import { Controller, useForm } from 'react-hook-form';

import styled from 'styled-components';

import Button from '../components/ui/Button';

import useFetchCategories from '../hooks/useFetchCategories';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  [type=submit] {
    display: block;
    margin-block: 1rem;
  }
`;

export default function CategoryNewPage() {
  const navigate = useNavigate();

  const { createCategory } = useFetchCategories();

  type FormValues = {
    name: string;
  };

  const { handleSubmit, control } = useForm<FormValues>();

  const onSubmit = async (data: FormValues) => {
    await createCategory({
      name: data.name,
    });
    navigate('/categories');
  };

  return (
    <Container>
      <h2>New Category</h2>
      <form onSubmit={handleSubmit(onSubmit)}>
        <Controller
          control={control}
          name="name"
          defaultValue=""
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-name">이름</label>
              <input
                id="input-name"
                value={value}
                onChange={onChange}
              />
            </div>
          )}
        />
        <Button type="submit">
          등록
        </Button>
      </form>
    </Container>
  );
}
```

#### Update
```tsx
import { useNavigate, useParams } from 'react-router-dom';

import { Controller, useForm } from 'react-hook-form';

import styled from 'styled-components';

import Button from '../components/ui/Button';

import useFetchCategory from '../hooks/useFetchCategory';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  [type=submit] {
    display: block;
    margin-block: 1rem;
  }
`;

export default function CategoryEditPage() {
  const params = useParams();

  const categoryId = String(params.id);

  const navigate = useNavigate();

  const {
    category, loading, error, updateCategory,
  } = useFetchCategory({ categoryId });

  type FormValues = {
    name: string;
    hidden: boolean;
  };

  const { handleSubmit, control } = useForm<FormValues>();

  const onSubmit = async (data: FormValues) => {
    await updateCategory({
      name: data.name,
      hidden: data.hidden,
    });
    navigate('/categories');
  };

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error || !category) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Container>
      <h2>Edit Category</h2>
      <form onSubmit={handleSubmit(onSubmit)}>
        <Controller
          control={control}
          name="name"
          defaultValue={category.name}
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-name">이름</label>
              <input
                id="input-name"
                value={value}
                onChange={onChange}
              />
            </div>
          )}
        />
        <Controller
          control={control}
          name="hidden"
          defaultValue={category.hidden}
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-hidden">감춤</label>
              <input
                id="input-hidden"
                type="checkbox"
                checked={value}
                onChange={onChange}
              />
            </div>
          )}
        />
        <Button type="submit">
          수정
        </Button>
      </form>
    </Container>
  );
}
```

#### hooks
```ts
import useFetch from './useFetch';

import { apiService } from '../services/ApiService';

import { Category } from '../types';

export default function useFetchCategories() {
  const {
    data, error, loading, mutate,
  } = useFetch<{
    categories: Category[];
  }>('/categories');

  return {
    categories: data?.categories ?? [],
    error,
    loading,
    async createCategory({ name }: {
      name: string;
    }) {
      await apiService.createCategory({ name });
      mutate();
    },
  };
}
```
```ts
import useFetch from './useFetch';

import { apiService } from '../services/ApiService';

import { Category } from '../types';

export default function useFetchCategory({ categoryId }: {
  categoryId: string;
}) {
  const url = `/categories/${categoryId}`;

  const {
    data, error, loading, mutate,
  } = useFetch<Category>(url);

  return {
    category: data,
    error,
    loading,
    async updateCategory({ name, hidden }: {
      name: string;
      hidden: boolean;
    }) {
      await apiService.updateCategory({ categoryId, name, hidden });
      mutate();
    },
  };
}
```

#### API
```ts
export default class ApiService {
  // ...

    async createCategory({ name }: {
        name: string;
    }): Promise<void> {
        await this.instance.post('/categories', { name });
    }

    async updateCategory({ categoryId, name, hidden }: {
        categoryId: string;
        name: string;
        hidden: boolean;
    }): Promise<void> {
        await this.instance.patch(`/categories/${categoryId}`, { name, hidden });
    }
}
```
