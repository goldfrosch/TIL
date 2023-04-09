# Closure

가장 기본적인 JS의 지식 중 하나이며, React에서는 useState를 이루고 있는 개념 중 하나다.
어떤 느낌인지는 이해했지만 막상 면접 같은데에서 설명하려면 한 줄 설명이 불가능할 것 같아서 기록해본다.

## scope란?

JS에서 함수는 선언되는 동시(lexical)에 자신만의 범위(scope)를 가지게 된다. 실행될 때가 아닌 선언 될 때
그래서 JS에서는 함수의 유효한 범위를 Lexical Scope라고 부르고있다.

## 그래서 Closure란?

외부 함수의 변수에 접근할 수 있는 내부함수, 또는 작동 원리를 일컫는 말이다.

```
    function outerFunc(){
        let outerVal = 'outer';
        console.log(outerVal);

        function innerFunc() {
            let innerVal = 'inner';
            console.log(outerVal, innerVal);
        }
        return innerFunc;
    }

    let innerFunc = outerFunc();
    innerFunc();
```

이렇게 작성하면 최초 변수 선언 시 outerVal의 값이 출력되는 콘솔이 찍히고,
innerFunc() 출력 시 외부변수, 내부변수의 값이 나오는 콘솔이 출력된다.

핵심은 내부변수 출력 시 외부변수, 내부변수가 같이 나오는게 가능하게끔 내부 변수가 외부 변수를 참조할 수 있다라는 점이다.

## 이것을 이용한 예제

### 커링

클로저 함수의 외부 함수를 템플릿 처럼 사용하는 경우

```
    function callFamily (last_name) {
        return function (first_name) {
            return `Hey, ${first_name} ${last_name}`;
        }
    }

    callFamily('James')('Jones');  // Hey, James Jones

    let callLees = callFamily('Lee');

    callLees('youjin');	 // Hey, youjin Lee
    callLees('youngjae'); // Hey, youngjae Lee

    let callHollys = callFamily('Holly');

    callHollys('wendy'); // Hey, wendy Holly
    callHollys('honey'); // Hey, honey Holly
```

외부 함수의 변수 값을 정해줌으로써 그 값에 따라 다른 결과값들을 출력해주는 경우를 볼 수 있다.

### 클로저 모듈

useState에서 많이 사용하는 경우인데, 변수를 클로저 함수의 스코프에 가둬 직접 접근하거나, 값을 수정하지 않게 하기 위해 사용하는 방식이다.

```
    function counter() {
        let initialVal = 3;
        return {
            add: function() { initialVal += 2 },
            sub: function() { initialVal -= 2 },
            getVal: function() { return initialVal }
        };
    }

    let calc1 = counter();
    calc1.add(); // 3 + 2
    calc1.add(); // 5 + 2
    calc1.getVal();  // 7

    let calc2 = counter();
    calc2.add(); // 3 + 2
    calc2.sub(); // 5 - 2
    calc2.sub(); // 3 - 2
    calc2.getVal();  // 1
```
