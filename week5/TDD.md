# TDD

## [TDD란](https://github.com/ahastudio/til/blob/main/agile/test-driven-development.md)

[TDD(Test Driven Develpoment)](http://wiki.c2.com/?TestDrivenDevelopment)란 **TDD Cycle에 따라 테스트 코드를 먼저 작성하고, 실패하고, 구현하고, 리팩터링 하는 과정을 엄격하게 지켜서 개발을 진행하는 것**을 의미합니다.

> add a test, get it to fail, and write code to pass the test (DoSimpleThings, CodeUnitTestFirst)
> remove duplication (OnceAndOnlyOnce, DontRepeatYourself, ThreeStrikesAndYouAutomate)

테스트 코드를 먼저 작성한다는 의미는 구현보다 인터페이스와 스펙을 먼저 정의한 후 개발을 진행하는 방식을 의미합니다.

```javascript
// 시그니처
add(x,y) -> number
```

여기서 인터페이스란 **위 시그니처들을 모아놓은 것**을 의미하고 스펙이란 **x와 y를 매개변수로 넣으면 +(플러스) 된다** 와 같이 정의하는 것을 의미합니다.

### TDD Cycle

1. Red
   실패하는 테스트 코드를 작성합니다. 인터페이스와 스펙에 집중합니다.\
2. Green
   재빨리 테스트를 통과시킵니다. 올바른 방법이 아니어도 괜찮습니다.\
3. Refactor
   리팩터링을 통해 코드를 올바르게 만듭니다. TDD에서 가장 중요한 부분이지만 간과될 때가 많습니다.

작은 단계를 찾고, 코드에서 피드백을 얻는게 어렵지만 중요합니다.\
**2번이 어렵다면 1번으로 돌아가서 더 작고 쉬운 문제를 정의**하고 **3번을 위해 의도를 드러내고 중복을 찾아 제거**하는 연습을 해야합니다.\
위 두 과정이 익숙하지 않으면 TDD를 하는 게 불가능하고, 일반적인 개발 또는 클린 코드를 작성하는 것 또한 어렵습니다.

### [단위테스트](https://github.com/ahastudio/til/blob/main/blog/2016/12-03-tdd-faq.md)

### [Jest를 이용한 간단한 TDD 예제](https://github.com/ahastudio/til/blob/main/jest/20201204-simple-tdd-example.md)

## [Jest](https://jestjs.io/)

### 테스트 케이스 정의 방식

1. test 함수로 개별 테스트를 나열하는 방식.
   파일명은 `app.test.ts`와 같이 `테스트할 파일명.test.확장자`로 명시합니다.
   TS 확장자를 사용할 경우 Jest에서 TypeScript 사용하도록 `jest.config.js` 파일을 작성합니다.

   ```typescript
   module.exports = {
     testEnvironment: 'jsdom',
     setupFilesAfterEnv: ['@testing-library/jest-dom/extend-expect'],
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

- app.test.ts

```typescript
test('add', () => {
  expect(add(1, 2)).toBe(3);
});
```

2. BDD(Behaviour-Driven Development) 스타일로 주체-행위 중심으로 테스트를 조직화하는 방식.
   [Given-When-Then](https://github.com/ahastudio/til/blob/main/blog/2018/12-08-given-when-then.md) 구조를 가지는 것을 권장합니다.

- BDD 스타일 정의
  - Feature : 테스트에 대상의 기능/책임을 명시한다.
  - Scenario : 테스트 목적에 대한 상황을 설명한다.
  - Given : 시나리오 진행에 필요한 값을 설정한다.
  - When : 시나리오를 진행하는데 필요한 조건을 명시한다.
  - Then : 시나리오를 완료했을 때 보장해야 하는 결과를 명시한다.

### Describe - Context - It 패턴

```typescript
const context = describe;
// Feature
describe('add', () => {
  // Scenario
  context('with no argument', () => {
    it('returns zero', () => {
      expect(add()).toBe(0);
    });
  });

  context('with only one number', () => {
    it('returns the same number', () => {
      expect(add(1)).toBe(1);
    });
  });

  context('with two numbers', () => {
    it('returns sum of two numbers', () => {
      expect(add(1, 2)).toBe(3);
    });
  });

  context('with three numbers', () => {
    it('returns sum of three numbers', () => {
      expect(add(1, 2, 3)).toBe(6);
    });
  });
});
```

## 참고 자료

- [BDD (Behaviour-Driven Development)에 대한 간략한 정리](https://www.popit.kr/bdd-behaviour-driven-development%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B0%84%EB%9E%B5%ED%95%9C-%EC%A0%95%EB%A6%AC/)

- [BDD?SDD? 팀 프로젝트 다 같이 개발, 설계하는 방법](https://yozm.wishket.com/magazine/detail/1565/)
