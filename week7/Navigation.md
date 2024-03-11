## 목표
### - Navigation

- Web APIs - History
- React Router - NavLink, Link, Navigate, useNavigate

---

## 내용
### 1. Navigation

### History.pushState

> [History.pushState()](https://developer.mozilla.org/ko/docs/Web/API/History/pushState)
>

```tsx
const handleClick = (event) => {
    event.preventDefault();
    const state = {};
    const title = '';
    const url = '/about';

    history.pushState(state, title, url);
};
```

### Link

> [Link](https://reactrouter.com/en/main/components/link)
>

```tsx
function Header() {

return (
	<header>
		<nav>
			<ul>
				<li><Link to="/">Home</Link></li>
				<li><Link to="/about">About</Link></li>
			</ul>
		</nav>
	</header>
	);
}
```

### NavLink

> [NavLink](https://reactrouter.com/en/main/components/nav-link)
>

```tsx
function Header() {
	return (
		<header>
			<nav>
				<ul>
					<li><NavLink to="/">Home</NavLink></li>
					<li><NavLink to="/about">About</NavLink></li>
				</ul>
			</nav>
		</header>
	);
}
```

- class 상태가 active 상태로 변경된다
- 이에 따라 스타일 변경 가능 active { font-size: 2rem; }

### Navigate

> [Navigate](https://reactrouter.com/en/main/components/navigate)
>

```tsx
import { Navigate } from 'react-router-dom';

export default function LoginPage() {
	return (
		<Navigate to="/" />
	);
}
```



### useNavigate

> [useNavigate](https://reactrouter.com/en/main/hooks/use-navigate)
>

```tsx
import { useNavigate } from 'react-router-dom';

export default function Footer() {
	const navigate = useNavigate();
	
	const handleClick = () => {
		navigate('/about');
	};
	
	return (
		<footer>
			<button type="button" onClick={handleClick}>
				Press
			</button>
		</footer>
	);
}
```
#### 테스트에서 “ReferenceError: Request is not defined” 에러가 나면 “whatwg-fetch”를 임포트해서 해결할 수 있다.

```tsx
describe('routes', () => {	
	function renderRouter(path: string) {
		const router = createMemoryRouter(routes, { initialEntries: [path] });
		render(<RouterProvider router={router} />);
	}
	
	context('when the current path is “/”', () => {
		it('renders the home page', () => {
			renderRouter('/');
	
			screen.getByText(/Hello/);
		});
	});
	
	context('when the current path is “/about”', () => {
		it('renders the about page', () => {
			renderRouter('/about');
	
			screen.getByText(/About/);
		});
	});

    context('when the current path is “/about”', () => {
        it('renders the logout page', () => {
            renderRouter('/logout');

            screen.getByText(/Welcome/);
        });
    });
    // -> Requsst is not defined 에러 발생
});
```


```ts
  //setupTests.ts
    import 'whatwg-fetch';
```