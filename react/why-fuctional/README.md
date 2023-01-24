# 왜 클래스형 컴포넌트보다 함수형인가?

## 클래스형과 함수형의 차이

- class component는 render()에서 jsx를 반환하고 functional component는 return으로만 반환한다
- props를 가져올 경우 클래스형은 this.props로 가져오고, 함수형은 parameter에 들어있는 값을 바로 사용한다.
- 사용하는 코드의 양이 적다 (불필요한 render()가 선언되지 않고, this.props같은 방식을 사용하지 않음 - 주로 this가 빠지는게 크지 않나~)
- React 팀에서는 계속해서 함수형 컴포넌트를 밀어주고 있다. (hooks등 계속해서 새로운 기술들은 함수형에서만 밀어주고 있는 상황)
- 클래스형에서는 lifecycle로 컴포넌트 업데이트에 대해 관리하지만 함수형은 useEffect로만 관리해준다.

- babel로 트랜스파일링 될 경우 클래스형이 함수형보다 트랜스파일링되는 코드의 양이 많다. (증명은 안된 상태)
  - 참조문서: https://medium.com/@majidrahimi/functional-component-vs-class-component-57347b26a24b
  - babel 트랜스파일링 된 코드가 길어지면 안 좋은 점?
    - cra든 webpack으로 세팅을 하던 빌드과정에서 이전 브라우저 지원을 위해 babel을 이용하여 이전 버전의 스크립트 코드로 변형해주는 과정을 babel에서 해준다.
    - 그 과정에서 코드가 길어진다라는 것은 코드의 용량이 늘어나고, 또 js를 로드하는 용량이 많아 시간이 더 길어질 수 있기 때문에 좋지는 않은 것
