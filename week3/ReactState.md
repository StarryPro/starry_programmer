# React State

## React State란?

컴포넌트는 상호 작용의 결과로 화면의 내용을 변경해야 하는 경우가 많은데, React의 state는 **변경**을 다루기 위한 요소입니다.

[Thinking in React](https://react.dev/learn/thinking-in-react)에서도 언급되지만
React에서는 상태(State)를 다룰 때 일관성과 효율을 위해 DRY 원칙을 따르는 SSOT을 만드는 것을 권장합니다.

## DRY 원칙

DRY는 Don't Repeat Yourself의 약어로 **정보의 반복을 줄이기 위한 소프트웨어 개발의 기본 원칙**입니다.

### DRY 원칙을 사용하는 이유?

1. 한 곳에서 코드를 변경하고, 모든 인스턴스에서 변경 사항을 확인할 수 있습니다.
2. 시간과 노력이 절약되고 유지 관리가 쉬우며 버그가 발생할 가능성도 줄어듭니다.

### DRY 원칙을 준수하는 방법

동일한 코드 또는 로직을 반복해서 작성/복사하여 붙여넣는 행위를 금지하고
코드와 로직을 재사용 가능한 작은 단위로 나누고 원하는 곳에서 해당 코드를 호출하여 사용합니다.

## SSOT(Single Source of Truth)

[SSOT(Single Source of Truth)](https://ko.wikipedia.org/wiki/%EB%8B%A8%EC%9D%BC_%EC%A7%84%EC%8B%A4_%EA%B3%B5%EA%B8%89%EC%9B%90)는 단일 진실 공급원을 의미합니다.
축약어를 그대로 해석해서 단일 진실 공급원이라는 표현을 사용했지만 이를 풀어서 해석하면
**모든 데이터 요소를 한 곳에서만 제어 또는 편집하도록 조직하는 것**을 의미합니다.

### React State의 조건

1. 변경돼야 합니다. 변경되지 않는 건 state로 다룰 가치가 없습니다.
   대표적인 예로 아래 이미지와 같은 형태의 data는 변경되지 않기 때문에 state로 다룰 가치가 없습니다.
   ![](<../images/reactState(1).png>)
2. 부모 컴포넌트가 props를 통해 전달한다면 state가 아닙니다.
3. **다른 state나 props를 이용해 계산 가능하다면 state가 아닙니다.**
   아래 코드 예제의 경우 categories의 값은 동일하지만 state와 effect를 사용할 경우
   코드가 필요 이상으로 복잡해지기 때문에 state를 사용하지 않는 게 좋습니다.

```typescript
// const categories = selectCategories(products);

const [categories, setCategories] = useState<string[]>([]);

useEffect(() => {
  setCategories(selectCategories(products));
}, [products]);
```

---

## 그렇다면 상태(state)는 누가 관리해야 할까? 정확히는 상태를 누가 소유해야되나?

**React만 사용한다면 해당 상태(state)에 의존적인 컴포넌트를 모두 포함하는 컴포넌트가 상태를 소요해야 합니다.**

## Lifting State Up

[React 베타 문서](https://react.dev/learn/sharing-state-between-components)에 따르면
예를 들어 두 컴포넌트의 상태가 항상 함께 변경되기를 원할 때가 있습니다.
이렇게 하려면 두 컴포넌트에서 state를 제거하고 가장 가까운 공통 부모로 이동한 다음 props를 통해 전달하면 됩니다.
이를 상태 리프팅(Lifting State Up)이라고 정의합니다.

## useState

[React Beta문서](https://react.dev/reference/react/useState)에 따르면 useState란 컴포넌트에 상태 변수(state variable)를 추가할 수 있는 React Hook이라고 정의합니다.

### useState 사용방법

```javascript
// 기본 형태
const [state, setState] = useState(initialState);
```

## 1급 객체(first-class object)란?

자바스크립트에서는 다름과 같은 조건을 만족하는 객체를
[일급 객체](https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89_%EA%B0%9D%EC%B2%B4)라고 합니다.

1. 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
2. 변수나 자료구조(객체. 배열 등)에 저장할 수 있다.
3. 함수의 매개변수에 전달할 수 있다.
4. 함수의 반환값으로 사용할 수 있다.

자바스크립트(JavaScript)의 함수는 위 조건을 모두 만족하기 때문에 일급 객체입니다.
함수가 일급 객체라는 것은 함수를 객체와 동일하게 사용할 수 있다는 의미입니다. 일급 객체로서 함수가 가지는 가장 큰 특징은
일반 객체와 같이 함수의 매개변수에 전달할 수 있으며, 함수의 반환값으로 사용할 수 있다는 것입니다. 이는 함수형 프로그래밍을 가능하게 하는 자바스크립트의 장점 중 하나입니다.

---

## Thinking in React

- Step 3: Find the minimal but complete representation of UI state
  최소한의 완전한 UI 상태(state) 표현을 찾는다.

[Thinking in React](https://react.dev/learn/thinking-in-react) 예제에서는
검색 텍스트(filterText)와 체크박스의 값(inStockOnly)을 상태(state)로 지정했습니다.

- components/SearchBar.tsx

```typescript
import CheckBoxField from './CheckBoxField';
import TextField from './TextField';

export default function SearchBar() {
  return (
    <div className='search-bar'>
      <TextField placeholder='Search...' />
      <div>
        <CheckBoxField label='Only show products in stock' />
      </div>
    </div>
  );
}
```

- components/TextField.tsx

```typescript
import { ChangeEvent, useState } from 'react';

type TextFieldProps = {
  placeholder: string;
};

export default function TextField({ placeholder }: TextFieldProps) {
  const [filterText, setFilterText] = useState<string>('');

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    setFilterText(value);
  };

  return (
    <div>
      <input
        type='text'
        placeholder={placeholder}
        value={filterText}
        onChange={handleChange}
      />
    </div>
  );
}
```

- components/CheckBoxField.tsx

```typescript
import { useRef, useState } from 'react';

export default function CheckBoxField({ label }: { label: string }) {
  const id = useRef(`checkbox-${label}`.replace(/ /g, '-').toLowerCase());

  const [inStockOnly, setInStockOnly] = useState<boolean>(false);
  const handleChange = () => {
    setInStockOnly(!inStockOnly);
  };
  return (
    <>
      <input
        type='checkbox'
        id={id.current}
        checked={inStockOnly}
        onChange={handleChange}
      />
      <label htmlFor={id.current}>{label}</label>
    </>
  );
}
```

- Step 4: Identify where you state should live
  **상태(state)가 있어야할 위치를 파악한다.**
  React는 단방향 데이터 흐름을 사용하며, 부모 컴포넌트에서 자식 컴포넌트로 컴포넌트 계층 구조를 따라 데이터를 전달합니다.
  따라서 가장 가까운 공통 상위 컴포넌트, 즉 계층 구조에서 모든 컴포넌트 위에 있는 컴포넌트를 찾습니다.

  현재 FilterableProductTable 컴포넌트는 SearchBar 컴포넌트와 ProductTable의 부모 컴포넌트 입니다.
  ProductTable은 해당 상태(검색 텍스트 및 체크박스 값)를 기준으로 제품 목록을 필터링해야 합니다.
  SearchBar는 해당 상태(검색 텍스트 및 체크박스 값)를 표시해야 합니다.

  - components/FilterableProductTable.tsx

```typescript
import { useState } from 'react';

import ProductTable from './ProductTable';
import SearchBar from './SearchBar';

import type Product from '../types/Product';

type FilterableProductTableProps = {
  products: Product[];
};

export default function FilterableProductTable({
  products,
}: FilterableProductTableProps) {
  // InStockOnly가 있어야 하는 곳
  // filterText가 있어야 하는 곳
  const [filterText, setFilterText] = useState<string>('');

  return (
    <div className='filterable-product-table'>
      <SearchBar filterText={filterText} />
      <ProductTable products={products} />
    </div>
  );
}
```

- components/SearchBar.tsx

```typescript
import CheckBoxField from './CheckBoxField';
import TextField from './TextField';

type SearchBarProps = {
  filterText: string;
};

export default function SearchBar({ filterText }: SearchBarProps) {
  return (
    <div className='search-bar'>
      <TextField placeholder='Search...' filterText={filterText} />
      <div>
        <CheckBoxField label='Only show products in stock' />
      </div>
    </div>
  );
}
```

- components/TextField.tsx

```typescript
type TextFieldProps = {
  placeholder: string;
  filterText: string;
};

export default function TextField({ placeholder, filterText }: TextFieldProps) {
  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    setFilterText(value);
  };

  return (
    <div>
      <input
        type='text'
        placeholder={placeholder}
        value={filterText}
        onChange={handleChange}
      />
    </div>
  );
}
```

- Step 5: Add inverse data flow
  역 데이터 흐름을 추가한다.
  다시 한 번 강조하지만 **React는 단방향 데이터 흐름을 사용하며, 부모 컴포넌트에서 자식 컴포넌트로 컴포넌트 계층 구조를 따라 데이터를 전달합니다.** React에서는 props를 통해 하위 컴포넌트로 함수를 전달할 수 있습니다ㅏ.

  이게 가능한 이유는 TypeScript(정확히는 JavaScript)에서는 **함수가 [일급 객체](https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89_%EA%B0%9D%EC%B2%B4)**이기 때문입니다.
  일급 객체는 일반 객체와 같이 함수의 매개 변수에 전달할 수 있으며, 함수의 반환값으로 사용할 수 있습니다.
  여기서, props를 통해 하위 컴포넌트로 전달되는 함수 즉 함수의 매개 변수로 전달되는 함수를 [콜백함수](https://ko.wikipedia.org/wiki/%EC%BD%9C%EB%B0%B1)라고 부릅니다. 참고로 함수를 반환하는 함수, 함수를 인자로 받을 수 있는 함수를 [고차함수](https://ko.wikipedia.org/wiki/%EA%B3%A0%EC%B0%A8_%ED%95%A8%EC%88%98)라고 부릅니다.

  - components/FilterableProductTable.tsx

```typescript
import { useState } from 'react';

import ProductTable from './ProductTable';
import SearchBar from './SearchBar';

import type Product from '../types/Product';

type FilterableProductTableProps = {
  products: Product[];
};

export default function FilterableProductTable({
  products,
}: FilterableProductTableProps) {
  // InStockOnly가 있어야 하는 곳
  // filterText가 있어야 하는 곳
  const [filterText, setFilterText] = useState<string>('');

  return (
    <div className='filterable-product-table'>
      <SearchBar filterText={filterText} setFilterText={setFilterText} />
      <ProductTable products={products} />
    </div>
  );
}
```

- components/SearchBar.tsx

```typescript
import CheckBoxField from './CheckBoxField';
import TextField from './TextField';

type SearchBarProps = {
  filterText: string;
  setFilterText: (value: string) => void;
};

export default function SearchBar({
  filterText,
  setFilterText,
}: SearchBarProps) {
  return (
    <div className='search-bar'>
      <TextField
        placeholder='Search...'
        filterText={filterText}
        setFilterText={setFilterText}
      />
      <div>
        <CheckBoxField label='Only show products in stock' />
      </div>
    </div>
  );
}
```

- components/TextField.tsx

```typescript
type TextFieldProps = {
  placeholder: string;
  filterText: string;
  setFilterText: (value: string) => void;
};

export default function TextField({
  placeholder,
  filterText,
  setFilterText,
}: TextFieldProps) {
  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    setFilterText(value);
  };

  return (
    <div>
      <input
        type='text'
        placeholder={placeholder}
        value={filterText}
        onChange={handleChange}
      />
    </div>
  );
}
```

## 참고자료

- [DRY Principle in 100 Seconds](https://dev.to/richardwynn/dry-principle-in-100-seconds-2l8c)
- [React Beta 문서 state](https://react.dev/learn/state-a-components-memory)
