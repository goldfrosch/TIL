# 렌더링 최적화

## 렌더링의 성능 개선

렌더링은 리액트에서 state나 props가 변경될 경우에 자연스럽게 일어나는 현상이지만, 이 작업이 때때로는 낭비가 되는 경우가 존재한다.
구성요소가 변경되지 않고 DOM의 해당 부분을 업데이트 할 필요가 없는데도 렌더링을 계산하는 행위는 매우 낭비다.

특정 컴포넌트에 대해 state나 props가 변해 다시 그리지 않는 이상은 렌더링 작업을 건너뛰어도 된다고 미리 알고 있으면 조금 더 성능이 향상될 수 있지 않을까...

일반적으로 소프트웨어의 성능을 향상시킨다면 기본적으로 2가지 접근법이 존재한다.

1. 동일한 작업을 더 빠르게 수행
2. 불필요한 작업을 수행하지 않음

## 불필요한 렌더링 방지법

- React.memo()
  - https://github.com/goldfrosch/FE-TIL/tree/main/react/rendering/%EB%A0%8C%EB%8D%94%EB%A7%81%20%EC%B5%9C%EC%A0%81%ED%99%94/memoization
- props
  - https://github.com/goldfrosch/FE-TIL/tree/main/react/rendering/%EB%A0%8C%EB%8D%94%EB%A7%81%20%EC%B5%9C%EC%A0%81%ED%99%94/props
