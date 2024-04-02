## 목표
### - Signup

---

## 내용
### 1. Signup

#### pages
```tsx
export default function SignupPage() {
  const navigate = useNavigate();

  const [{ accessToken }, store] = useSignupFormStore();

  useEffect(() => {
    store.reset();
  }, []);

  useEffect(() => {
    if (accessToken) {
      store.reset();
      navigate('/signup/complete');
    }
  }, [accessToken]);

  return (
    <SignupForm />
  );
}
```
```tsx
export default function SignupCompletePage() {
  return (
    <p>
      회원 가입이 완료되었습니다.
    </p>
  );
}
```

#### components
```tsx
export default function SignupForm() {
  const { setAccessToken } = useAccessToken();

  const [{
    email, name, password, passwordConfirmation, valid, error, accessToken,
  }, store] = useSignupFormStore();

  useEffect(() => {
    if (accessToken) {
      setAccessToken(accessToken);
    }
  }, [accessToken]);

  const handleChangeEmail = (value: string) => {
    store.changeEmail(value);
  };

 const handleChangeName = (value: string) => {
    store.changeName(value);
  };

  const handleChangePassword = (value: string) => {
    store.changePassword(value);
  };

  const handleChangePasswordConfirmation = (value: string) => {
    store.changePasswordConfirmation(value);
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    store.signup();
  };

  return (
    <Container>
      <h2>회원 가입</h2>
      <form onSubmit={handleSubmit}>
        <TextBox
          label="E-mail"
          placeholder="tester@example.com"
          value={email}
          onChange={handleChangeEmail}
        />
        <TextBox
          label="Name"
          value={name}
          onChange={handleChangeName}
        />
        <TextBox
          label="Password"
          type="password"
          value={password}
          onChange={handleChangePassword}
        />
        <TextBox
          label="Password Confirmation"
          type="password"
          value={passwordConfirmation}
          onChange={handleChangePasswordConfirmation}
        />
        <Button type="submit" disabled={!valid}>
          회원 가입
        </Button>
        {error && (
          <p>회원 가입 실패</p>
        )}
      </form>
    </Container>
  );
}
```

#### stores
```tsx
@singleton()
@Store()
export default class SignupFormStore {
  email = '';

  name = '';

  password = '';

  passwordConfirmation = '';

  error = false;

  accessToken = '';

  get valid() {
    return this.email.includes('@')
      && !!this.name
      && this.password.length >= 4
      && this.password === this.passwordConfirmation;
  }

  @Action()
  changeEmail(email: string) {
    this.email = email;
  }

  @Action()
  changeName(name: string) {
    this.name = name;
  }

  @Action()
  changePassword(password: string) {
    this.password = password;
  }

  @Action()
  changePasswordConfirmation(password: string) {
    this.passwordConfirmation = password;
  }

  @Action()
  setAccessToken(accessToken: string) {
    this.accessToken = accessToken;
  }

  @Action()
  setError() {
    this.error = true;
  }

  @Action()
  reset() {
    this.email = '';
    this.name = '';
    this.password = '';
    this.passwordConfirmation = '';
    this.error = false;
    this.accessToken = '';
  }

  async signup() {
    try {
      const accessToken = await apiService.signup({
        email: this.email,
        name: this.name,
        password: this.password,
      });

      this.setAccessToken(accessToken);
    } catch (e) {
      this.setError();
    }
  }
}
```

#### hooks
```tsx
export default function useSignupFormStore() {
  const store = container.resolve(SignupFormStore);
  return useStore(store);
}
```

```ts
export default class ApiService {
    // ...

    async signup({ email, name, password }: {
        email: string;
        name: string;
        password: string;
    }): Promise<string> {
        const {data} = await this.instance.post('/users', {
            email, name, password,
        });
        const {accessToken} = data;
        return accessToken;
    }
}
```

#### routes
```ts
{ path: '/signup', element: <SignupPage /> },
{ path: '/signup/complete', element: <SignupCompletePage /> },
```

#### others
```tsx
<p>
  <Link to="/signup">
    회원 가입
  </Link>
</p>
```