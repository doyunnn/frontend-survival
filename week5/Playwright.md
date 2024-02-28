## 목표
### - Playwright

- E2E(End to End) Test
- Headless Chrome
- Puppeteer
- Playwright
- CodeceptJS

---

## 내용
### 1. Playwright

> [Playwright](https://playwright.dev/)
>

> [Playwright Configuration](https://playwright.dev/docs/test-configuration)
>

> [Ashal의 Playwright](https://github.com/ahastudio/til/blob/main/test/playwright.md)
>

<aside>
💡 웹 브라우저 기반 E2E 테스트 자동화 도구.
Headless Chrome을 기반으로 한 Puppeteer를 계승하면서, 더 많은 웹 브라우저를 지원한다.

</aside>

Playwright 패키지 설치

```bash
npm i -D @playwright/test eslint-plugin-playwright
```

`playwright.config.ts` 파일

```jsx
import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
	testDir: './tests',
	retries: 0,
	use: {
		baseURL: 'http://localhost:8080',
		headless: !!process.env.CI,
		screenshot: 'only-on-failure',
	},
};

export default config;
```

`tests/.eslintrc.js` 파일

```jsx
module.exports = {
	env: {
		jest: false,
	},
	extends: ['plugin:playwright/playwright-test'],
	rules: {
		'import/no-extraneous-dependencies': 'off',
	},
};
```

`tests/home.spec.ts`

```jsx
import { test, expect } from '@playwright/test';

test('Filter products', async ({ page }) => {
	await page.goto('/');

	await expect(page.getByText('Apple')).toBeVisible();

	const searchInput = page.getByLabel('Search');

	await searchInput.fill('a');

	await expect(page.getByText('Apple')).toBeVisible();

	await searchInput.fill('aa');

	await expect(page.getByText('Apple')).toBeHidden();
});

test('Click the “Increase” button', async ({ page }) => {
	await page.goto('/');

	const count = 13;

	await Promise.all((
		[...Array(count)].map(async () => {
			await page.getByText('Increase').click();
		})
	));

	await expect(page.getByText(`${count}`)).toBeVisible();
});
```

테스트 실행

```bash
npx playwright test
```

`.gitignore` 파일에 에러 상황의 스크린샷 등이 담기는 `test-results` 디렉터리 추가.

```
/test-results/
```

- 인간 친화적인 E2E 테스팅 도구로 CodeceptJS가 있다.

- [CodeceptJS](https://codecept.io/)
- [CodeceptJS 3 시작하기](https://github.com/ahastudio/til/blob/main/test/20201207-codeceptjs.md)
- [CodeceptJS 사용](https://github.com/ahastudio/CodingLife/tree/main/20211012/react#codeceptjs-사용)