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

## useState와 불변성

useState는 state관리를 위한 정말 편리한 방법이다. 다만 사용할 경우에 이상한 문제가 가끔씩 발생하게 된다...

```
  const like = () => {
    setComment((oldValue) => {
      oldValue.likes = 1;
      oldValue.likedByCurrentUser = true;
      return oldValue;
    });
  };
```

해당 코드는 setState를 통해 특정 객체의 수정을 원했지만 클릭해도 그 어떠한 변화도 없다. useState에서 setState는 값이 변경 되어야만 리렌더링을 수행하기 때문이다.
위의 예제에서는 누가봐도 값 자체는 변경되는 것처럼 보인다. (state의 initialValue가 나오지 않았지만 setState하는 값이랑은 아무튼 다름)
이렇게 되는 이유는 자바스크립트의 불변성 때문이다.

### 불변성이란?

말 그대로 변하지 않는 것을 의미한다. js에서는 객체가 생성된 후 그 속성을 변경할 수 없게하는 즉 데이터의 원본이 훼손되는 것을 막는 패턴이다.
JS에서는 데이터의 타입이 크게 2가지 존재하는데, 데이터 복사가 일어날 때 새로 메모리 공간을 확보해 독립적인 값을 저장하는 원시(Primitive Type)타입이 존재하고, 메모리에 직접 데이터를 접근하는 것이 아닌 메모리의 위치(주소)에 대해 간접적으로 참고해 접근하는 참조(Reference)타입이 존재한다.

- 원시타입

  - boolean, number, string, undefined 등의 기본적 객체들이 원시타입이다.
  - 데이터의 복사가 일어날 때 마다 새로운 메모리에 데이터를 생성하고 저장하는 방식
  - 즉 원시타입일 경우에는 b라는 변수가 a라는 변수를 참조해도 새로운 메모리에 데이터를 저장하기 때문에 서로 다른 값이 된다.

- 참조타입
  - 객체나 배열형태 등 원시타입이 아니면 전부 참조타입이다.
  - 데이터의 메모리 주소를 참조해 접근하는 방식이라 b라는 변수가 a라는 변수를 참조하면 같은 메모리 주소를 공유하기 때문에 a가 변경되면 b도 변경됨.

즉 setState는 데이터의 변경을 감지해서 리렌더링을 해준다. 하지만 특정 객체의 값을 변경한들 메모리 주소는 변경되는 것이 아니기 때문에 동일한 값으로 취급이 되어 리렌더링이 발생하지 않게 된다. 그렇다면 참조 타입의 요소들이 데이터가 변경됨을 어떻게 감지시킬 수 있는가?

핵심은 얕은 복사와 깊은 복사다.
얕은 복사는 메모리 주소를 전달해 하나의 객체를 다른 곳과 공유하는 방식으로 우리가 흔히 쓰는 방식대로 복사하면 된다.

```
 let a = {
   test: "사랑해요 연예가 중개"
 }
 let b = a;
```

이런 얕은 복사는 메모리 주소가 공유되어 a가 변경되면 b도 변경되는 전형적인 참조 타입이 된다.

깊은 복사는 객체 원본과의 참조가 아에 끊어진 불변한 객체들을 복사해서 가져오는 경우를 말한다. 즉 주소를 가져오는게 아니라 내부의 값만을 복사해서 가져오는 방법이다.

### 깊은 복사 사용법

1. spread 연산자
   첫번째 방법은 spread 연산자로 깊은 복사를 행하는 방법이다 (가장 흔히 사용함). spread 연산자로 객체 내부의 값들을 가져오는 방법이다.

```
  const like = () => {
    setComment((oldValue) => {
      return { ...oldValue, likes: 1, likedByCurrentUser: true };
    });
  };
```

2. Lodash cloneDeep
   Lodash라는 라이브러리를 이용해 cloneDeep이라는 함수를 사용하면 자동으로 깊은 복사를 해준다.

```
  setUser((oldValue) => {
    const newValue = cloneDeep(oldValue)
    newValue.mailingSettings.address.zipCode = "12345"
    return newValue
  });
```

3. JSON.stringify
   핵심은 객체는 참조타입이기 때문에 얕은 복사가 되는 것이다. 깊은 복사가 되려면 원시타입이여야만 한다면 참조타입을 원시타입으로 바꾸면 해결될 문제다.
   JSON.stringify를 통해 객체를 string으로 바꾸면 원시타입이 되어 깊은 복사가 자동으로 가능해진다. 다만... 데이터가 너무 커 좋은 방법은 아니다.

4. Object.assign()
   해당 메소드는 넣는 객체들의 모든 열거 가능한 속성들을 복사해 하나의 객체로 붙여넣는 방법이다. 객체를 복사해 붙여넣는 깊은 복사를 자동수행해주는 방법이다.

즉 setState를 할 경우에 객체나 배열같은 참조타입을 변환할 경우에는 무조건 깊은 복사를 행해서 데이터를 복사해줘야만 한다!

### useState는 왜 늘 const로 선언되는가?

이 역시 불변성과 관련이 있다. 위의 이슈처럼 useState의 state는 직접 수정되면 안된다. 또한 setState로 데이터의 변경이 가능하고 컴포넌트는 실행되면서 매번마다 const 변수를 선언해주기 때문에 직접 객체를 변환시키지 않게 하기 위해 let이 아닌 const로 선언해주는 것

```
  let [state, setState] = useState(0);

  const handleClick = () => {
    state = 1;
    setState(state);
  };
```

해당 경우도 state의 데이터를 변경했기에 리렌더링은 하지만, 단순히 state값만 변화시키면 리렌더링하지 않는다.
