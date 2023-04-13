# External Store

## [관심사의 분리](https://ko.wikipedia.org/wiki/%EA%B4%80%EC%8B%AC%EC%82%AC\_%EB%B6%84%EB%A6%AC)

하나의 시스템은 작은 부품이 모여서 만들어집니다. 우리는 이미 작은 컴포넌트를 합쳐서 더 큰 컴포넌트를 만드는 방식으로 개발하고 있습니다.

### Layered Architecture

관심사 분리의 기준으로 흔히 사용되는 Layered Architecture가 있습니다.\
Layered Architecture에서는 사용자에게 가까운 것과 사용자에게서 먼 것으로 구분합니다.\
가장 가까운 건 UI를 다루는 부분, 그 다음엔 Business Logic을 다루는 부분, 그 너머에는 데이터에 접근하고 저장하는 부분으로 나눌 수 있게 됩니다.\
각 부분은 하나의 역할, 하나의 관심사로 격리됨으로써 복잡도를 낮추게 됩니다.

### IPO(Input -> Process -> Output)

프로세스 관점에서도 관심사 분리를 할 수 있습니다.\
Input -> Process -> Output 3단계로 적절히 구분만 해도 코드를 이해하고 유지 보수하는데 큰 도움이 됩니다.\
하나의 Output은 다시 사용자에게 Input을 요청하게 되고, 일반적인 프로그램은 다음과 같이 계속 순환하는 구조가 됩니다.

1. Input: 프로그램 시작
2. Process: 프로그램 초기화
3. Output: 사용자에게 초기 UI 보여주기
4. Input: 사용자의 입력
5. Process: 사용자의 입력에 따라 처리
6. Output: 처리 결과 보여주기
7. Input: 사용자의 또 다른 입력
8. …반복…

### MVC 패턴

## [Flux Architecture](https://haruair.github.io/flux/docs/overview.html)

Flux Architecture는 Facebook(현 Meta)에서 MVC의 대안으로 내세운 아키텍처입니다.\
2-way binding을 썼을 때 생길 수 있는 Model-View의 복잡한 관계(전통적인 MVC에선 이런 상황을 지양한다)를 겨냥해 명확히 “단방향 데이터 흐름(unidirectional data flow)”를 강조합니다.

1. Action → 이벤트/메시지 같은 객체.
2. Dispatcher → (여러) Store로 Action을 전달.
3. Store (여러 개) → 받은 Action에 따라 상태를 변경. 상태 변경을 알림.
4. View → Store의 상태를 반영.

### [Redux의 핵심](https://ko.redux.js.org/tutorials/essentials/part-1-overview-concepts/)

Redux는 단일 Store를 사용함으로써 좀 더 단순한 그림을 제안합니다.

1. Action
2. Store → dispatch를 통해 Action을 받고, 사용자가 정의한 reducer를 통해 State를 변경한다.
3. View → State를 반영.

### External Store

React에서는 특별히 쓰이지 않는 상태라고 해도 "상태가 바뀌면" 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링합니다.\
[forceUpdate와 같은 것이 있습니까?](https://ko.reactjs.org/docs/hooks-faq.html#is-there-something-like-forceupdate)의 공식 문서에서의 접근법처럼 React가 UI를 담당하고 순수한 TypeScript(또는 JavaScript)가 비즈니스 로직을 담당하는 관심사의 분리(Separation of Concerns)를 명확히 할 수 있습니다.\
이를 통해 자주 바뀌는 UI 요소에 대한 테스트 대신 오래 유지되는(바뀌면 치명적인)비즈니스 로직에 대한 테스트 코드를 작성해 유지 보수에 도움이 되는 테스트 코드를 치밀하게 작성할 수 있습니다.

* Counter.tsx

```typescript
import useForceUpdate from "../hooks/useForceUpdate";

// Business Logic
const state = {
  count: 0
}

function increase(){
  state.count += Math.ceil(Math.random())*10
}

// UI
export default function Counter() {
  const forceUpdate = useForceUpdate();

  const handleClick = () => {
  increase()
   // 강제로 렌더링
   forceUpdate()

  }
  return (
    <>
    <div>
      <p>{state.count}</p>
      <button type="button" onClick={handleClick}>Increase</button>
    </div>
    </>
  )
}
```

* useForceUpdate.tsx

```typescript
import { useCallback, useState } from "react";

export default function useForceUpdate() {
  const [, setState] = useState({})

  return useCallback(() => setState({}), [])
}
```

## useReducer

## [useCallback](https://react.dev/reference/react/useCallback)

### [React Hooks: useCallback 사용법](https://www.daleseo.com/react-hooks-use-callback/)

### [useCallback 을 사용하여 함수 재사용하기](https://react.vlpt.us/basic/18-useCallback.html)

## 참고 자료

* [계층화 아키텍처 (Layered Architecture)](https://hudi.blog/layered-architecture/)
* [Flux와 Redux](https://taegon.kim/archives/5288)
