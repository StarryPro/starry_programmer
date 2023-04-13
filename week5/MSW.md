# MSW

## [MSW(Mock Service Worker)](https://mswjs.io/)

MSW(Mock [Service Worker](https://developer.mozilla.org/ko/docs/Web/API/Service_Worker_API))를 사용하면 코드 레벨이 아니라 네트워크 레벨에서 가짜로 구현할 수 있습니다.

### [MSW 패키지 설치](https://mswjs.io/docs/getting-started/integrate/node)

```bash
npm i -D msw
```

1. jest.config.js 파일의 “setupFilesAfterEnv” 속성에 setupTests.ts 파일 추가합니다.

```javascript
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: [
    '@testing-library/jest-dom/extend-expect',
    '<rootDir>/src/setupTests.ts',
  ],
  transform: {
    '^.+\\.(t|j)sx?$': [
      '@swc/jest',
      {
        jsc: {
          parser: {
            syntax: 'typescript',
            jsx: true,
            decorators: true,
          },
          transform: {
            react: {
              runtime: 'automatic',
            },
          },
        },
      },
    ],
  },
};
```

2. setupTests.ts 파일을 생성합니다.

- src/setupTests.ts

```typescript
import server from './mocks/server';

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));

afterAll(() => server.close());

afterEach(() => server.resetHandlers());
```

3.server.ts 파일을 생성합니다.

- src/mocks/server.ts

```typescript
import { setupServer } from 'msw/node';

import handlers from './handlers';

const server = setupServer(...handlers);

export default server;
```

4. handlers.ts 파일을 생성합니다.

- src/mocks/handlers.ts

```typescript
import { rest } from 'msw';

const BASE_URL = 'http://localhost:3000';

const handlers = [
  rest.get(`${BASE_URL}/products`, (req, res, ctx) => {
    const products = [
      {
        category: 'Fruits',
        price: '$1',
        stocked: true,
        name: 'Apple',
      },
    ];

    return res(ctx.status(200), ctx.json({ products }));
  }),
];

export default handlers;
```

5. app.test.ts 파일

```typescript
import { render, screen, waitFor } from '@testing-library/react';

import App from './App';

// jest.mock 불필요.

test('App', async () => {
  render(<App />);

  await waitFor(() => {
    screen.getByText('Apple');
  });
});
```

너무 본격적으로 코딩하면 사실상 백엔드를 개발하게 되니, 이 부분에 주의해야 합니다.\
MSW는 테스트 환경(Node.js 기반) 외에 웹 브라우저도 지원하기 때문에, API 스펙은 나왔지만 아직 구현되지 않은 경우 임시로 사용할 수도 있습니다.\
단순히 임시 서버를 만들 거라면 Express를 쓰는 게 더 낫지만, 테스트 코드도 지원하면서 겸사겸사 웹 브라우저를 지원하는 용도로는 나쁘지 않은 선택입니다.

### [GitHub에서 만든 fetch polyfill](https://github.com/github/fetch)

[polyfill](https://developer.mozilla.org/ko/docs/Glossary/Polyfill)은 기본적으로 지원하지 않는 이전 브라우저에서 최신 기능을 제공하는 데 필요한 코드 (일반적으로 웹의 JavaScript)입니다.\
fetch polyfill은 fetch 기능을 수행하지 못하는 브라우저에서 fetch를 사용할 수 있도록 해주는 코드입니다.


## 참고 자료

- [똑똑하게 브라우저 Polyfill 관리하기](https://toss.tech/article/smart-polyfills)