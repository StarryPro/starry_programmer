# React Testing Library

## [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)

React Testing Library는 React 컴포넌트를 사용자 입장에 가깝게 테스트할 수 있는 도구입니다.

- 기존 TextField.tsx

```typescript
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
        type="text"
        placeholder={placeholder}
        value={filterText}
        onChange={handleChange}
      />
    </div>
  );
}
```

테스트 코드, 즉 컴포넌트를 사용하는 코드를 작성하면서 해당 컴포넌트의 인터페이스를 점검할 수 있습니다.\
기존 컴포넌트에는 label이 빠져있었고, text 같이 범용적인 표현을 사용하지 않은 문제가 있었습니다.\

- TextField.test.tsx

```typescript
import { render, screen } from '@testing-library/react';

import TextField from './TextField';

test('TextField', () => {
  // given
  const text = 'Tester';
  const setText = () => {
    // do nothing...
  };

  // when
  render(
    <TextField
      label="Name"
      placeholder="Input your name"
      text={text}
      setText={setText}
    />
  );

  //then
  screen.getByLabelText('Name');
});
```

- 테스트 후 개선된 TextField.tsx

```typescript
export default function TextField({
  label,
  placeholder,
  text,
  setText,
}: TextFieldProps) {
  const id = useRef(`input-${Math.random()}`);
  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    setText(value);
  };

  return (
    <div>
      <label htmlFor={id.current}>{label}</label>
      <input
        id={id.current}
        type="text"
        placeholder={placeholder}
        value={text}
        onChange={handleChange}
      />
    </div>
  );
}
```

### BDD 스타일 테스트 코드

반복되는 코드를 Extract Function하고, fireEvent 등을 통해 인터랙션만 검증합니다.

```typescript
import { render, screen, fireEvent } from '@testing-library/react';

import TextField from './TextField';

const context = describe;

describe('TextField', () => {
  const text = 'Tester';
  const setText = jest.fn();

  beforeEach(() => {
    setText.mockClear();
    // 또는 jest.clearAllMocks();
  });

  function renderTextField() {
    render(
      <TextField
        label="Name"
        placeholder="Input your name"
        text={text}
        setText={setText}
      />
    );
  }

  it('renders an input control', () => {
    renderTextField();

    screen.getByLabelText('Name');
  });

  context('when user types text', () => {
    it('calls the change handler', () => {
      renderTextField();

      fireEvent.change(screen.getByLabelText('Name'), {
        target: {
          value: 'New Name',
        },
      });

      expect(setText).toBeCalledWith('New Name');
    });
  });
});
```

### Mocking

만약 외부 의존성이 큰 코드를 작성한다면, 해당 부분만 가짜로 구현할 수 있습니다.

```typescript
import { render, screen } from '@testing-library/react';

import App from './App';

jest.mock('./hooks/useFetchProducts', () => fixtures.products);

test('App', () => {
  render(<App />);

  screen.getByText('Apple');
});
```

### Test fixture

중복 발생되는 무언가(행위)를 고정시켜 한곳에 관리하도록 하겠다는 개념입니다.

- fixtures/index.ts

```typescript
import products from './producsts';

export default { products };
```

- fixture/products.ts

```typescript
const products = [
  {
    category: 'Fruits',
    price: '$1',
    stocked: true,
    name: 'Apple',
  },
];

export default products;
```
