# state의 올바른 사용법

## state란?

props와 같이 리액트의 컴포넌트 렌더링 결과물에 영향을 끼치는 요소중 하나로 컴포넌트의 안에서 관리가 되는 객체다.

## setState

state를 변경하는 동작인 setState는 함수형, 클래스형 전부 비동기적으로 진행이 된다.

- class 형

```
  import React, { Component } from 'react';

  class Counter extends Component {
    state = {
      number: 0,
      fixedNumber: 0,
    };
    render() {
      const { number } = this.state;

      return (
        <div>
          <h1>{number}</h1>
          <button
            onClick={() => {
              this.setState({ number: number + 1 });
              this.setState({ number: number + 1 });
              this.setState({ number: this.state.number + 1 });
            }}
          >
            +1
          </button>
        </div>
      );
    }
  }

  export default Counter;
```

이런식으로 코드를 짜게 될 때 혹자는 number가 3이 될거라고 예상하지만 실제로는 1씩 증가하게 됨.
setState가 비동기적으로 작동하기 때문에 3번이 도는 시점의 this.state.number는 전부 0이기 때문
여러번 업데이트를 원한 경우

```
  // 클래스형
  this.setState((prevState, props) => ({
    number: prevState.number + 1
  }));

  // 함수형
  setState((state) => state + 1);
```

이런식으로 기존의 state에서 update를 쳐준다고 선언해야함

## 왜 비동기적으로 작동하는가?

리액트는 브라우저 이벤트가 끝난 시점에 state를 일괄적으로 업데이트하고, 더 큰 규모의 앱에서 뚜렷한 성능 향상을 이루고 있다. 즉 하나의 이벤트 핸들러에서 작동 시 리액트는 변화를 한번만 작동시키게 된다. 이런 동작을 batch처리 라고 하는데, 바로 전달받은 state 값을 바꾸는 것이 아니라 이전의 dom 트리와 전달받은 state가 적용된 dom트리를 비교하고 최종적으로 변경된 부분만 DOM에 적용함.
