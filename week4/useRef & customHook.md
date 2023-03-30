# useRef & customHook

## [useRef](https://ko.reactjs.org/docs/hooks-reference.html#useref)

[React Beta 문서](https://react.dev/reference/react/useRef)에 따르면 useRef란 렌더링에 필요하지 않는 값을 참조할 수 있는 React Hook입니다.\
`const refContainer = useRef(initialValue);`
useRef는 .current 프로퍼티로 전달된 인자(initialValue)로 초기화된 변경 가능한 ref 객체를 반환합니다.\
반환된 객체는 컴포넌트의 전 생애주기를 통해 유지됩니다. 즉, 컴포넌트가 없어질 때까지 동일한 객체가 유지됩니다.\
또한 상태(state)가 변경되면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하지만, 레퍼런스 객체의 현재 값(current)이 바뀌더라도 렌더링에 영향을 주지 않습니다.

### 주요 용도

1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우. => input 등의 ID 관리.
2. (특히 useEffect 등과 함께 쓰면서 만나게 되는) 비동기 상황에서 현재 값을 제대로 쓰고 싶은 경우.

```typescript
const [filterText, setFilterText] = useState<string>('');

useEffect(() => {
  setTimeout(() => {
    console.log(filterText);
  }, 5_000);
}, []);

// 초기 값인 빈 문자열 나옵니다.
```

- useRef 적용

```typescript
const query = useRef('');
const [filterText, setFilterText] = useState<string>('');

// 실제로 이렇게 사용할 일은 거의 없습니다.
useEffect(() => {
  query.current = filterText;
}, [filterText]);

useEffect(() => {
  setTimeout(() => {
    console.log(query.current);
  }, 5_000);
}, []);
// 현재 값을 나타낼 수 있습니다.
```

## [custom Hook](https://react.dev/learn/reusing-logic-with-custom-hooks)

로직을 재사용하기 위한 제일 쉬운 방법으로써 평범하게 Extract Function을 수행하면 됩니다.\
컴포넌트가 대문자로 시작하는 PascalCase로 이름을 붙인다면, Hook은 "use"로 시작하는 camelCase로 이름을 붙이면 됩니다.

- app.tsx

```typescript
import FilterableProductTable from './component/FilterableProductTable';
import TimerControl from './component/TimerControl';
import useFetchProducts from './hooks/useFetchProducts';

export default function App() {
  const products = useFetchProducts();
  return (
    <>
      <TimerControl />
      <h1>상품</h1>
      <FilterableProductTable products={products} />
    </>
  );
}
```

- hooks/useFetchProducts.ts

```typescript
import { useEffect, useState } from 'react';
import Product from '../types/Product';

export default function useFetchProducts() {
  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    const fetchProducts = async () => {
      const url = 'http://localhost:3000/products';
      const response = await fetch(url);
      const data = await response.json();
      setProducts(data.products);
    };

    fetchProducts();
  }, []);
  return products;
}
```

- components/FilterableProductTable.ts

```typescript
export default function FilterableProductTable({
  products,
}: FilterableProductTableProps) {
  const {
    filterText,
    setFilterText,
    inStockOnly,
    setInStockOnly,
    filteredProducts,
  } = useProductsFilter(products);

  return (
    <div className='filterable-product-table'>
      <SearchBar
        filterText={filterText}
        setFilterText={setFilterText}
        inStockOnly={inStockOnly}
        setInStockOnly={setInStockOnly}
      />
      <ProductTable products={filteredProducts} />
    </div>
  );
}
```

- hooks/useProductsFilter.ts

```typescript
import { useState } from 'react';
import Product from '../types/Product';
import filterProducts from '../utils/filterProducts';

export default function useProductsFilter(products: Product[]) {
  const [inStockOnly, setInStockOnly] = useState<boolean>(false);

  const [filterText, setFilterText] = useState<string>('');

  const filteredProducts = filterProducts(products, {
    filterText,
    inStockOnly,
  });
  return {
    filterText,
    setFilterText,
    inStockOnly,
    setInStockOnly,
    filteredProducts,
  };
}
```

## [Hook의 규칙](https://ko.reactjs.org/docs/hooks-rules.html)

1. 최상위 레벨에서만 Hook을 호출해야 합니다.

2. Function Component 또는 Custom Hook에서만 호출해야 합니다.

처음에는 콜백 함수나 조건문 안에서 Hook을 호출하는 실수를 저지르기 쉽습니다.

```javascript
if (playing) {
  const products = useFetchProducts();
  console.log(products);
}
```
