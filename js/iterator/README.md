# Iterator

## Iteration이란?

iteration이란 반복 처리라는 뜻으로 데이터 안의 요소를 연속적으로 꺼내는 행위를 말한다.
대표적인 예시로 forEach 메서드가 있다.

## Iterator란?

반복처리(Iteration)이 가능한 객체를 말한다. 위에서 설명한 forEach라는 작업은 내부적으로 처리되므로 개발자는 그 처리에 대해 관리가 불가능하다. 다만 ES6이후로 부터 나온 문법 Iterator를 사용하면 개발자가 반복 처리를 단계별로 제어가 가능하다.

Iterator는 일반적으로 2가지 항목을 만족하는 객체다.

- next라는 메서드를 가진다.
- next의 반환값은 value property(속성값)와 done property를 가진 객체다. value에는 꺼낸 값이 저장되고 done은 반복이 끝났는지를 뜻하는 논리값이 저장된다.

```
    let a = [1, 2, 3];
    let iter = a[Symbol.iterator]();

    iter().next() // { value: 1, done: false }
    iter().next() // { value: 2, done: false }
    iter().next() // { value: 3, done: false }
    iter().next() // { value: undefined, done: true }
```

### Symbol이란? (번외)

변경 불가능한 원시 타입의 값이며 다른 값과 중복되지 않는 고유한 값이다. 충돌 위험이 없는 오브젝트의 유일한 property key를 만들기 위해 사용한다.

```
    const symbolA = Symbol('test');
    const symbolB = Symbol('test');

    symbolA === symbolB // false
```
