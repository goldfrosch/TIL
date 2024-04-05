# Type과 Interface

typescript에서 타입을 정의할 때는 type과 interface 2개를 주로 사용하는데 각각의 차이점과 장단점에 대해 알아보자

## Type

type 선언은 다른 말로 type aliasing이라고 불리며 말 그대로 특정 타입의 별칭화 작업을 하기 위한 용도로 주로 사용된다.

type의 경우 모든 타입들이 전부 허용되는데 interface는 객체로만 선언이 가능함에 비해 type의 경우는 primitive한 타입부터 object, array, Tuple, function, Enum 등 모든 타입의 선언이 가능하다.

```
type A = number | string | [string, number];
type B = (a: string) => void;
```

### & and |

type의 경우 & 연산자와 | 연산자를 이용해서 타입을 지정 가능하다.

#### &

밑의 코드처럼 작성해 두개의 타입을 합치는 and 연산자처럼 동작시킨다.

```
type A = {a: number} & {b: number}
const a: A = {a: 0, b: 2};
```

& 연산자의 특징 중 하나는 겹치는 타입에 대해 재선언이 가능하고 재선언하는 경우 그 겹치는 타입들이 서로 합쳐지게 된다.

```
type A1 = {a: string};
type B1 = {a: number} & A1;
const a: B1 = {a: 0} // type error a는 string & number기에 불가능한 타입이라 never로 정의된다.


type A2 = {a: () => 'df'};
type B2 = {a: () => string} & A2;

const b: B2 = {a: () => 'df'}; // 결과 타입이 'df' & string 타입이기에 가능
```

#### |

흔히 유니온 타입이라고도 많이 부르며 특정 타입 중 택1을 시킬 때 주로 사용한다.

```
type B = {a: number} | {b: number}
const b = {b: 0};
```

## Interface

인터페이스는 기본적으로 객체 형식으로 타입 선언을 하며 type보다 정의 가능한 타입의 pool은 좁지만 다른 방면으로 여러 이점이 존재한다.

그리고 타입과 다르게 함수를 선언할 때 arrow function 처럼 사용하지 않고 :로 사용한다.

```
interface A {
    (number1: number): number;
}

const a: A = (num1) => 0;
```

### Declaration Merging

Typescript에서 사용되는 특이한 개념 중 하나로 선언 병합이라고도 불리는 개념이다.

interface 뿐만 아니라 namespace와 class에도 해당되는 개념이나 우선 interface 기준으로 알아보자면

같은 이름의 interface를 재선언하게 된다면 그 interface는 서로 합쳐져 기존의 property들이 합쳐지는 새로운 타입이 완성되는 개념이다.

```
interface A {
    a: number;
    b: number;
}

interface A {
    c: number;
}

const a: A = {a: 0, b: 0, c: 0}

```

다만 위의 타입에 이미 선언된 부분을 새로운 interface에 재선언 하게 되는 경우 compile error가 발생하게 되어 중복 타입 선언은 불가능하다.

```
interface A {
    a: number;
    b: number;
}

interface A {
    b: string; // same type error
}

const a: A = {a: 0, b: 'd'}; // 먼저 선언한 타입이 기준이기에 b는 number여야 한다고 에러가 나타나게 됨
```

### extends

interface의 고유한 특징 중 하나로 상속이 된다. 일반 type의 경우 extends문을 쓰지 않고 &나 | 연산자로 처리하는 것에 반해 extends로 이전 타입을 받아온다.

extends로 특정 타입을 상속시키는 경우 재선언(override)는 불가능하고 새로운 타입을 지정해줘야한다.

## 결론

아직 많이 부족하지만 type과 interface 각각의 차이에 대해 알아보았는데 용도에 맞게 더 편하게 쓰면 좋을 것 같다.
