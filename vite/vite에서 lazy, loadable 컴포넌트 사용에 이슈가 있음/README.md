# Vite에서 lazy, loadable 컴포넌트 사용이 가능한가?

## React.lazy는 promise형 객체이다

- React.lazy로 가져온 컴포넌트는 Suspense 컴포넌트 하위에서 렌더링되며, Suspense는 lazy 컴포넌트가 로드되는 동안 로딩 화면을 보여준다.
- Suspense에서 lazy component를 가져올 때 에러발생하는 경우를 ErrorBoundary 컴포넌트 (componentDidCatch)로 잡아 예외처리를 할 수 있다.

## lazy 사용이유

### 코드 분할이란?

- 어플리케이션 초기 로드 시에 초기 경로에 필요한 코드만 보내도록 js bundle 분할해서 보내면 로드 시간이 빨라짐.
- webpack, parcel, rollup 같은 모듈 번들러에서는 동적 가져오기를 통해 번들을 분할함
  - 동적 가져오기란?
    - 정적으로 가져오는 경우: 단순하게 import후 실행
      ```
        <script type="module">
          import * as module from './utils.mjs';
          module.default();
          // → logs 'Hi from the default export!'
          module.doStuff();
          // → logs 'Doing stuff…'
        </script>
      ```
      - 동적으로 가져오게 될 경우
      ```
        <script type="module">
          (async () => {
            const moduleSpecifier = './utils.mjs';
            const module = await import(moduleSpecifier)
            module.default();
            module.doStuff();
          })();
        </script>
      ```
    - 기존의 import ~~~의 경우에는 정적으로 데이터를 가져오고 동기적으로 처리됨. 다 가져오기 전까지 시간이 걸리면 js로드자체에 문제 발생
    - import() 식으로 데이터를 import할 경우 promise 객체 즉 비동기적으로 import를 하기 때문에 페이지 로드시에 영향을 끼치지 않고 별도로 동작하게 된다.
  - React.Lazy를 통해서 코드 분할을 선언하고, 로드하게 될 때 청크 파일을 하나 만들어 페이지 로드 시 Lazy로드를 선언한 컴포넌트 파일 (chunk.js)데이터를 가져오게 된다.
  - loadable component? (TODO: 장단점 구분이 현재 조금 미흡함.)
    - React.Lazy와는 다르게 SSR(Server Side Rendering) 을 지원해준다고 함
    - 라이브러리도 분할로 load할 수 있게 지원이 됩니다.

## 별개로 안 내용

- SVC는 Rust로 짜여진 컴파일러
- HMR이란 리로드 없이 변경사항을 페이지에 반영하는 (live-reload)기능이다.
  - 리로드 중에 react 상태가 유지되지 않는 문제는 react-hot-loader를 통해서 해결된다.
