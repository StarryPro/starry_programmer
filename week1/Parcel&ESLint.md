# 5. Parcel & ESLint

## 강의노트

### Parcel

**특별한 설정 없이 바로 사용 가능한 빌드 도구**입니다.

- 빌드 실행

```bash
npx parcel build
```

#### 추천하는 설정 작업

- `package.json` 파일에 source 속성 추가

```json
"source" : "./index.html"
```

- ![parcel-reporter-static-files-copy](https://github.com/elwin013/parcel-reporter-static-files-copy) 패키지 설치 후 `.parcelrc` 파일 작성
  아래 설정을 통해서 static 폴더의 파일을 정적 파일로 Serving할 수 있습니다.(이미지 등 Assets)

```json
{
  "extends": ["@parcel/config-default"],
  "reporters": ["...", "parcel-reporter-static-files-copy"]
}
```

## 학습 키워드

### Bundler(번들러)

먼저, `번들링`이란 **모듈들의 의존성 관계를 파악하여 그룹화시켜주는 작업**을 의미합니다. 모듈(module)이란 특정 기능을 갖는 작은 코드 단위입니다. 번들링 작업 도구를 번들러라고 하며, 대표적인 번들러에는 webpack, parcel, vite 등이 있습니다.

#### Parcel

Parcel은 **특별한 설정 없이 바로 사용 가능한 빌드 도구**입니다.
내부적으로 `SWC`를 사용해서 기존 도구들보다 빠릅니다.

> SWC란 Rust로 짜여진 컴파일러로 속도와 성능 개선에 초점을 맞추고 있습니다.

### Lint(린트)

#### ESLint

## 참고 자료

![Parcel로 모듈 번들러 이해해보기](https://2donny-world.tistory.com/13)
