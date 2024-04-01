## 목표
### - Login

---

## 내용
### 1. Login

- 프론트엔드 입장에서 로그인은 사용자의 username(여기서는 email)과 password를 백엔드로 전송해서 Access Token(여기서는 JWT를 사용)를 얻는 과정이다.
- 이렇게 얻은 Access Token을 관리하는 방법은 여러 가지가 있지만, 여기서는 usehooks-ts의 useLocalStorage를 사용해서 전역적으로 동기화한다.
- LoginPage를 만들어 LoginForm과 LoginFormStore를 사용하고, Access Token이 바뀌었을 때 홈(/)으로 리다이렉션한다.

#### 로그인 보여주기

```tsx
import { useEffect } from 'react';

import { useNavigate } from 'react-router-dom';

import LoginForm from '../components/login/LoginForm';

import useLoginFormStore from '../hooks/useLoginFormStore';

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
    <LoginForm />
  );
}
```
```tsx
import React, { useEffect } from 'react';

import styled from 'styled-components';

import TextBox from '../ui/TextBox';
import Button from '../ui/Button';

import useAccessToken from '../../hooks/useAccessToken';
import useLoginFormStore from '../../hooks/useLoginFormStore';

const Container = styled.div`
  h2 {
    margin-bottom: 1rem;
    font-size: 4rem;
  }

  button {
    margin-left: 10.5rem;
  }

  p {
    margin-block: 1rem;
  }
`;

export default function LoginForm() {
  const { setAccessToken } = useAccessToken();

  const [{
    email, password, valid, error, accessToken,
  }, store] = useLoginFormStore();

  useEffect(() => {
    if (accessToken) {
      setAccessToken(accessToken);
    }
  }, [accessToken]);

  const handleChangeEmail = (value: string) => {
    store.changeEmail(value);
  };

  const handleChangePassword = (value: string) => {
    store.changePassword(value);
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    store.login();
  };

  return (
    <Container>
      <h2>로그인</h2>
      <form onSubmit={handleSubmit}>
        <TextBox
          label="E-mail"
          placeholder="tester@example.com"
          value={email}
          onChange={handleChangeEmail}
        />
        <TextBox
          label="Password"
          type="password"
          value={password}
          onChange={handleChangePassword}
        />
        <Button type="submit" disabled={!valid}>
          로그인
        </Button>
        {error && (
          <p>로그인 실패</p>
        )}
      </form>
    </Container>
  );
}
```

```tsx
import React, { useRef } from 'react';

import styled from 'styled-components';

const Container = styled.div`
  margin-block: .5rem;

  label {
    display: inline-block;
    width: 10rem;
    margin-right: .5rem;
    text-align: right;
    vertical-align: middle;
  }

  input {
    width: 20rem;
  }
`;

type TextBoxProps = {
    label: string;
    placeholder?: string;
    type?: 'text' | 'number' | 'password'; // ← 계속해서 지원할 타입을 쭉 써주자.
    value: string;
    onChange: (value: string) => void;
}

export default function TextBox({
                                    label, placeholder = undefined, type = 'text', value, onChange,
                                }: TextBoxProps) {
    const id = useRef(`textbox-${Math.random().toString().slice(2)}`);

    const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
        onChange(event.target.value);
    };

    return (
        <Container>
            <label htmlFor={id.current}>
                {label}
            </label>
            <input
                id={id.current}
                type={type}
                placeholder={placeholder}
                value={value}
                onChange={handleChange}
            />
        </Container>
    );
}
```

```tsx
import styled from 'styled-components';

type ButtonProps = {
    type?: 'button' | 'submit' | 'reset';
}

const Button = styled.button.attrs<ButtonProps>((props) => ({
    type: props.type ?? 'button',
}))`
  border: .1rem solid ${(props) => props.theme.colors.primary};
  background: transparent;
  color: ${(props) => props.theme.colors.primary};
  cursor: pointer;

  :disabled {
    filter: grayscale(80%);
    cursor: not-allowed;
  }
`;

export default Button;
```

#### Header 수정

```tsx
export default function Header() {
  const { accessToken } = useAccessToken();

  const { categories } = useFetchCategories();

  const handleClickLogout = async () => {
    // TODO: 로그아웃
  };

  return (
    <Container>
      <h1>Shop</h1>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/products">Products</Link>
            {!!categories.length && (
              <ul>
                {categories.map((category) => (
                  <li key={category.id}>
                    <Link to={`/products?categoryId=${category.id}`}>
                      {category.name}
                    </Link>
                  </li>
                ))}
              </ul>
            )}
          </li>
          {accessToken ? (
            <>
              <li>
                <Link to="/cart">Cart</Link>
              </li>
              <li>
                <Button onClick={handleClickLogout}>
                  Logout
                </Button>
              </li>
            </>
          ) : (
            <li>
              <Link to="/login">Login</Link>
            </li>
          )}
        </ul>
      </nav>
    </Container>
  );
}
```

