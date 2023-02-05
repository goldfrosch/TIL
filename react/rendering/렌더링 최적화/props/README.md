## 새로운 props참조가 렌더링 최적화에 미치는 영향

기본적으로 컴포넌트의 props가 변경되지 않더라도 하위 컴포넌트라면 다시 렌더링 과정을 거친다라는 것을 알았다.
그렇다라는 것은 하위 컴포넌트에 props를 전달 하던, 하지 않던 렌더링의 최적화랑은 연관이 크게 없음을 의미한다.

```
  function ParentComponent() {
    const onClick = () => {
      console.log('Button clicked');
    };

    const data = { a: 1, b: 2 };

    return <NormalChildComponent onClick={onClick} data={data} />;
  }
```

이런식으로 부모 컴포넌트가 매번 렌더링 될 때 마다, 매번 새로운 onClick과 data 객체 참조를 만들어 자식 컴포넌트에 전달할 것 이다.

이 의미는 `<div />`나 `<button />`을 React.memo로 감싸는 것 마냥 호스트 컴포넌트에 대해 렌더링을 최적화 하는 것이 별 의미 없다라는 것이다. (결국 새로 다 만들어주니까~)

하지만 자식 컴포넌트가 props의 변경여부를 확인해 렌더링을 최적화 하려는 경우는 새로운 props를 전달하면 하위 컴포넌트는 렌더링을 수행하게 된다.

새로운 props가 실제로 새로운 데이터라면 이 방법이 유효하지만 상위 컴포넌트는 단순하게 콜백함수를 전달하는 수준이라면

```
  const MemoizedChildComponent = React.memo(ChildComponent)

  function ParentComponent() {
  const onClick = () => {
  console.log('Button clicked')
  }

      const data = { a: 1, b: 2 }

      return <MemoizedChildComponent onClick={onClick} data={data} />

  }
```

ParentComponent가 렌더링 과정을 거칠 때마다 MemoizedChildComponent는 props의 값이 변하지 않았음에도 props가 새로운 참조로 변경되었음을 확인하고 다시 렌더링을 해준다.

결국은 아무리 메모이제이션을 하고 싶다고 한들. 새로운 참조가 계속 생기기 때문에 props의 변화를 비교하는 것은 의미가 없다.

## props 참조 최적화하기

클래스 컴포넌트의 경우 항상 동일한 참조인 인스턴스 메소드를 가질 수 있기 때문에, 실수로 새 콜백 함수 참조를 만들어 버릴 걱정을 크게 할 필요는 없다. 하지만 별도의 자식 목록들에게 대한 고유한 콜백을 생성하거나 익명 함수에서 값을 캡쳐해 자식에게 전달해야 할 경우는 새로운 참조가 생기고 렌더링 하는 동안 자식 요소에서 새로운 개체가 생성된다. 리액트는 이러한 문제를 현재 해결해주고 있지 않다.

함수형의 경우 동일한 참조 재사용에서는 두가지 훅을 이용한다.

- useMemo
  - 객체 생성
  - 복잡한 계산과 같은 모든 종류의 일반 데이터
- useCallback
  - 콜백함수 재사용

## 그렇다면 왜 모든 것을 memoization을 안하는가?

useMemo와 useCallback으로 모든 것을 감쌀 필요는 없다. 이러한 처리는 자식 컴포넌트의 동작의 변화를 만들 뿐이다.

그렇다면 왜 리액트에서는 모든 컴포넌트에 대해 React.memo로 감싸지 않았냐라는 것이다.

Dan Abramov라는 사람이 계속해서 지적하는 내용은 props를 비교하는 것은 공짜가 아니라는 것. 컴포넌트가 리렌더링 될 때마다 props를 비교하고, 컴포넌트가 항상 새로운 props를 받기 때문에 메모이션으로 체크한다고 해도 리렌더링을 막을 수 없는 상황 또한 존재한다.

리액트는 완전히 렌더링을 기반으로하고 무엇이든 동작하려면 렌더링을 해야한다. 그리고 대부분의 렌더링은 비싸지 않다.

즉 낭비되고 있는 렌더링을 줄이는 것만이 그렇게 좋은 방법도 아니고 전체를 리렌더링 하는 것도 그렇게 많은 경우도 아니다.

그리고 계속해서 개발자들은 정말 React.memo를 사용해야 하는 가에 대해 활발하게 이야기 중이다
https://github.com/facebook/react/issues/14463
