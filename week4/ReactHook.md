# React Hook

## React Hook 이란

### [Hook의 개요](https://ko.reactjs.org/docs/hooks-intro.html)

### 기존 방식의 문제점

1. Wrapper Hell(HOC)\
   기존에는 컴포넌트 사이에서 상태 로직을 재사용하기 어려웠습니다.\
    컴포넌트를 가져와 새 컴포넌트를 반환하는 함수인[고차 컴포넌트(HOC, Higher Order Component)](https://ko.reactjs.org/docs/higher-order-components.html)이나 [reder props](https://ko.reactjs.org/docs/render-props.html)와 같은 패턴을 통해서 상태 로직을 재사용했습니다.\
    하지만 위 패턴의 사용은 컴포넌트의 재구성을 강요하며, 코드의 추적을 어렵게 만드고 Wrapper Hell을 볼 가능성을 높였습니다.
2. Huge Component
3. Confusing Classes

### 기존 방식 vs 현재 방식

- 기존 방식
  상태를 가진 컴포넌트는 Class Component로 만들고, props만 사용하는 재사용이 용이한 작은 컴포넌트는 Function Component로 작성합니다.

- 현재 방식

1. 컴포넌트는 Function Component만 사용합니다.
2. 상태 관리 유무를 신경쓰지 않아도 됩니다.
3. 복잡한 요소는 전부 Hook으로 분리시켜서 재사용이 가능하게 합니다.

## [Hooks](https://ko.reactjs.org/docs/hooks-reference.html)

## [useState](https://ko.reactjs.org/docs/hooks-reference.html#usestate)

[React Beta 문서](https://react.dev/reference/react/useState)에 따르면 useState hook은 컴포넌트에 상태 변수를 추가할 수 있습니다.

## [useEffect](https://ko.reactjs.org/docs/hooks-reference.html#useEffect)

[React Beta 문서](https://react.dev/learn/synchronizing-with-effects)에 따르면 useEffect hook은 렌더링 후 일부 코드를 실행하여 컴포넌트를 React 외부의 시스템과 동기화하는 데 사용합니다.\

- [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
- [Using the Effect Hook](https://ko.reactjs.org/docs/hooks-effect.html)
- [useEffect 완벽 가이드](https://overreacted.io/ko/a-complete-guide-to-useeffect/)

위 링크로 연결된 문서의 내용을 요약하면 다음과 같습니다.

1. useEffect hook은 렌더링 이후 해야 할 일, 즉 React의 외부와 관련된 일을 정해줄 수 있다.
2. 기본적으로 렌더링 때마다 실행되므로, 의존성 배열을 통해 언제 Effect를 실행할 지 지정할 수 있다.
3. 함수를 return 해서 종료할 수 있다.

### 처음에 한 번만 실행하기

의존성 배열에서 아무 것도 지정하지 않으면 맨 처음에 딱 한 번만 실행하게 할 수 있습니다.\
 주로 API를 호출해서 데이터를 얻을 때 사용합니다.

```typescript
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
```

### [함수를 이펙트 안으로 옮기기](https://overreacted.io/ko/a-complete-guide-to-useeffect/#%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%ED%8E%99%ED%8A%B8-%EC%95%88%EC%9C%BC%EB%A1%9C-%EC%98%AE%EA%B8%B0%EA%B8%B0)\*\*

### useLayoutEffect

### [useContext](https://ko.reactjs.org/docs/hooks-reference.html#usecontext)

`const value = useContext(MyContext);`

[React Beta 문서](https://react.dev/reference/react/useRef)에 따르면 useContext는 컴포넌트에서 컨텍스트를 읽고 구독할 수 있는 React Hook입니다. 여기서, [컨텍스트](https://ko.reactjs.org/docs/context.html)란 부모 컴포넌트가 프로퍼티를 통해 명시적으로 전달하지 않고도 그 아래 트리에 있는 모든 컴포넌트가 일부 정보를 사용할 수 있도록 하는 객체입니다.

## [React StrictMode](https://ko.reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects) 란

[React Beta 문서](https://react.dev/reference/react/StrictMode)에 따르면 React.StrictMode로 컴포넌트 전체를 감쌀 경우, 개발 중에 컴포넌트에서 흔히 발생하는 버그를 조기에 발견할 수 있습니다.\
 Strict 모드는 개발 모드에서만 활성화되기 때문에, 프로덕션 빌드에는 영향을 끼치지 않습니다.\

- StrictMode가 활성화 되면 다음과 같은 개발 전용 동작이 활성화됩니다.
  - 컴포넌트가 불완전한 렌더링으로 인한 버그를 찾기 위해 추가 시간을 들여 다시 렌더링합니다.\
  - 컴포넌트가 이펙트 정리가 누락되어 발생한 버그를 찾기 위해 한 번 더 이펙트를 다시 실행합니다.\
  - 컴포넌트에서 더 이상 사용되지 않는 API의 사용 여부를 확인합니다.\
