# Enum이란?

이름이 있는 상수들의 집합을 말하며 다른 언어에서도 굉장히 친숙한 개념 중 하나이다. (Java에서도 Enum은 애용된다)

typescript에서의 Enum은 다른 언어와 조금 다른 의도와 장단점이 있어 서술해보고자 한다

## 열거형(Enum) 종류

Enum은 열거형이라고도 한다. Typescript에서 Enum의 종류는 크게 숫자 열거형, 문자 열거형 2개로 나뉘어 지는데 각각의 특성에 대해 알아보자

### 숫자 열거형

말그래도 key, value 매칭에서 value에 숫자를 매칭시키는 경우다. enum 키워드로 정의가 가능하다.

#### 기본 정의 방식

```
enum Direction {
    UP = 1,
    DOWN,
    LEFT,
    RIGHT,
}
```

위에 Direction의 UP = 1 처럼 직접 정의해도 되고, 정의하지 않아도 된다. 정의하지 않는다면 최상단의 값 (Direction에서는 UP)을 기준으로 하나씩 하나씩 늘어날 수 있다.

물론 아에 선언을 하지 않으면 최상단의 값(Direction.UP)은 0이 되고 하나씩 늘어나는 방식이 된다.

추가로 이상하게 사용될 수는 있으나 사용한다면 밑의 방식처럼도 사용이 가능하다.

#### 중복 선언

```
enum Direction {
    UP, // 0
    DOWN = 3, // 3
    LEFT, // 2
    RIGHT, // 3
}
```

중간에 값을 집어넣는 행위는 처음에 할당된 값들에서 재선언하는 방식이기에 값을 중간에 바꾸는 것이 가능하고, 위에 Direction 기준이면 `Direction.DOWN === Direction.RIGHT`가 true가 되게끔 처리도 가능하다.

#### 계산된 멤버에 대한 처리

```
function getSomeValue() {
    return 2;
}

enum Direction {
  Up = getSomeValue(),
  Down, // error plz set initial value
  Left = getSomeValue(),
  Right, // error plz set initial value
}
```

숫자 열거형의 경우 계산된 멤버와 상수 멤버를 섞어서 사용이 가능하다.

##### 상수 멤버

계산된 멤버가 더 모르는 내용일텐데 상수 멤버를 적는 이유는 계산된 멤버는 상수 멤버가 아닌 경우를 의미하기 때문이다.

상수 멤버는 다음과 같은 표현식에 해당된다.

1. 리터럴 열거형 표현식 (문자 or 숫자 리터럴)
2. 다른 상수 열거형 멤버에 대한 참조
3. 괄호로 묶인 상수 열거 표현식
4. 단항 연산자(+, -, ~), 이중 연산자(+,-,\*,/,%, <<, >>, &, | ...);

이 외 다른 모든 경우 열거형 멤버는 계산된 것으로 간주하게 되는데, 함수 형태로 적는 것도 이에 해당된다.

##### 결론

위에 getSomeValue()라는 함수를 통해 값을 가져오는 경우는 보통 parameter에 따라 값이 틀려질 수 있으니 열거형에서는 초기값 설정에 문제가 생길 수 있어 에러를 발생시키는 것으로 추측된다.

즉 위처럼 함수를 사용하게 된다면 그 다음 값에는 반드시 상수 멤버를 사용해서 값을 초기화 시켜줘야할 필요가 있다라는 것 이다.

무엇보다 이것은 꼭 처음이 아니여도 발생할 수 있는 문제다.

### 문자 열거형

숫자 열거형이랑 동일한 기능이고 문자 열거형의 경우는 무조건 값을 초기화 해줘야만 사용이 가능하다라는 특징이 있다. 초기화 하지 않으면 숫자 열거형 처럼 되기 때문이다.

문자 열거형의 경우 숫자 열거형 처럼 값을 자동 증가 시켜주는 기능은 없지만 직렬화를 잘하는 이점이 있다.

숫자 열거형을 사용한다면 간혹 그 값에 혼동이 와 불확실하거나 잘못된 검증이 이루어질 수 있으나, 문자 열거형은 값에 유의미한 정보 자체가 들어가 열거형 멤버의 이름과는 별개로 유의미하고 읽기 좋은 값을 넣을 수 있다는 이점이 있다.

### 이종 열거형

말 그대로 이종으로 숫자와 문자 열거를 합치는 기능이 Enum에서 제공은 되나 Typescript 공식 문서에서도 굳이 사용하지 말라고 권장한다. (JS 런타임 이점을 취하기 위한 것이 아닌 경우에는 사용해도 좋다. 라고 말하고 있음)

## 열거형의 장단점

Enum은 개발자 사이에서 논란이 많은 친구다 어떤 문제 때문에 논란에 휩싸인지에 대해 더 알아보자

### 장점

#### 명시적이다.

Enum은 어찌되었든 모든 언어랑 동일하게 단순 상수와는 다른 더 명시적인 값 그리고 관리가 편하다는 장점이 있다.

동일한 값이지만 상황에 따라 더 정의하기 쉬워지기 때문이다.

