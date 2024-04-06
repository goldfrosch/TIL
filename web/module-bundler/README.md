# 모듈 번들러

모듈이란 하나의 큰 JS 코드 베이스를 모듈이라는 여러 단위로 분할해 사용할 수 있게 만드는 구조를 말한다. CommonJS와 ESM이 나오면서 부터 적극적으로 사용되기 시작했는데 이 모듈 시스템을 적극적으로 활용하면서 JS로 개발할 때는 모듈없이 개발하기 힘들게 되었다.
하지만 모든 브라우저에서 모듈 기능을 활용할 수 있는 것은 아니기에 브라우저에서 이 모듈을 사용하려면 의존 관계를 미리 해결하고 브라우저에서 인식이 가능한 JS 코드로 변환하는 작업이 별도로 필요하고 이 역할을 모듈 번들러가 해준다.

## 모듈 번들러 역할

모듈 번들러의 역할은 어찌되었든 의존 관계를 분석 후 브라우저가 인식 가능한 JS코드로 변환한다. 더 구체적으로는 어떤 모듈을 읽고 브라우저에서도 인식이 가능한 일반 함수로 변환하고 그 함수의 인수로 다른 모듈을 import하기 위한 함수와 모듈의 export하는 값을 저장하기 위한 객체를 전달해준다.

```
// AS-IS
import { test } from "testM";

export const value = test + 30;

// TO-BE
function (require, module, exports) {
    var { test } = require('testM);
    exports.y = test + 30;
}
```

분석해 변환된 함수는 CommonJS 형식으로 되어있는데 ESM을 CommonJS로 트랜스파일링 시켜 브라우저에서도 사용하기 편하게 한다.

## 모듈 번들러 변화

이전에는 모듈 번들러를 사용하기에 종류가 너무 적었다. 그나마 괜찮은 모듈 번들러는 Webpack이였는데 그 외에 마땅히 있지 않았고 CRA가 없는 시대도 있었기 때문에 Webpack의 configuration을 직접 설정하면서 사용하고 있었고 별 다른 선택지가 없었다.

하지만 이제는 ESM을 브라우저에서 지원(`<script type="module" />`)을 시작했고 에버그린 브라우저(별도의 재설치가 없어도 업데이트가 가능한 브라우저)가 아닌 브라우저들을 지원하지 않는 비율이 급격하게 늘어나게 되었다.

### loader의 확장

https://victor-log.vercel.app/post/babel-loader-and-ts-loader-and-esbuild-loader/

### bundler 종류의 확장
