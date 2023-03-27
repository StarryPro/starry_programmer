# Express

## Express 란

### [Express](https://expressjs.com/ko/)

Expresss는 Node.js 웹 애플리케이션 프레임워크로서 자유롭게 활용할 수 있는 수많은 HTTP 유틸리티 메소드 및 미들웨어를 통해
쉽고 빠르게 강력한 API를 작성할 수 있습니다.

### 간단한 서버 앱 npm 패키지 세팅

- [Express 설치](https://expressjs.com/ko/starter/installing.html) 순서
  Express 설치하기는 Express 문서를 통해서 자세하게 확인할 수 있습니다.

1. 작업 디렉토리 생성
   [Node.js](https://nodejs.org/en)가 이미 설치되었다고 가정한 상태에서, 애플리케이션을 보관할 디렉토리를 작성하고 그 디렉토리를 작업 디렉토리로 설정합니다.

```bash
mkdir express-demo-app
cd mkdir express-demo-app
```

2. 잊지 말고 .gitignore 파일 준비

```bash
touch .gitignore
echo "/node_modules/" > .gitignore
```

3. 패키지 초기화
   npm init 명령을 이용하여 애플리케이션에 대한 package.json 파일을 작성합니다.

```bash
npm init -y
```

4. TypeScript 설치

```bash
npm i -D typescript
npx tsc --init
```

5. ESLint

```bash
npm i -D eslint
npx eslint --init
```

6. ts-node 설치

```bash
npm i -D ts-node
```

7. Express 설치

```bash
npm i express
npm i -D @types/express
```

### [Hello World 에제](https://expressjs.com/ko/starter/hello-world.html)

1. TypeScript에 맞춰서 app.ts 파일 작성
   앱은 서버를 시작하며 3000번 포트에서 연결을 청취합니다. 앱은 루트 URL(/) 또는 라우트에 대한 요청에 “Hello World!”로 응답합니다. 다른 모든 경로에 대해서는 404 Not Found로 응답합니다.

```typscript
import express from 'express';

const port = 3000;

const app = express();

app.get('/', (req, res) => {
  res.send('Hello world!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

2. ts-node로 실행

```bash
npx ts-node app.ts
```

3. [nodemon](https://github.com/remy/nodemon) 사용
   node.js 애플리케이션의 변경 사항을 모니터링하고 서버를 자동으로 다시 시작하게 해주는 도구입니다.
   코드를 수정할 때마다 서버를 재실행해야 하는 문제를 피하기 위해 사용합니다.

```bash
npm i -D nodemon
npx nodemon app.ts
```

## URL 구조

브라우저 주소창에 입력한 URL은 서버가 제공되는 환경에 존재하는 파일의 위치를 나타냅니다.
슬래시(`/`)를 이용해 서버의 폴더에 진입하거나 파일을 요청할 수 있습니다.\
![]()

### URL

URL은 Uniform Resource Locator의 줄임말로, 네트워크 상에서 웹 페이지, 이미지, 동영상 등의 파일이 위차한 정보를 나타냅니다.\
URL은 scheme, hosts, url-path로 구분할 수 있습니다. 가장 먼저 작성하는 scheme은 통신 방식(프로토콜)을 결정합니다. 일반적인 브라우저에서는 http(s)를 사용합니다. hosts는 웹 서버의 이름이나 도메인, IP를 사용하여 주소를 나타냅니다. url-path는 웹 서버에서 지정한 루트 디렉토리부터 시작하여 웹 페이지, 이미지, 동영상 등이 위치한 경로와 파일명을 나타냅니다.

### URI

URI는 Uniform Resource Identifier의 줄임말로, 일반적으로 URL의 기본 요소인 scheme, hosts, url-path에 더해 query, fragment를 포함합니다.\
query는 웹 서버에 보내는 추가적인 질문입니다. 위 이미지의 `http://www.google.com:80/search?q=JavaScript`를 브라우저의 검색창에 입력하면, 구글에서 JavaScript를 검색한 결과가 나타납니다.\
fragment는 일종의 북마크 기능을 수행하며 URL에 fragment(#)와 특정 HTML 요소의 id를 전달하면 해당 요소가 있는 곳으로 스크롤을 이동할 수 있습니다.

브라우저의 검색창을 클리하면 나타나는 주소가 URI입니다. URI는 URL을 포함하는 상위개념입니다.

| 부분                                 | 명칭     | 설명                                                                                |
| ------------------------------------ | -------- | ----------------------------------------------------------------------------------- |
| `file://`, `http://`, `https://`     | scheme   | 통신프로토콜                                                                        |
| `127.0.0.1`, `www.google.com`        | hosts    | 웹 페이지, 이미지, 동영상 등의 파일이 위치한 웹 서버, 도메인 또는 IP                |
| `:80`, `:443`, `:3000`               | port     | 웹 서버에 접속하기 위한 통로                                                        |
| `/search`, `/Users/username/Desktop` | url-path | 웹 서버의 루트 디렉토리로부터 웹 페이지, 이미지, 동영상 등의 파일이 위치까지의 경로 |
| `q=JavaScript`                       | query    | 웹 서버에 전달하는 추가 질문                                                        |

## REST API

### REST API란 무엇인가?

REST API의 REST는 `Representational State Transfer`의 약자로 [로이 필딩](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)의 박사학위 논문에서 웹의 장점을 최대한 활용할 수 있는 아키텍쳐로써 처음 소개되었습니다.\
REST API는 **웹에서 사용되는 데이터나 자원(Resource)을 HTTP URI로 표현하고, HTTP 프로토콜을 통해 요청과 응답을 정의하는 방식**을 의미합니다.

### REST API를 디자인하는 방법

REST API를 작성할 때는 몇가지 지켜야 할 규칙들이 있습니다. 로이 필딩이 논문에서 제시한 REST 방법론을 보다 더 실용적으로 적용하기 위해 [레오나르드 리차드슨](https://martinfowler.com/articles/richardsonMaturityModel.html)은 REST API를 잘 적용하기 위한 4단계 모델을 만들었습니다.

로이 필딩은 이 모델의 모든 단계를 충족해야 REST API라고 부를 수 있다고 주장했습니다.\
그러나 실제로 엄밀하게 3단계까지 지키기 어렵기 때문에 2단계까지만 적용해도 좋은 API 디자인이라고 볼 수 있고, 이런 경우를 HTTP API라고도 부릅니다.

- REST 성숙도 모델 - 0단계\
  0단계에서는 단순히 HTTP 프로토콜을 사용하기만 해도 됩니다. 해당 API를 REST API라고 할 수는 없으며, 0단계는 REST API를 작성하기 위한 기본 단계입니다.

- REST 성숙도 모델 - 1단계\
  1단계에서는 개별 리소스(Resource)와의 통신을 준수해야 합니다.\
  앞서 **REST API는 웹에서 사용되는 모든 데이터나 자원(source)을 HTTP URI로 표현**한다고 언급했습니다.
  따라서 모든 자원은 개별 리소스에 맞틑 앤드포인트(Endpoint)를 사용해야하며 요청하고 받는 자원에 대한 정보를 응답으로 전달해야 한다는 것이 핵심입니다.\
  엔드포인트 작성 시에는 동사, HTTP 메서드, 혹은 어떤 행위에 대한 단어 사용은 지양하고, 리소스에 집중해 명사 형태의 단어로 작성하는 것이 바람직한 방법입니다.

- REST 성숙도 모델 - 2단계\
  2단계에서는 CRUD에 맞게 적절한 HTTP 메서드를 사용하는 것에 중점을 둡니다.\

  기본 리소스 URL:/products\
  Collection은 복수 리소스, Item(Element)는 단수 리소스를 의미합니다.

  1. Read(Collection) -> GET/products => 상품 목록 확인
  2. Read(Item) -> GET/products/{id} => 특정 상품 정보 확인
  3. Create(Collection Pattern 활용) -> POST/products => 상품 추가(JSON 정보 함께 전달)
  4. Update(Item) -> PUT 또는 PATCH/products/{id} => 특정 상품 정보 변경(JSON 정보 함께 전달)
  5. Delete(Item) -> DELETE/products/{id} => 특정 상품 삭제

- REST 성숙도 모델 - 3단계\
  마지막 단계는 HATEOAS(Hypermedia As The Engine Of Application State)라는 약어로 표현되는 하이퍼미디어 컨트롤을 적용합니다.\
  3단계의 요청은 2단계와 동일하지만, 응답에는 리소스의 URI를 포함한 **링크** 요소를 삽입하여 작성해야 합니다.

### CRUD(Create, Read, Update, Delete)와 HTTP 메서드 적용 규칙

- `GET` 메서드는 **데이터를 조회(READ)**하기 위해서 사용합니다. `GET` 메서드를 사용하여 요청을 보내고, 이때 `GET` 메서드는 `body`를 가지지 않기 때문에 query parameter를 사용하여 필요한 리소스를 전달합니다. 참고로 조회(Read)는 복수(Collection) 조회와 단수(Item(Element)) 조회로 나뉩니다.\
  `GET`메서드는 서버의 데이터를 변화시키지 않는 요청에 사용해야 합니다.
- `POST` 메서드는 **데이터를 추가(CREATE)**하기 위해서 사용합니다. `POST`메서드는 요청마다 새로운 리소스를 생성하고 `PUT` 메서드는 요청마다 같은 리소스를 반환합니다.\
  이렇게 매 요청마다 같은 리소스를 반환하는 특징을 `멱등` 하다고 합니다. 그렇기 때문에 멱등성을 가지는 `PUT`메서드와 그렇지 않은 `POST`메서드를 구분하여 사용해야 합니다.
- `PUT` 메서드와 `PATCH`메서드는 **데이터를 업데이트(UPDATE)**하기 위해 사용합니다. `PUT` 메서드의 경우 리소스의 모든 것을 업데이트하고 `PATCH` 메서드는 리소스의 일부를 업데이트합니다.\
  따라서 `PUT` 메서드는 교체, `PATCH`메서드는 수정의 용도로 사용합니다.

| 요청         | 적절한 메서드  |
| ------------ | -------------- |
| 조회(Read)   | GET            |
| 추가(Creat)  | POST           |
| 갱신(Update) | PUT 또는 PATCH |
| 삭제(Delete) | DELETE         |
