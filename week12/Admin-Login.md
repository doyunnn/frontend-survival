## 목표
### - Admin Login

---

## 내용
### 1. Admin Login

#### routes

```ts
const routes = [
  { path: '/login', element: <LoginPage /> },
  {
    element: <Layout />,
    children: [
      { path: '/', element: <HomePage /> },
      { path: '/users', element: <UserListPage /> },
      { path: '/categories', element: <CategoryListPage /> },
      { path: '/categories/new', element: <CategoryNewPage /> },
      { path: '/categories/:id/edit', element: <CategoryEditPage /> },
      { path: '/products', element: <ProductListPage /> },
      { path: '/products/new', element: <ProductNewPage /> },
      { path: '/products/:id', element: <ProductDetailPage /> },
      { path: '/products/:id/edit', element: <ProductEditPage /> },
      { path: '/orders', element: <OrderListPage /> },
      { path: '/orders/:id', element: <OrderDetailPage /> },
      { path: '/orders/:id/edit', element: <OrderEditPage /> },
    ],
  },
];
```

#### UI

```tsx
import { useEffect } from 'react';

import { useNavigate } from 'react-router-dom';

import styled from 'styled-components';

import LoginForm from '../components/LoginForm';

import useLoginFormStore from '../hooks/useLoginFormStore';

const Container = styled.div`
  margin: 5rem auto;
  width: 90%;
`;

export default function LoginPage() {
  const navigate = useNavigate();

  const [{ accessToken }, store] = useLoginFormStore();

  useEffect(() => {
    store.reset();
  }, []);

  useEffect(() => {
    if (accessToken) {
      store.reset();
      navigate('/');
    }
  }, [accessToken]);

  return (
    <Container>
      <LoginForm />
    </Container>
  );
}
```

```tsx
import { Outlet } from 'react-router-dom';

import styled from 'styled-components';

import Header from './Header';

import useCheckAccessToken from '../hooks/useCheckAccessToken';

const Container = styled.div`
  margin-inline: auto;
  width: 90%;
`;

export default function Layout() {
  const ready = useCheckAccessToken();

  if (!ready) {
    return null;
  }

  return (
    <Container>
      <Header />
      <Outlet />
    </Container>
  );
}
```

```tsx
import { useEffect, useState } from 'react';

import { useNavigate } from 'react-router-dom';

import useAccessToken from './useAccessToken';

import { apiService } from '../services/ApiService';

export default function useCheckAccessToken(): boolean {
  // Access Token 검증이 끝나면 ready가 된다.
  const [ready, setReady] = useState(false);

  const { accessToken, setAccessToken } = useAccessToken();

  const navigate = useNavigate();

  useEffect(() => {
    const fetchCurrentUser = async () => {
      try {
        await apiService.fetchCurrentUser();
        setReady(true);  // 👈 확인됨!
      } catch (e) {
        setAccessToken('');
      }
    };

    fetchCurrentUser();
  }, [accessToken, setAccessToken]);

  // 기존과 다른 부분 —---------
  useEffect(() => {
    if (!accessToken) {
      navigate('/login');
    }
  }, [accessToken, navigate]);

  return ready;
}
```

```tsx
const Container = styled.header`
  margin-bottom: 2rem;

  h1 {
    font-size: 4rem;
  }

  ul {
      display: flex;

      li {
        height: 4rem;
        line-height: 4rem;
        margin-right: 1rem;
    }
  }
`;
export default function Header() {
  const navigate = useNavigate();

  const { setAccessToken } = useAccessToken();

  const handleClickLogout = async () => {
    await apiService.logout();
    setAccessToken('');
    navigate('/');
  };

  return (
    <Container>
      <h1>Shop Administrator</h1>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/users">Users</Link>
          </li>
          <li>
            <Link to="/categories">Categories</Link>
          </li>
          <li>
            <Link to="/products">Products</Link>
          </li>
          <li>
            <Link to="/orders">Orders</Link>
          </li>
          <li>
            <Button onClick={handleClickLogout}>
              Logout
            </Button>
          </li>
        </ul>
      </nav>
    </Container>
  );
}
```