#### AddToCartForm
```tsx
import { Link } from 'react-router-dom';

import styled from 'styled-components';

import Options from './Options';
import Quantity from './Quantity';
import Price from './Price';
import SubmitButton from './SubmitButton';

import useAccessToken from '../../../hooks/useAccessToken';

const Container = styled.div`
  margin-bottom: 2rem;
  line-height: 1.8;
`;

export default function AddToCartForm() {
  const { accessToken } = useAccessToken();

  if (!accessToken) {
    return (
      <Container>
        주문하려면
        {' '}
        <Link to="/login">로그인</Link>
        하세요
      </Container>
    );
  }

  return (
    <Container>
      <Options />
      <Quantity />
      <Price />
      <SubmitButton />
    </Container>
  );
}
```

```json 
{ path: '/login', element: <LoginPage /> }, // routes에 추가.
'react/require-default-props': [2, { functions: 'defaultArguments' }], //기본 Props 값을 지원하는 ESLint 룰 추가.
```

#### hooks
```tsx
export default function useAccessToken() {
  const [accessToken, setAccessToken] = useLocalStorage('accessToken', '');
  
    useEffect(() => {
        apiService.setAccessToken(accessToken);
    }, [accessToken]);

  return { accessToken, setAccessToken };
}
```

#### Store
```tsx
@singleton()
@Store()
export default class LoginFormStore {
  email = '';

  password = '';

  error = false;

  accessToken = '';

  get valid() {
    return this.email.includes('@') && !!this.password;
  }

  @Action()
  changeEmail(email: string) {
    this.email = email;
  }

  @Action()
  changePassword(password: string) {
    this.password = password;
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
    this.password = '';
    this.error = false;
    this.accessToken = '';
  }

  async login() {
    try {
      const accessToken = await apiService.login({
        email: this.email,
        password: this.password,
      });

      this.setAccessToken(accessToken);
    } catch (e) {
      this.setError();
    }
  }
}
```
```tsx
import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import LoginFormStore from '../stores/LoginFormStore';

export default function useLoginFormStore() {
  const store = container.resolve(LoginFormStore);
  return useStore(store);
}
```

#### API

```ts
export default class ApiService {
    private accessToken = '';

    setAccessToken(accessToken: string) {
        if (accessToken === this.accessToken) {
            return;
        }

        const authorization = accessToken ? `Bearer ${accessToken}` : undefined;

        this.instance = axios.create({
            baseURL: API_BASE_URL,
            headers: { Authorization: authorization },
        });

        this.accessToken = accessToken;
    }

    async login({ email, password }: {
        email: string;
        password: string;
    }): Promise<string> {
        const { data } = await this.instance.post('/session', { email, password });
        const { accessToken } = data;
        return accessToken;
    }
}

export const apiService = new ApiService();
```

#### tests

```tsx
let accessToken = '';

jest.mock('../hooks/useAccessToken', () => () => ({
  get accessToken() {
    return accessToken;
  },
}));

jest.mock('../hooks/useFetchCategories', () => () => ({
  categories: [],
}));

const context = describe;

describe('Header', () => {
  it('renders title', () => {
    render(<Header />);

    screen.getByText(/Shop/);
  });

  it('renders basic link', () => {
    render(<Header />);

    screen.getByRole('link', { name: 'Home' });
  });

  context("when the current user isn't logged in", () => {
    beforeEach(() => {
      accessToken = '';
    });

    it('renders “Login” link', () => {
      render(<Header />);

      screen.getByRole('link', { name: 'Login' });
    });
  });

  context('when the current user is logged in', () => {
    beforeEach(() => {
      accessToken = 'ACCESS-TOKEN';
    });

    it('renders “Cart” link', () => {
      render(<Header />);

      screen.getByRole('link', { name: 'Cart' });
    });

    it('renders “Logout” button', () => {
      render(<Header />);

      screen.getByRole('button', { name: 'Logout' });
    });
  });
});
```

```tsx
let accessToken = '';

jest.mock('../../../hooks/useAccessToken', () => () => ({
  get accessToken() {
    return accessToken;
  },
}));

const context = describe;

describe('AddToCartForm', () => {
  const [product] = fixtures.products;

  beforeEach(async () => {
    iocContainer.clearInstances();

    const productDetailStore = iocContainer.resolve(ProductDetailStore);
    await productDetailStore.fetchProduct({ productId: product.id });
  });

  context("when the current user isn't logged in", () => {
    beforeEach(() => {
      accessToken = '';
    });

    it('renders message', () => {
      const { container } = render(<AddToCartForm />);

      expect(container).toHaveTextContent('주문하려면 로그인하세요');
    });
  });

  context('when the current user is logged in', () => {
    beforeEach(() => {
      accessToken = 'ACCESS-TOKEN';
    });

    it('renders “Add To Cart” button', async () => {
      render(<AddToCartForm />);

      fireEvent.click(screen.getByText('장바구니에 담기'));

      await waitFor(() => {
        screen.getByText(/장바구니에 담았습니다/);
      });
    });
  });
});
```