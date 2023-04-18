# Fetch API & CORS

## Fetch API&#x20;

### [Fetch API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)

Fetch API는 네트워크 통신을 포함한 리소스를 가지고 오기 위한 인터페이스를 제공해주는 API입니다.

### [Fetch 사용하기](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)

Fetch API가 제공하는 전역 [fetch() 메서드](https://developer.mozilla.org/en-US/docs/Web/API/fetch)로 네트워크의 리소스를 쉽게 **비동기적**으로 가져올 수 있습니다.

> **JavaScript의 동기 처리**란 '특정 코드의 실행이 완료될 때까지 기다리고 난 후 다음 코드를 수행하는 것'을 의미합니다. \
> **JavaScript의 비동기 처리**란 '특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드들을 수행하는 것'을 의미합니다.

```typescript
//`fetch()` 메서드는 Promise 객체를 반환합니다.
fetch('http://localhost:3000/products');
// -> Promise

await fetch('http://localhost:3000/products');
// -> Response
// Fetch API의 Response 인터페이스는 요청에 대한 응답을 나타냅니다.

const response = await fetch('http://localhost:3000/products');
// Fetch API는 Response 객체의 body 속성을 통하여 ReadableStream의 구체적인 인스턴스를 제공합니다.
// ReableStream 인터페이스는 ReadableStream 인터페이스는 바이트 데이터를 읽을수 있는 스트림을 제공합니다.

const reader = response.body.getReader();
// -> reponse.body는 ReadableStream 입니다.
// ReableStream.getReader()는 Reader를 만들고 스트림을 그 Reader에 고정시킵니다.

const chunk = await reader.read();
// → chunk.value는 Uint8Array 타입
// Uint8Array 타입의 배열은 8비트 부호 없는 정수의 배열을 나타냅니다.
// → 원래는 chunk.done이 true일 때까지 반복해야 한다.

const body = new TextDecoder().decode(chunk.value);
// TextDecoder 인터페이스는 UTF-8, ISO-8859-2, KOI8-R, GBK 등과 같은 특정 문자 인코딩을 위한 디코더를 나타냅니다.
// TextDecoder.decode() 메서드는 매개변수로 전달된 버퍼에서 디코딩된 텍스트가 포함된 문자열을 반환합니다.

const data = JSON.parse(body);
// JSON.parse는 JSON 문자열을 JavaScript 값 또는 객체로 변환합니다.
```

- **response 객체는 json() 메서드를 지원합니다.**\
  response 객체의 json() 메서드는 본문 텍스트를 JSON으로 구문 분석한 결과와 함께 프로미스를 반환합니다.

```javascript
const response = await fetch('http://localhost:3000/products');
const data = await response.json();
```

- **다른 HTTP Method를 사용하고 싶다면 fetch의 두 번째 매개변수를 이용합니다.**

```javascript
const response = fetch(url, {
  method: 'POST',
});
```

## Promise

Promise는 JavaScript의 [표준 내장 객체](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects)로서 제어 추상화 객체 항목에 속합니다.\
제어 추상화는 코드 구조화, 특히 비동기 코드 작성에 도움을 줍니다.

### [new Promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/Promise)

Promise()는 new 키워드를 통해 Promise 객체를 생성합니다. 주로 프로미스를 지원하지 않는 함수를 감쌀 때 사용합니다.\
또한 Promise는 비동기 처리를 수행한 콜백 함수(executor)를 인수로 전달받는데 이 콜백 함수는 resolve, reject 함수를 인수로 전달받습니다.\
promise 객체가 생성되면 executor는 자동으로 실행되고 작성했던 코드들이 작동됩니다. 코드가 정상적으로 처리가 되었다면 resolve 함수를 호출하고 에러가 발생했을 경우에는 reject 함수를 호출하면 됩니다.

```javascript
let promise = new Promise((resolve, reject) => {
  // 1. 정상적으로 처리되는 경우
  // resolve의 인자에 값을 전달할 수도 있습니다.
  resolve(value);

  // 2. 에러가 발생하는 경우
  // reject의 인자에 에러메시지를 전달할 수도 있습니다.
  reject(error);
});
```

### Promise 객체의 내부 프로퍼티

new Promise가 반환하는 Promise 객체는 state, result 내부 프로퍼티를 갖습니다. 하지만 직접 접근할 수 없고 **.then**, **.catch**, **.finally** 의 메서드를 사용해야 접근이 가능합니다.

### State

기본 상태는 pending(대기)입니다. 비동기 처리를 수행할 콜백함수(executor)가 성공적으로 작동했다면, fulfilled(이행)으로 변경이 되고, 에러가 발생했다면 rejected(거부)가 됩니다.

### Result

처음은 undefined입니다. 비동기 처리를 수행할 콜백 함수(executor)가 성공적으로 작동하여 resolve(value)가 호출되면 value로, 에러가 발생하여 reject(error)가 호출되면 error로 변합니다.

### [Then](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)

executor에 작성했던 코드들이 정상적으로 처리되었다면 resolve 함수를 호출하고 .then 메서드로 접근할 수 있습니다.\
또한 .then 안에서 리턴한 값이 Promise면 Promise의 내부 프로퍼티 result를 다음 .then의 콜백 함수의 인자로 받아오고, Promise가 아니라면 리턴한 값을 .then의 콜백 함수의 인자로 받아올 수 있습니다.

```javascript
let promise = new Promise((resolve, reject) => {
  resolve('성공');
});

promise.then((value) => {
  console.log(value);
  // "성공"
});
```

### [Catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)

executor에 작성했던 코드들이 에러가 발생했을 경우에는 reject 함수를 호출하고 .catch 메서드로 접근할 수 있습니다.

```javascript
let promise = new Promise(function (resolve, reject) {
  reject(new Error('에러'));
});

promise.catch((error) => {
  console.log(error);
  // Error: 에러
});
```

### [Finally](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally)

executor에 작성했던 코드들의 정상 처리 여부와 상관없이 .finally 메서드로 접근할 수 있습니다.

```javascript
let promise = new Promise(function (resolve, reject) {
  resolve('성공');
});

promise
  .then((value) => {
    console.log(value);
    // "성공"
  })
  .catch((error) => {
    console.log(error);
  })
  .finally(() => {
    console.log('성공이든 실패든 작동!');
    // "성공이든 실패든 작동!"
  });
```

### Promise chaining

Promise chaining가 필요한 경우는 비동기 작업을 순차적으로 진행해야 하는 경우입니다.\
&#x20;Promise chaining이 가능한 이유는 .then, .catch, .finally 의 메서드들은 Promise를 리턴하기 때문입니다. 따라서 .then을 통해 연결할 수 있고, 에러가 발생할 경우 .catch로 처리하면 됩니다.

```javascript
let promise = new Promise(function (resolve, reject) {
  resolve('성공');
});

promise
  .then((value) => {
    console.log(value);
    // "성공"
  })
  .then((value) => {
    console.log(value);
    // "성공"
  })
  .then((value) => {
    console.log(value);
    // "성공"
  })
  .catch((error) => {
    console.log(error);
  })
  .finally(() => {
    console.log('성공이든 실패든 작동!');
    // "성공이든 실패든 작동!"
  });
```

### [Promise.all()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

Promise.all()은 여러 개의 비동기 작업을 동시에 처리하고 싶을 때 사용합니다.\
인자로는 배열을 받습니다. 해당 배열에 있는 모든 Promise에서 executor 내 작성했던 코드들이 정상적으로 처리가 되었다면 결과를 배열에 저장해 새로운 Promise를 반환해줍니다.

```javascript
const promiseOne = () =>
  new Promise((resolve, reject) => setTimeout(() => resolve('1초'), 1000));
const promiseOne = () =>
  new Promise((resolve, reject) => setTimeout(() => resolve('2초'), 2000));
const promiseOne = () =>
  new Promise((resolve, reject) => setTimeout(() => resolve('3초'), 3000));
```

위 코드를 Promise chaning으로 적용했을 때 코드들이 순차적으로 동작되기 때문에 총 6초의 시간이 걸립니다. 또한 같은 코드가 중복되는 현상도 발생하게 됩니다.

```javascript
const result = [];
promiseOne()
  .then((value) => {
    result.push(value);
    return promiseTwo();
  })
  .then((value) => {
    result.push(value);
    return promiseThree();
  })
  .then((value) => {
    result.push(value);
    console.log(result);
    // ['1초', '2초', '3초']
  });
```

이러한 문제들을 Promise.all()을 통해 해결할 수 있습니다. Promise.all()은 비동기 작업들을 동시에 처리합니다. 따라서 3초 안에 모든 작업이 종료됩니다.

```javascript
Promise.all([promiseOne(), promiseTwo(), promiseThree()])
  .then((value) => console.log(value))
  // ['1초', '2초', '3초']
  .catch((err) => console.log(err));
```

추가적으로 Promise.all()은 인자로 받은 배열에 있는 Promise 중 하나라도 에러가 발생하게 되면 나머지 Promise의 state와 상관없이 즉시 종료됩니다.

### Promise Hell

Promise를 통해 비동기 코드의 순서를 제어할 수 있지만 Callback 함수와 같이 코드가 길어질수록 복잡해지고 가독성이 낮아지는 Promise Hell이 발생하는 단점이 있습니다.

### [Async](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function)와 [Await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/await)

JavaScript는 ES8에서 async/await 키워드를 제공했습니다. 이를 통해 복잡한 Promise 코드를 간결하게 작성할 수 있게 되었습니다.\
함수 앞에 async 키워드를 사용하고 async 함수 내에서만 await 키워드를 사용하면 됩니다.\
이렇게 작성된 코드는 await 키워드가 작성된 코드가 동작하고 나서야 다음 순서의 코드가 작동하게 됩니다.

```javascript
// 함수 선언식
async function funcDeclarations(){
  await 작성하고자 하는 코드
}

// 함수 표현식
const funcExpression = async function(){
  await 작성하고자 하는 코드
}

// 화살표 함수
const ArrowFunc = async () => {
  await 작성하고자 하는 코드
}
```

## [Unicode](https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)

Unicode란 전 세계의 모든 문자를 컴퓨터에서 일관되게 표현하고 다룰 수 있도록 설계된 산업 표준입니다.\
Unicode 변환 형식 중 [UTF-8](https://ko.wikipedia.org/wiki/UTF-8)은 유니코드를 위한 [문자 인코딩](https://ko.wikipedia.org/wiki/%EB%AC%B8%EC%9E%90_%EC%9D%B8%EC%BD%94%EB%94%A9) 방식 중 하나입니다.\
Unicode Transformation Format - 8bit의 약어로서 8비트(1바이트)를 기준으로 인코딩한다는 의미입니다. [TextDecoder와 TextEncoder](<(https:/ko.javascript.info/text-decoder)/>)에서 사용하는 변환 형식도 UTF-8을 사용합니다.

## CORS 란

### [동일 출처 정책(SOP)](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)

웹 브라우저는 동일 출처 정책(Same Origin Policy, SOP)에 따라 웹 페이지와 리소스를 요청한 곳이 서로 다른 [출처](https://developer.mozilla.org/ko/docs/Glossary/Origin)일 때 서버에서 얻은 결과를 사용할 수 없게 막습니다. 이때, 서버에 요청하고 응답을 받아오는 것까지는 이미 다 진행된 상태이고 브라우저에서 사용을 막는 상황입니다.

> 출처는 URL의 scheme(프로토콜), hosts(도메인), port로 구성됩니다.

### [교차 출처 리소스 공유(CORS)](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

교차 출처 리소스 공유(Cross-Origin Resource Sharing, CORS)는 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제입니다.\
CORS 체제는 브라우저와 서버 간의 안전한 교차 출처 요청 및 데이터 전송을 지원합니다.

**Fetch API는 동일 출처 정책을 따릅니다.** 즉, 이 API를 사용하는 웹 애플리케이션은 자신의 출처와 동일한 리소스만 불러올 수 있으며, 다른 출처의 리소스를 불러오려면 그 출처에서 올바른 CORS 헤더를 포함한 응답을 반환해야 합니다.

- [Access-Control-Allow-Origin](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 은 단일 출처를 지정하여 브라우저가 해당 출처가 리소스에 접근하도록 허용합니다. 또는 자격 증명이 없는 요청의 경우 "\*" 와일드 카드는 브라우저의 origin에 상관없이 모든 리소스에 접근하도록 허용합니다.

### [CORS 미들웨어](https://expressjs.com/en/resources/middleware/cors.html)

Express에서는 CORS 미들웨어를 설치해서 사용할 수 있습니다.

- 패키지 설치

```bash
npm i cors
npm i -D @types/cors
```

- CORS 미들웨어 사용 예시

```javascript
import express from 'express';
import cors from 'cors';

const app = express();
app.use(cors());
```

## 참조 자료

- [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)
- [ReableStream](https://developer.mozilla.org/ko/docs/Web/API/ReadableStream)
- [TextDecoder & TextEncoder](https://ko.javascript.info/text-decoder)
- [인코딩 vs 디코딩 정확하게 이해하기](https://codingpractices.tistory.com/entry/%EC%9D%B8%EC%BD%94%EB%94%A9-vs-%EB%94%94%EC%BD%94%EB%94%A9-%EC%A0%95%ED%99%95%ED%95%98%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
- [JSON.parse](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)
- [async와 await](https://ko.javascript.info/async-await)
- [fetch 사용 시 유의사항](<https://wooooooak.github.io/javascript/2018/11/25/fetch&json()/>)
