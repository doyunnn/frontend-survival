## 목표
### - Check User

---

## 내용
### 1. Check User

#### Access Token 확인

- 사이트에 접속하거나 새로 고침 등을 했을 땐 Access Token을 확인하고, 사용자 정보를 얻는 작업이 필요하다. 만약 회원 정보를 얻는 API가 실패하면 Access Token에 문제가 있는 상황이니 로그아웃시킨다.

- ApiService에 fetchCurrentUser 메서드를 추가한다.
```ts

export default class ApiService {
    // ...

    async fetchCurrentUser(): Promise<{
        id: string;
        name: string;
    }> {
        const { data } = await this.instance.get('/users/me');
        const { id, name } = data;
        return { id, name };
    }
}
```

#### Hook
```ts
export default function useCheckAccessToken(): void {
    const { accessToken, setAccessToken } = useAccessToken();

    useEffect(() => {
        const fetchCurrentUser = async () => {
            try {
                await apiService.fetchCurrentUser();
            } catch (e) {
                setAccessToken('');
            }
        };

        fetchCurrentUser();
    }, [accessToken, setAccessToken]);
}
```

#### Layout에서 useCheckAccessToken을 호출한다.
```tsx
export default function Layout() {
  useCheckAccessToken();

  return (
    <Container>
      <Header />
      <Outlet />
    </Container>
  );
}
```
