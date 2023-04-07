# Playwright

## [Playwright](https://playwright.dev/)

Playwright는 웹 브라우저 기반 E2E 태스트 자동화 도구입니다.\
Headless Chrome을 기반으로 한 Puppeteer를 계승하면서, 더 많은 웹 브라우저를 지원합니다.

### [Playwright 패키지 설치하기](https://github.com/microsoft/playwright)

```typescript
npm i -D @playwright/test eslint-plugin-playwright
```

1. [playwright.config.ts](https://playwright.dev/docs/test-configuration) 파일 생성하기

```typescript
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

2. tests 폴더 생성하기

- tests/.eslintrc.js 파일

```javascript
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

- tests/home.spec.ts 파일 생성하기

```typescript
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

  await Promise.all(
    [...Array(count)].map(async () => {
      await page.getByText('Increase').click();
    })
  );

  await expect(page.getByText(`${count}`)).toBeVisible();
});
```

3. 테스트 실행하기

```bash
npx playwright test

// 현재 playwright.config.ts의 headless는 CI 환경에 따라 실행됩니다.
// 화면을 렌더링 않기 때문에 속도가 좀 더 빠릅니다.
CI=true npx playwright test
```

5. .gitignore 파일에 에러 상황의 스크린샷 등이 담기는 test-results 디렉터리 추가하기

```text
/test-results/
```

## [CodeceptJs](https://codecept.io/)

좀 더 직관적인 E2E 테스팅 도구로 CodeceptJS를 사용할 수 있습니다.

### [CodeceptJs 시작하기](https://github.com/ahastudio/til/blob/main/test/20201207-codeceptjs.md)

### [CodeceptJs 사용하기](https://github.com/ahastudio/CodingLife/tree/main/20211012/react#codeceptjs-%EC%82%AC%EC%9A%A9)

## E2E(End to End) Test
