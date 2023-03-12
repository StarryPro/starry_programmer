# 개발환경

## 강의 노트

### JavaScript 개발 환경(Node.js) 세팅

제가 사용하고 있던 Node.js version은 `v16.15.1`이었습니다.\
현재 기준으로 `v18.14.2 LTS`의 Node.js version으로 세팅하려 합니다.\
Node.js version 관리를 위해 [FNM(Fast Node Manager)](https://github.com/Schniz/fnm#using-a-script)를 사용했습니다. 기존에는 `nvm`을 통해서 Node.js version을 관리했지만, `fnm`이 `nvm`보다 더 빠르고 간편하게 Node.js version을 관리할 수 있다고 하여 사용하게 되었습니다.

```bash
// Node.js version 확인
node --version  // v16.15.1

// Homebrew를 이용해 fnm 설치
brew install fnm

// `~/.bashrc` 또는 `~/.zshrc`에 다음 추가
eval "$(fnm env --use-on-cd)"

// Node.js LTS(Long Term Support) version 설치 및 사용
fnm install --lts
fnm use lts-latest

// 설치된 Node.js version 확인 및 지정한 버전 default로 설정
fnm list
fnm default $(fnm current)
```

### TypeScript + React + Jest + ESLint + Parcel 개발 환경 세팅

#### 1. 먼저 적절한 작업 폴더를 준비합니다.

```bash
mkdir my-app
cd my-app
```

#### 2. npm 패키지를 준비합니다.

npm 패키지를 준비하는 게 첫 번째 작업입니다.

```bash
npm init -y
```

#### 3. `.gitignore` 파일을 작성합니다.

`node_modules`와 같은 파일들을 commit 하지 않도록 `.gitignore` 파일을 통해 방지할 수 있습니다.

```
/node_modules/
/dist/
/.parcel-cache/
```

위 3개의 파일은 반드시 `.gitignore` 파일에 넣어줘야하고, github에서 기본으로 제공하는 [.gitignore 파일](https://github.com/github/gitignore/blob/main/Node.gitignore)을 주로 사용합니다.

#### 4. TypeScript를 설정합니다.

```bash
npm i -D typescript
npx tsc --init
```

`tsconfig.json` 파일에서 `"jsx": "preserve"` 주석을 해제하고, `"jsx": "react-jsx"` 설정을 변경했습니다. 참고로 TypeScript는 [여러 가지의 JSX 모드](https://www.typescriptlang.org/ko/docs/handbook/jsx.html)를 제공하고 있습니다.\
`npm i -D`는 `npm install --save-dev`의 약어로 **개발 환경**에서 사용하는 도구를 설치할 때 사용하는 명령어 입니다. 서버 배포 시 `devDependencies`를 제외할 수 있어서 배포 크기를 줄일 수 있습니다.

```json
// package.json의 devDependencies key는 개발할 때만 사용하는 확장 모듈을 관리합니다.
"devDependencies": {
    "typescript": "^4.9.5"
  }
```

`npx tsc --init`에서 **tsc**는 TypeScript Compiler의 약어이고, **npx**는 npm에서 제공하는 도구로서 로컬에 설치된 모듈을 간편하게 실행시킬 수 있고, 로컬에 해당 모듈이 없다면 임시 설치해서 실행시킬 수 있습니다. `./node_modules/.bin/tsc`와 `npx tsc`은 동일합니다.

#### 5. ESLint를 설정합니다.

```bash
npm i -D eslint
npx eslint --init
```

`.eslintrc.js`파일을 적절히 수정합니다.

<pre class="language-json"><code class="lang-json"><strong>env: {
</strong>	browser: true,
<strong>	es2021: true,
</strong>	jest: true,
},
extends: ['plugin:react/recommended', 'plugin:react/jsx-runtime', 'xo'],
</code></pre>

아직 Jest를 설치하지 않았지만, 미리 `jest: true`를 잡아주면 좋습니다.

#### 6. `.eslintignore` 파일을 작성합니다

`.eslintignore` 파일 내용은 `.gitignore` 파일과 동일하게 작성했습니다.

#### 7. React를 설치합니다

```bash
npm i react react-dom
npm i -D @types/react @types/react-dom
```

#### 8. 테스팅 도구를 설치합니다

```bash
npm i -D jest @types/jest @swc/core @swc/jest \
    jest-environment-jsdom \
    @testing-library/react @testing-library/jest-dom
```

#### 9. `jest.config.js` 파일을 작성합니다

[jest.config.js 파일](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/jest.config.js)을 작성해서 테스트 시 `SWC`를 사용할 수 있게 합니다. `setupFilesAfterEnv`의 내용 중 `./jest.setup`은 삭제합니다.

*   [SWC(Speedy Web Compiler)](https://swc.rs/)란?

    > SWC can be used for both compilation and bundling. For compilation, it takes JavaScript / TypeScript files using modern JavaScript features and outputs valid code that is supported by all major browsers.

#### 10. `Parcel`을 설치합니다

```bash
npm i -D parcel
```

*   `Parcel`이란?

    Parcel은 **특별한 설정 없이 바로 사용 가능한 빌드 도구**입니다. \
    내부적으로 `SWC`를 사용해서 기존 도구들보다 빠릅니다.

#### 11. `package.json` 파일의 scripts 내용을 수정합니다.

아래의 [scripts](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/package.json) 내용을 참고하여 수정합니다.

```json
"scripts": {
    "start": "parcel --port 8080",
    "build": "parcel build",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll"
  },
```

또한 위 상태에서 `npm run start`를 하면 에러가 발생하는데, \
현재 `packsge.json`의 `"main" : "index.js"`를 `"resource" : "index.html"`로 수정하면 에러를 해결할 수 있습니다. 원래 node의 경우 실행 파일을 `"main"` 으로 잡아주는데, 해당 파일에서는 웹 서버를 띄울 것이기 때문에 `"source"`로 변경했습니다.

## 학습 키워드

### [Node.js](https://nodejs.org/en/about/)

Node.js는 **비동기 이벤트 기반 JavaScript 런타임**입니다.

### NPM(Node Package Manager)

NPM(Node Package Manage)는 **자바스크립트 패키지 매니저**입니다. **Node.js에서 사용할 수 있는 모듈들을 패키지화하여 모아둔 저장소 역할**과 **패키지 설치 및 관리를 위한 CLI(Command line interface)를 제공**합니다.

#### 1-1. package.json

NPM(Node Package Manager)은 **`package.json` 파일을 통해서 프로젝트 정보와 패키지의 의존성을 관리**합니다. `package.json`을 통해서 동일한 개발 환경을 빠르게 구축할 수 있는 장점이 있습니다.

package.json의 `dependencies` key는 서비스를 배포할 때 쓰이는 모듈 관리를 위한 부분입니다.\
`npm install` 명령어를 통해 `dependencies`에 포함된 모든 확장 모듈을 설치할 수 있습니다.

```sh
// package.json 생성
npm init

// package.json 기본 설정 적용
npm init -y
```

#### 1-2. package-lock.json

**package.json에서 설치한 모듈의 `dependencies`들을 상세하게 명시해 놓은 곳**입니다.

#### 2. node\_modules

**node\_modules**는 **모듈들을 패키지화하여 모아둔 저장소**입니다.

#### 3. npx

**npx**는 npm에서 제공하는 도구로서 로컬에 설치된 모듈을 간편하게 실행시킬 수 있고, 로컬에 해당 모듈이 없다면 임시 설치해서 실행시킬 수 있습니다.

## 참고 자료

* [\[NODE\] 📚 npm(node package manager) 완벽 정리](https://inpa.tistory.com/entry/NODE-%F0%9F%93%9A-%EB%85%B8%EB%93%9C-npmnode-package-manager)
* [모듈화와 npm(node package manager)](https://poiemaweb.com/nodejs-npm)
* &#x20;[Node.js의 module loading system](https://poiemaweb.com/nodejs-module)&#x20;
* [npm 과 npx 차이](https://hanamon.kr/npm-npx-%EC%B0%A8%EC%9D%B4/)
