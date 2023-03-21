# React Component

## 강의노트

[Thinking in React](https://react.dev/learn/thinking-in-react)의 **Start with the mockup**에서는 다음과 같은 mockup과 JSON API가 나옵니다.

- mockup
  ![mockup](</images/thinking-in-react-mockup(1).png>)

- JSON API

```json
[
  { "category": "Fruits", "price": "$1", "stocked": true, "name": "Apple" },
  {
    "category": "Fruits",
    "price": "$1",
    "stocked": true,
    "name": "Dragonfruit"
  },
  {
    "category": "Fruits",
    "price": "$2",
    "stocked": false,
    "name": "Passionfruit"
  },
  {
    "category": "Vegetables",
    "price": "$2",
    "stocked": true,
    "name": "Spinach"
  },
  {
    "category": "Vegetables",
    "price": "$4",
    "stocked": false,
    "name": "Pumpkin"
  },
  { "category": "Vegetables", "price": "$1", "stocked": true, "name": "Peas" }
]
```

### Step 1: Break the UI into a component hierarchy

![mockup](<../images/thinking-in-react-mockup(2).png>)

1. mockup의 모든 컴포넌트와 하위 컴포넌트 주위에 상자를 그리고 이름을 지정합니다.
   만약 디자이너와 함께 작업한다면 이미 컴포넌트 이름이 지정된 경우가 많습니다.

```typescript
type Product = {
  category: string;
  price: string;
  stocked: boolean;
  name: string;
};

const products: Product[] = [
  { category: 'Fruits', price: '$1', stocked: true, name: 'Apple' },
  { category: 'Fruits', price: '$1', stocked: true, name: 'Dragonfruit' },
  { category: 'Fruits', price: '$2', stocked: false, name: 'Passionfruit' },
  { category: 'Vegetables', price: '$2', stocked: true, name: 'Spinach' },
  { category: 'Vegetables', price: '$4', stocked: false, name: 'Pumpkin' },
  { category: 'Vegetables', price: '$1', stocked: true, name: 'Peas' },
];

export default function App() {
  const categories = products.reduce(
    (acc: string[], product: Product) =>
      acc.includes(product.category) ? acc : [...acc, product.category],
    []
  );

  return (
    <div className='filterable-product-table'>
      <div className='search-bar'>
        <div>
          <input type='text' placeholder='Search...' />
        </div>
        <div>
          <input type='checkbox' id='only-stock' />
          <label htmlFor='only-stock'>Only show products in stock</label>
        </div>
      </div>
      <table className='product-table'>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <th colSpan={2}>{categories[0]}</th>
          </tr>
          {products
            .filter((product) => product.category === categories[0])
            .map((product) => (
              <tr key={product.name}>
                <td>{product.name}</td>
                <td>{product.price}</td>
              </tr>
            ))}
          <tr>
            <th colSpan={2}>{categories[1]}</th>
          </tr>
          {products
            .filter((product) => product.category === categories[1])
            .map((product) => (
              <tr key={product.name}>
                <td>{product.name}</td>
                <td>{product.price}</td>
              </tr>
            ))}
        </tbody>
      </table>
    </div>
  );
}
```

1-1.[Extract Function](https://refactoring.com/catalog/extractFunction.html)
일단 길게 코드를 작성하고, 적절히 자를 수 있는 부분이 보일 때 **함수로 추출**합니다.

- App.tsx

```typescript
import ProductsInCategory from './component/ProductsInCategory';
// import type은 한 칸 아래로 띄어줘서 보기 쉽게 관리합니다.
import type Product from './types/Product';

const products: Product[] = [
  { category: 'Fruits', price: '$1', stocked: true, name: 'Apple' },
  { category: 'Fruits', price: '$1', stocked: true, name: 'Dragonfruit' },
  { category: 'Fruits', price: '$2', stocked: false, name: 'Passionfruit' },
  { category: 'Vegetables', price: '$2', stocked: true, name: 'Spinach' },
  { category: 'Vegetables', price: '$4', stocked: false, name: 'Pumpkin' },
  { category: 'Vegetables', price: '$1', stocked: true, name: 'Peas' },
];

export default function App() {
  const categories = products.reduce(
    (acc: string[], product: Product) =>
      acc.includes(product.category) ? acc : [...acc, product.category],
    []
  );

  return (
    <div className='filterable-product-table'>
      <div className='search-bar'>
        <div>
          <input type='text' placeholder='Search...' />
        </div>
        <div>
          <input type='checkbox' id='only-stock' />
          <label htmlFor='only-stock'>Only show products in stock</label>
        </div>
      </div>
      <table className='product-table'>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>
          {categories.map((category) => (
            <ProductsInCategory
              key={category}
              category={category}
              products={products}
            />
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

- components/ProductsCategory.tsx

```typescript
import type Product from '../types/Product';

type ProductsInCategoryProps = {
  category: string;
  products: Product[];
};

export default function ProductsInCategory({
  category,
  products,
}: ProductsInCategoryProps) {
  const productInCategory = products.filter(
    (product) => product.category === category
  );
  return (
    <>
      <tr>
        <th colSpan={2}>{category}</th>
      </tr>
      {productInCategory.map((product) => (
        <tr key={product.name}>
          <td>{product.name}</td>
          <td>{product.price}</td>
        </tr>
      ))}
    </>
  );
}
```

1-2. Design’s Layer
JSON이 잘 구조화되어 있으면 UI의 컴포넌트 구조에 자연스럽게 매핑되는 것을 종종 발견할 수 있습니다.
이는 UI와 데이터 모델이 동일한 정보 아키텍처, 즉 동일한 형태를 가지고 있는 경우가 많기 때문입니다.
따라서 UI를 컴포넌트로 분리하면 각 컴포넌트가 데이터 모델의 한 부분과 일치하다는 것을 알 수 있습니다.

- App.tsx

```typescript
import FilterableProductTable from './component/FilterableProductTable';

import type Product from './types/Product';

const products: Product[] = [
  { category: 'Fruits', price: '$1', stocked: true, name: 'Apple' },
  { category: 'Fruits', price: '$1', stocked: true, name: 'Dragonfruit' },
  { category: 'Fruits', price: '$2', stocked: false, name: 'Passionfruit' },
  { category: 'Vegetables', price: '$2', stocked: true, name: 'Spinach' },
  { category: 'Vegetables', price: '$4', stocked: false, name: 'Pumpkin' },
  { category: 'Vegetables', price: '$1', stocked: true, name: 'Peas' },
];

export default function App() {
  return <FilterableProductTable products={products} />;
}
```

- components/FilterableProductTable.tsx

```typescript
import ProductTable from './ProductTable';
import SearchBar from './SearchBar';

import type Product from '../types/Product';

type FilterableProductTableProps = {
  products: Product[];
};

export default function FilterableProductTable({
  products,
}: FilterableProductTableProps) {
  return (
    <div className='filterable-product-table'>
      <SearchBar />
      <ProductTable products={products} />
    </div>
  );
}
```

- components/SearchBar.tsx

```typescript
export default function SearchBar() {
  return (
    <div className='search-bar'>
      <div>
        <input type='text' placeholder='Search...' />
      </div>
      <div>
        <CheckBoxField label='Only show products in stock' />
      </div>
    </div>
  );
}
```

- components/CheckBoxField.tsx

```typescript
export default function CheckBoxField({ label }: { label: string }) {
  const id = useRef(`checkbox-${label}`.replace(/ /g, '-').toLowerCase());
  return (
    <>
      <input type='checkbox' id={id.current} />
      <label htmlFor={id.current}>{label}</label>
    </>
  );
}
```

- components/ProductTable.tsx
  [Inline Function](https://refactoring.com/catalog/inlineFunction.html)을 사용할 수 있습니다.

```typescript
import ProductsInCategory from './ProductsInCategory';

import selectCategories from '../utils/selectCategories';

import type Product from '../types/Product';

type ProductTableProps = {
  products: Product[];
};

export default function ProductTable({ products }: ProductTableProps) {
  const categories = selectCategories(products);
  return (
    <table className='product-table'>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>
        {categories.map((category) => (
          <ProductsInCategory
            key={category}
            category={category}
            products={products}
          />
        ))}
      </tbody>
    </table>
  );
}
```

- utils/selectCategories.ts

```typescript
import type Product from '../types/Product';

export default function selectCategories(products: Product[]): string[] {
  return products.reduce((acc: string[], product: Product) => {
    const { category } = product;
    return acc.includes(category) ? acc : [...acc, category];
  }, []);
}
```

- component/ProductsInCategory.tsx
  [Inline Function](https://refactoring.com/catalog/inlineFunction.html)을 사용할 수 있습니다.

```typescript
import ProductCategoryRow from './ProductCategoryRow';
import ProductRow from './ProductRow';
import selectProducts from '../utils/selectProducts';

import type Product from '../types/Product';

type ProductsInCategoryProps = {
  category: string;
  products: Product[];
};

export default function ProductsInCategory({
  category,
  products,
}: ProductsInCategoryProps) {
  const productInCategory = selectProducts(products, category);
  return (
    <>
      <ProductCategoryRow category={category} />
      {productInCategory.map((product) => (
        <ProductRow key={product.name} product={product} />
      ))}
    </>
  );
}
```

- utils/selectProducts.ts

```typescript
import type Product from '../types/Product';

export default function selectProducts(
  items: Product[],
  category: string
): Product[] {
  return items.filter((item) => item.category === category);
}
```

- component/ProductCategoryRow.tsx

```typescript
export default function ProductCategoryRow({ category }: { category: string }) {
  return (
    <tr>
      <th colSpan={2}>{category}</th>
    </tr>
  );
}
```

- component/ProductRow.tsx

```typescript
import type Product from '../types/Product';

type ProductRowProps = {
  product: Product;
};

export default function ProductRow({ product }: ProductRowProps) {
  return (
    <tr>
      <th>{product.name}</th>
      <th>{product.price}</th>
    </tr>
  );
}
```
