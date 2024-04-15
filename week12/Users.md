## 목표
### - Users

---

## 내용
### 1. Users

#### UI

- 사용자 관리는 사실 대단히 복잡할 수 있다.
- 사용자와 관련된 정보가 많기 때문에 이를 모두 다루는 것 자체도 문제고, 고객 지원 업무에서도 사용자는 핵심 축이 되기 때문에 제대로 된 사용자 관리는 정말 복잡한 일이다.
- 하지만 여기서는 단순한 CRUD, 그것도 사용자 목록 정도만 확인하는 작업에 집중하겠다.
- SWR를 써서 단순 작업을 얼마나 간단히 처리할 수 있는지 확인해 보자.

```tsx
import styled from 'styled-components';

import useFetchUsers from '../hooks/useFetchUsers';

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
`;

export default function UserListPage() {
  const { users, loading, error } = useFetchUsers();

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
      <h2>Users</h2>
      <table>
        <thead>
          <tr>
            <th>이름</th>
            <th>이메일</th>
            <th>역할</th>
          </tr>
        </thead>
        <tbody>
          {users.map((user) => (
            <tr key={user.id}>
              <td>{user.name}</td>
              <td>{user.email}</td>
              <td>{user.role}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </Container>
  );
}
```

#### Hooks
```ts
import useSWR from 'swr';

import { apiService } from '../services/ApiService';

const API_BASE_URL = process.env.API_BASE_URL || 'http://localhost:3000/admin';

export default function useFetch<Data>(path: string) {
  const url = `${API_BASE_URL}${path}`;

  const {
    data, error, isLoading, mutate,
  } = useSWR<Data>(url, apiService.fetcher());

  return {
    data,
    error,
    loading: isLoading,
    mutate,
  };
}
```
```ts
import useFetch from './useFetch';

import { User } from '../types';

export default function useFetchUsers() {
  const { data, error, loading } = useFetch<{
    users: User[];
  }>('/users');

  return {
    users: data?.users ?? [],
    error,
    loading,
  };
}
```

#### Service
```ts
fetcher() {
  return async (url: string) => {
    const { data } = await this.instance.get(url);
    return data;
  };
}
```