```
const DIRECTION_UP = 'UP';

enum DIRECTION {
    UP = 'UP',
}

console.log(DIRECTION_UP);
console.log(DIRECTION.UP);
```

위에 콘솔은 동일한 결과를 호출한다, 다만 위와 아래의 차이는 만약 DIRECTION에 값이 추가된다면 일반 상수형식은 DIRECTION_DOWN이라는 새로운 상수를 할당하지만 enum의 경우 enum안에 DOWN을 새로 만들면 되서 상수에 대한 group화가 매우 편하고 객체 형태에서 꺼내오기 때문에 개발자가 코드 내부에서 꺼내서 쓸 때 굳이 이름을 다 외우지 않고 ENUM만 알고있어도 그 값에서 꺼내올 수 있는 장점이 있다.

#### key, value 순회

Enum은 객체 처럼 사용된다. 단점에서도 비슷한 얘기를 서술할 것이나 현재로써 장점은 Enum에 대해 key들과 value들을 Object.keys, Object.values 함수를 이용해 순회하면서 다양한 기능을 사용이 가능하다라는 장점이 있다. 이는 union 타입에서는 불가능한 기능이다.

## 열거형의 문제점

### Tree Shaking이 안됨

위의 key, value 순회 장점이 단점이 되기도 한다. Enum은 번들링 시 tree-shaking에서 제외되는 typescript 문법이다.

Enum은 빌드 과정(번들링)을 수행하게 될 때 JS 함수로 번들링이 되기 때문에 코드량을 증가시키는 원인이 되기 때문이다.

```
enum Direction {
  Up = 'Up',
  Down = 'Down',
  Left = 'Left',
  Right = 'Right',
  Test = 1,
}

// 번들링 후
"use strict";
var Direction;
(function (Direction) {
    Direction["Up"] = "Up";
    Direction["Down"] = "Down";
    Direction["Left"] = "Left";
    Direction["Right"] = "Right";
    Direction[Direction["Test"] = 1] = "Test";
})(Direction || (Direction = {}));

```

개발 편의성을 위해서 만든 상수 집합 타입이 불필요한 코드의 양을 늘리게 되는 것이다. 이렇게 되면서 추후 JS를 가져오는 과정에서 불필요한 코드의 증가로 인해 파일 크기가 늘어 JS를 import하는 속도가 늦어질 수 있다.

#### 하지만...

Typescript는 이 문제에 대해 인지하고 있다. 그리고 이 문제를 계속 방치하지 만은 않아 2가지 제안을 내놓게 되었다.

##### union type

우선은 Enum을 사용하지 않는 방법에 대해 먼저 얘기한다. Enum대신 union 타입으로 정의해 특정 타입에 대해 어떤 값 들이 있는지를 알려주는 것이다.

```
// AS-IS
enum Direction {
  Up = 'Up',
  Down = 'Down',
  Left = 'Left',
  Right = 'Right',
  Test = 1,
}

// TO-BE
type Direction = 'Up' | 'Down' | 'Left' | 'Right';
```

위 방식을 활용한다면 조금 더 편하게 특정 타입에 대한 검증이 쉬워질 것 이다.

##### const enum

Enum을 상수 값 처럼 사용하는 경우에는 위 union 타입이 그렇게까지 효과적이지 않는다. 그리고 귀찮아질 가능성이 높다. 그래서 ts에서는 const enum을 권장한다.

const enum은 기존 enum 처럼 Object.keys, values가 동작하지 않는다. 즉 함수를 만드는 것이 아닌 완전히 타입으로 선언해버리는 것이다.

이렇게 되도 union 타입처럼 함수에 어떤 타입이 들어가야 하는 지 정의를 해주게 되는데, 상수처럼 사용이 불가능하냐? 는 아니다.

```
const enum Direction {
  Up = 'Up',
  Down = 'Down',
  Left = 'Left',
  Right = 'Right',
  Test = 1,
}

let test = [Direction.Down];

// 번들링 후
"use strict";
let test = ["Down" /* Direction.Down */];
```

놀랍게도 기존의 Enum처럼 함수가 만들어지지 않고 value만 그대로 집어넣고 주석으로 어떤 상수인지 표현해주게 된다.

즉 const enum을 사용한다면 기존 enum 처럼 편하게 사용이 가능하게 되고, tree-shaking에서도 제외할 수 있게 되는 것이다. 대신 const enum에서 사용하는 값의 문자열이 매우 길고, 그것을 실제로 사용하게 되면 번들링 시에도 그 값이 나오니 조금 부담이 될 수 있다.

## 총 결론

이전에는 typescript에서 Enum이 안좋으니 쓰지 말라 라는 편견에 사로잡혀 머릿속에서 완전히 비우고 있었는데 const enum 이라는 것을 알고난 뒤 이전 언어 처럼 Enum을 활용할 수 는 있어 보인다.

개인 평가로는 props로 받아야 하는 type들에 대해서는 union type을, 상수로도 자주 사용되는 값들인 경우는 const enum을 써봐도 괜찮을 것 같다.
