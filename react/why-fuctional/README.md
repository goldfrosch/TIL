# 왜 클래스형 컴포넌트보다 함수형인가?

## 클래스형과 함수형의 차이
- class component는 render()에서 jsx를 반환하고 functional component는 return으로만 반환한다
- props를 가져올 경우 클래스형은 this.props로 가져오고, 함수형은 parameter에 들어있는 값을 바로 사용한다.

- babel로 트랜스파일링 될 경우 함수형과 클래스형의 반응값이 다르다.
    - 함수형 (순수 js형태로 트랜스파일링 됨)
    