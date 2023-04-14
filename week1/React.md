# React

## 강의 노트

### React

React에 대한 내용은 아래 문서들을 통해 주기적인 학습이 필요합니다.

* [React 코어 개발자가 쓴 React에 대한 이해를 돕는 글](https://overreacted.io/ko/react-as-a-ui-runtime/)
* [React Beta 문서](https://beta.reactjs.org/)
* [React 공식 문서](https://ko.reactjs.org/)
* [React 작업 프로세스 공식 문서](https://ko.reactjs.org/docs/thinking-in-react.html)
* [React 작업 프로세스 Beta 문서](https://beta.reactjs.org/learn/thinking-in-react)

### 렌더링

* [createRoot Beta 문서](https://beta.reactjs.org/reference/react-dom/client/createRoot)
* [createRoot 공식 문서](https://ko.reactjs.org/docs/react-dom-client.html#createroot)
* [Updating a root component](https://beta.reactjs.org/reference/react-dom/client/createRoot#updating-a-root-component)

여러 개의 Root를 만들거나, 여러 번 render가 가능합니다.

```typescript
import ReactDOM from 'react-dom/client';
import App from './App';

function Demo({ count }: { count: number }) {
  return <p id='demo'>{`count: ${count}`}</p>;
}

function main() {
  const elementRoot = document.getElementById('root');
  const elementDemo = document.getElementById('demo');
  if (!elementRoot || !elementDemo) {
    return;
  }

  const root = ReactDOM.createRoot(elementRoot);
  const demo = ReactDOM.createRoot(elementDemo);

  root.render(<App />);

  let count = 0;
  demo.render(<Demo count={count} />);

  setInterval(() => {
    count += 1;
    demo.render(<Demo count={count} />);
  }, 1000);
}

main();
```

### 리렌더링

* [React는 컴포넌트를 언제 다시 리렌더링 할까요?](https://velog.io/@surim014/react-rerender)
* [왜 리액트에서 리렌더링이 발생하는가](https://medium.com/@yujso66/%EB%B2%88%EC%97%AD-%EC%99%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C-%EB%A6%AC%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%B4-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94%EA%B0%80-74dd239b0063)
* [React 렌더링 동작에 대한 (거의)완벽한 가이드](https://velog.io/@superlipbalm/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior)

```typescript
import { useState } from 'react';

export default function App() {
  const [count, setCount] = useState(0);
  // 하나의 node로 반환돼야 합니다.
  return (
    <div>
      <p>Hello world</p>
      <p>Count: {count}</p>
      <button
        type='button'
        onClick={() => {
          setCount(count + 1);
        }}
      >
        클릭!
      </button>
    </div>
  );
}
```

## 학습 키워드

* **React 란?**
* **React 컴포넌트**
* **React 리렌더링**
* **IoC(Inversion of Control)**
* **Library vs Framework**
