# VirtualDOM

## 강의노트

### 브라우저 렌더링 과정

![](/images/browswer-rendering.png)
사용자가 브라우저를 통해 웹 사이트에 접속하면 브라우저는 서버로부터 HTML, CSS, JavaScript와 같은 웹 사이트에 필요한 리소스를 다운 받습니다.
이후 브라우저 렌더링 과정은 아래와 같습니다.

1. HTML DOM Tree 구축: 렌더링 엔진은 전달받은 HTML 문서를 파싱(parsing)해서 DOM(Document Object Model, 문서 객체 모델) Tree를 형성합니다.
2. CSSOM Tree 구축: 이어서 다운 받은 외부 CSS 파일과 함께 포함된 스타일 요소를 파싱(parsing)해서 CSSOM Tree를 형성합니다.
3. Render Tree 구축: 만든 DOM 트리와 CSSOM 트리를 결합합니다.
4. Layout: 각 요소를 어디에 배치할 지 결정합니다.
5. Paint: 레이아웃 과정이 끝나면 Render Tree를 화면에 그리기 시작합니다.

### DOM 조작의 비효율성

브라우저는 HTML 문서를 화면에 보여주기 위해 `DOM Tree 생성` -> `CSSOM Tree 생성` -> `Render Tree 생성` -> `Layout` -> `Paint` 과정을 거칩니다.
DOM은 트리 데이터 구조로 표현됩니다. 따라서 DOM의 변경 및 업데이트가 빠릅니다.
하지만 변경 후에는 업데이트된 요소와 그 하위 요소를 다시 렌더링하여 애플리케이션 UI를 업데이트해야 하는데
이때 UI를 다시 렌더링하거나 다시 페인팅하는 과정에서 비용이 듭니다.
따라서 UI 컴포넌트가 많을수록 DOM을 업데이트할 때마다 다시 렌더링해야 하므로 DOM 업데이트 비용이 더 많이 듭니다.

이러한 맥락에서 DOM을 업데이트할 때마다 다시 렌더링해야 하는 비효율성을 해결하고 최적화를 할 필요성이 대두되었고, 이 과정에서 등장한 개념이 VDOM(Virtual DOM)입니다.

### VDOM(Virtual DOM)이란?

[React 공식 문서](https://reactjs.org/docs/faq-internals.html#what-is-the-virtual-dom)에 따르면
**VDOM(Virtual DOM)**은 UI의 이상적인 또는 "가상" 표현을 **메모리에 보관**하고 ReactDOM과 같은 라이브러리를 통해 **"실제" DOM과 동기화하는 프로그래밍 개념**입니다. 이 접근방식이 React의 **선언적 API**를 가능하게 하고
이 과정을 **재조정(reconciliation)**이라고 부릅니다.

Virtual DOM을 좀 더 쉽게 정의하면 **DOM Tree를 가벼운 버전으로 복제한 자바스크립트 객체**라고 할 수 있습니다.
Virtual DOM을 가벼운 버전으로 복제한 자바스크립트 객체라고 표현안 이유는 Virtual DOM은 class, style 등의 속성은 갖고 있지만 `document.getElementById` 같은 DOM api 메서드는 갖고 있지 않기 때문입니다.

### Virtual DOM의 동작

UI에 새 요소가 추가되면 트리로 표시되는 가상 DOM이 생성됩니다. 각 요소는 이 트리의 노드입니다. 이러한 요소 중 하나라도 상태가 변경되면 새로운 가상 DOM 트리가 생성됩니다. 그런 다음 이 트리를 이전 가상 DOM 트리와 비교하거나 "차이점"을 찾습니다.

이 작업이 완료되면 가상 DOM은 실제 DOM에 이러한 변경 사항을 적용하기 위해 가능한 최선의 방법을 계산합니다. 이렇게 하면 실제 DOM에서 최소한의 작업만 수행됩니다. 따라서 실제 DOM을 업데이트하는 데 드는 성능 비용을 줄일 수 있습니다.

![](/images/virtualDOM.png)

### 재조정(reconciliation)이란?
