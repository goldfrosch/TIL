# Generator

일반적인 함수와는 다르게 원하는 시점에서 정지, 실행이 가능한 함수다.

## 제너레이터 함수 사용법

우선 제너레이터 함수는 arrow function을 사용할 수 없다.

```
    function* testGenerator1() {}
    const testGenerator2 = function* () {}
```

일반 함수와는 다르게 제너레이터 함수는 실행의 제어권을 양도할 수 있는 함수다.
일반적으로 함수의 동작 패턴은 어디까지나 실행되는 함수가 정한다. (로직은 보통 자기 자신 안에서 해결이 되니까.)

제너레이터 함수의 경우는 함수를 호출한 곳에서 함수의 상태를 주고받을 수 있다. 호출한 함수에서 언제 멈출지, 실행할 지 제어가 가능하다라는 것이다.

일반 함수는 호출될 경우 함수의 코드 블록을 실행시키지만, 제너레이터 함수는 코드 블럭 실행이 아닌, 제너레이터 객체를 반환하기 때문이다.

이 generator 객체를 이용해서 이런 함수를 만들어서 사용이 가능하다.

```
    function* tests() {
        yield 'apple';
        yield 'banana';
        yield 'melon';
    }

    for (test of tests()) {
        console.log(`과일 종류: ${test}`);
    }
```

yield로 선언하고 일시정지할 객체들을 선언 후 for문으로 반복시키는 방법을 사용하면 하나의 함수를 여러번 돌리면서 각각 yield로 지정한 값들을 호출이 가능해진다.

### 제너레이터 객체

제너레이터 함수를 호출할 때 return되는 객체로 iterable하면서 iterator다.

### iterator란?

https://github.com/goldfrosch/FE-TIL/tree/main/js/iterator
