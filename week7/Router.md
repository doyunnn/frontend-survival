## 목표
### - Router

- ReactRouter - RouterProvider

---

## 내용
### 1. Router

React Router 버전 6.4부터 지원하는, 라우터 객체를 만들어서 쓰는 방법.

라우팅 정보만 별도의 파일로 관리.

```tsx
import { Outlet } from 'react-router-dom';

function Layout() {
	return (
		<div>
			<Header />
			<Outlet />
			<Footer />
		</div>
	);
}

const routes = [
	{
		element: <Layout />,
		children: [
			{ path: '/', element: <HomePage /> },
			{ path: '/about', element: <AboutPage /> },
		],
	},
];

export default routes;
```

App 컴포넌트를 거치지 않고 바로 브라우저 라우터 만들어서 사용.

> [createBrowserRouter](https://reactrouter.com/en/main/routers/create-browser-router)
>

> [RouterProvider](https://reactrouter.com/en/main/routers/router-provider)
>

```tsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import routes from './routes';

const router = createBrowserRouter(routes);

root.render((
	<React.StrictMode>
		<RouterProvider router={router} />
	</React.StrictMode>
));
```

메모리 라우터 만들어서 테스트.

> [createMemoryRouter](https://reactrouter.com/en/main/routers/create-memory-router)
>

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
});
```
