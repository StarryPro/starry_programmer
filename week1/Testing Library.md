# Testing Library

## 강의노트

### Jest

[Jest 공식문서](https://jestjs.io/docs/getting-started)

#### 기본적인 테스트 코드

```typescript
function add(x: number, y: number): number {
  return x + y;
}

test('add', () => {
  expect(add(1, 2)).toBe(3);
});
```

#### BDD 스타일의 테스트 코드

표현력이 좋아지고, 좀 더 고민할 기회를 제공합니다.

```typescript
function add(x: number, y: number): number {
  return x + y;
}

describe('add', () => {
  it('return sum of two numbers', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

### React Testing Library

## 학습 키워드

* Jest
* Describe-Context-It 패턴
* React Testing Library
