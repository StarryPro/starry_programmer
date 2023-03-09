# 2. TypeScript

## 강의 노트

간단히 `REPL`을 사용하고 싶다면 ts-node를 실행하면 됩니다.

```bash
npx ts-node
```

### 타입 지정

```typescript
// 원시 타입 : string, number 그리고 boolean
let name: string;
let age: number;

name = '한성욱';
age = 30;

name = 13;
// <repl>.ts:6:1 - error TS2322: Type 'number' is not assignable to type 'string'.
age = '한성욱';
// <repl>.ts:7:1 - error TS2322: Type 'string' is not assignable to type 'number'.

let human: {
  name: string;
  age: number;
};

human = { name: '한프로', age 15 };

// 정해진 값으로 타입을 지정할 수 있습니다. 이런 타입은 Union에서 유용하게 쓰입니다.
let category: 'food';
category = 'food';

// 배열은 타입 뒤에 대괄호를 붙여주면 됩니다.
let numbers: number[];
numbers = [1,2,3];

// 배열보다 깐깐하게 타입을 관리하고 싶다면 Tuple을 사용합니다.
let pair: [string, number];
pair = ['hp', 256]
```

#### ![타입 정의](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html#%ED%83%80%EC%9E%85-%EC%A0%95%EC%9D%98%ED%95%98%EA%B8%B0-defining-types)

**복잡한 오브젝트의 타입을 재사용**하기 위해 타입을 정의할 수 있습니다.

```typescript
type Human = {
  name: string;
  age: number;
};

let boy: Human;

boy = { name: '한성욱', age: 30 };

interface Person {
  name: string;
  age: number;
}

let girl: Person;
girl = { name: '한과장', age: 20 };

function add(x: number, y: number): number {
  return x + y;
}
add(1, 2);
add('hello', 2);
// <repl>.ts:6:5 - error TS2345: Argument of type 'string' is not assignable to parameter of type 'number'.

function sub(x: number, y: number): string {
  return x - y;
}
// <repl>.ts:6:45 - error TS2322: Type 'number' is not assignable to type 'string'.
```

### ![타입 추론](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html#%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0-types-by-inference)

TypeScript는 JavaScript 코드를 받아들이면서 타입을 가지는 타입 시스템을 구축할 수 있습니다.
예를 들어, 변수를 생성하면서 동시에 특정 값에 할당하는 경우, TypeScript는 그 값을 해당 변수의 타입으로 사용합니다.

```typescript
const name: string = '한성욱';
const name = '한성욱';
```

### ![Union Type](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html#%EC%9C%A0%EB%8B%88%EC%96%B8-unions)

`유니언(Union)`은 타입이 여러 타입 중 하나일 수 있음을 선언하는 방법입니다.
Union Type은 `|` operator를 사용합니다.

예를 들어, boolean 타입을 true 또는 false로 설명할 수 있습니다.

```typescript
type bool = true | false;

let flag: bool;

flag = true;

flag = flase;

flag = 3;
//<repl>.ts:10:1 - error TS2322: Type 'number' is not assignable to type 'bool'.
```

#### Union Type를 사용하는 이유

- 매개변수를 제한하거나 할 때 유용하게 사용할 수 있습니다.

```typescript
type Category = 'food' | 'toy' | 'bag';

function fetchProducts({ category }: { category: Category }) {
  console.log(`Fetch ${category}`);
}
```

- ![ReactNode](https://github.com/facebook/react/blob/main/packages/shared/ReactTypes.js)처럼 레거시 환경 또는 코드 때문에 사용할 수 밖에 없습니다.

```typescript
export type ReactNode =
  | React$Element<any>
  | ReactPortal
  | ReactText
  | ReactFragment
  | ReactProvider<any>
  | ReactConsumer<any>;

let target: string | number;
target = '한성욱';
target = 11;

target = null;
//<repl>.ts:11:1 - error TS2322: Type 'null' is not assignable to type 'string | number'.
target = undefined;
//<repl>.ts:11:1 - error TS2322: Type 'undefined' is not assignable to type 'string | number'.
```

#### Optional Parameter

아래 예제 코드처럼 `undefined`를 직접 사용하진 않습니다.

```typescript
let targetName: string | undefined;
targetName = '한성욱';
targetName = undefined;
```

`undefined`는 함수 매개변수(Parameter)에서 주로 사용되고, 물음표 기호(?)를 사용해서 `Optional Parameter`로 처리합니다.

```typescript
function greeting(name?: string): string {
  return `Hello ${name || 'world'}`;
}

// Optional Parameter로 처리하는 것도 좋지만 기본값을 잡아주면 더 좋습니다.
function greeting(name: string = 'world'): string {
  return `Hello ${name}`;
}

// Optional Parameter는 매개변수가 오브젝트일 때 가장 많이 사용합니다.
function greeting({ name, age }: { name: string; age?: number }): string {
  return age ? `${name} (${age})` : name;
}

// 기본값을 잡아줄 경우
function greeting(
  { name, age }: { name: string; age?: number } = { name = '' }
): string {
  return age ? `${name} (${age})` : name;
}

// 아래처럼 타입 정의를 활용해서 처리할 수도 있습니다.
type Human = {
  name: string;
  age?: number;
};

function greeting({ name, age }: Human): string {
  return age ? `${name} (${age})` : name;
}
```

### ![Intersection Type](https://www.typescriptlang.org/docs/handbook/2/objects.html#intersection-types)

아래 예제 코드의 `Person`은 마치 하나의 객체 리터럴 타입으로 작성된 것 처럼 `Human` 와 `Creature` 두 개의 모든 속성을 가지고 있습니다. Intersection Type은 `&` Operator를 사용합니다.

```typescript
type Human = {
  name: string;
  age: number;
};

type Creature = {
  hp: number;
  mp: number;
};

types Person = Human & Creature;
let person: Person;
person = {name: '한성욱', age: 20, hp: 400, mp: 200}

```

### Generics, Utility Types, and Tips

#### ![Generics](https://www.typescriptlang.org/ko/docs/handbook/2/generics.html)

#### ![Utility Types](https://www.typescriptlang.org/ko/docs/handbook/utility-types.html)

#### ![더 좋은 타입스크립트 프로그래머로 만드는 11가지 팁](https://velog.io/@lky5697/11-tips-that-help-you-become-a-better-typescript-programmer)

### Visual Studio Code 자동 완성과 실시간 오류 검사

** 현실적으로 TypeScript를 사용하는 갸장 큰 이유입니다. **

## 학습 키워드

### REPL

- `REPL`이란?
  **Read-Eval-Print-Loop**의 약자로 애플리케이션 실행 상태에서 사용자가 입력한 명령어(소스코드)를 `읽고(Read)` 명령어를 `평가(Eval)`하고 결과를 `출력(Print)`한 다음 다시 입력을 기다리는 상태로 돌아가는 과정을 `반복(Loop)`합니다.
- `REPL`의 필요성
  `REPL`은 주로 개발자들이 소스 코드 실행 결과를 빨리 확인해야 하는 경우 사용합니다. 특정 개발자들은 더 나은 단위 테스트를 작성하기 위해 REPL을 사용하기도 합니다. Chrome 개발자 도구의 콘솔 탭도 `REPL` 도구 중 하나입니다.

### TypeScript

#### ![interface vs Type](https://www.typescriptlang.org/ko/docs/handbook/2/everyday-types.html#%ED%83%80%EC%9E%85-%EB%B3%84%EC%B9%AD%EA%B3%BC-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)

#### 타입추론

#### Union Type vs intersection Type

#### Optional Parameter
