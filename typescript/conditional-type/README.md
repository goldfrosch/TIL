# 조건부 타입

https://www.typescriptlang.org/ko/docs/handbook/2/conditional-types.html (참고함)

실무를 진행하다가 특정 타입의 값에 따라 타입을 변화시킬 수 없냐라는 고민이 생기는 것을 봤다. 나도 실제로 생각하던 부분이기에 방법이 없나 찾아보았다.

## 조건부 타입이란?

당연히 타입스크립트는 타입 기반의 언어인 만큼 어떤 타입인지를 구분(검사)해주는 기능은 분명 존재할 것이다. 이 조건부 타입을 통해 입,출력 타입간의 관계를 설명하는데 도움이 되지 않을까 한다.

```
  interface Animal {
    live(): void;
  }
  interface Dog extends Animal {
    woof(): void;
  }

  type Example1 = Dog extends Animal ? number : string; // Example1 type is number

  type Example2 = RegExp extends Animal ? number : string; // Example2 type is string
```

조건부 타입은 js의 삼항연산자를 이용해 새로운 타입을 지정하는 것이 가능하다. 하지만 이렇게되면 하나의 분기처리만 하거나 혹은 조건부를 계속 써가면서 하는게 그렇게까지 유용해보이지는 않는다.
하지만 Generic과 함께 사용할 때 매우 유용한 것을 볼 수 있다.

- Generic을 안쓰고 할 경우

```
  interface IdLabel {
    id: number /* some fields */;
  }
  interface NameLabel {
    name: string /* other fields */;
  }

  function createLabel(id: number): IdLabel;
  function createLabel(name: string): NameLabel;
  // 타입 구분하는게 쪼금 이해하기 어렵다.
  function createLabel(nameOrId: string | number): IdLabel | NameLabel;
```

이런식의 작성은 매번 API전체에서 할 때마다 유니온타입을 계속 입력해주면 불편하다.
이런식은 케이스별로 확실한 경우면 괜찮지만 다양한 타입들이 추가되면 추가될수록 로직이 매우 복잡해진다.

- 여기서 Generic을 사용할 경우

```
  type NameOrId<T extends number | string> = T extends number
  ? IdLabel
  : NameLabel;
```

아까보다 그나마 T의 속성이 어떤 타입이냐에 따라 구분해주기 때문에 조금 더 명시적임을 볼 수 있다.

## 조건부 타입으로 제한하기

특정 타입에서 어떠한 타입의 값을 가져오고 싶을 경우 타입 가드에서도 어느정도 구체적인 타입을 추론해 좁혀주듯이 (ex. typeof, instanceof ...) 조건부 타입이 일치한 후의 분기처리는 비교한 타입에 따라 Generic을 제한할 수 있다고 함.

```
  type MessageOf<T> = T["message"];
  Type '"message"' cannot be used to index type 'T'.
```

위의 예제에서는 정말로 T가 message라는 속성값을 가지고 있는지 (혹은 원시 타입인지도 모르는...) 모르기 때문에 에러를 발생시킬 수 밖에 없다. 하지만 Generic에 조건부 타입을 제한시켜주면서 특정 타입에 대해 분기처리가 쉽게 가능해진다.

```
  // message라는게 뭔지는 모르지만 일단 이게 존재하는 객체라는 것은 타입에서 확실하게 인지시킴.
  type MessageOf<T extends { message: unknown }> = T["message"];

  interface Email {
    message: string;
  }

  // Email의 message 타입은 string이므로 해당 타입은 string이 된다.
  type EmailMessageContents = MessageOf<Email>;
```

위의 내용은 가장 이상적인 (message라는 타입이 존재할 경우) 케이스다. 해당 조건부 타입 조건에 만족하지 못했을 경우의 분기처리도 필요하다

```
  type MessageOf<T> = T extends { message: unknown } ? T["message"] : never;

  interface Email {
    message: string;
  }

  interface Dog {
    bark(): void;
  }

  type EmailMessageContents = MessageOf<Email>; // message라는 타입이 존재하고 그 타입은 string이다.

  type DogMessageContents = MessageOf<Dog>; // message라는 타입이 존재하지 않아 삼항연산자로 인해 해당 타입은 never이 된다.
```

객체 뿐만 아니라 배열 형태 타입에 대한 분기나 Flatten타입에 대한처리도 매우 쉬워질 것이다.

```
  type Flatten<T> = T extends any[] ? T[number] : T;

  type Str = Flatten<string[]>; // Str = string;

  type Num = Flatten<number>; // Num = number;
```

extends를 이용하여 다양한 분기처리를 할 수 있고, 그 분기에 대해 특정 타입을 어떻게 지정하느냐도 매우 쉽게 가능하다라는 것.

## 조건부 타입 내에서 추론하는 방법

위에서는 조건부 타입을 이용해 특정 타입을 추출해 적용하는 방식이였다면, 지금은 조건부 타입을 더욱 쉽게 정의하는 방법이다.
infer라는 키워드를 이용하면 어느정도 추론이 가능하다.

### infer란?

타입 조건식에 사용하며 특정 타입의 조건이 일치하는지를 검증하는 방식이다.

```
  Element<number> extends Element<infer U>
```

위의 형식처럼 사용하며 반드시 extends랑 같이 사용된다. infer U를 통해서 U 타입은 number타입으로 추론되고, 이후 참인 경우에 대응되는 식에 추론된 U타입을 이용한다.
쉽게 말하면 U가 추론 가능한 타입인가를 알려주는 boolean값이다.

ex. 외부 참고자료
![img](https://miro.medium.com/v2/resize:fit:720/format:webp/1*mki-531FqeSL6R5dhWbhCA.jpeg)
위의 그림을 보면 특정 타입을 Generic에 삽입 후 U타입의 배열형태의 타입으로 추론이 가능한지 검증 후 boolean값에 따라 U타입을 그대로 사용하던가, T에 넣은 타입을 그대로 사용하던가를 return해주는 방식으로 사용이 가능해진다라는 것이다.

### 사용하는 방식?

inter를 조금 더 이용하면 function 형식처럼도 이용이 가능하다.

```
  // 특정 함수를 통해 Type이 특정 타입을 리턴해주는 함수라면 리턴해주는 타입을, 아니면 never로 선언해주는 함수
  type GetReturnType<Type> = Type extends (...args: never[]) => infer Return
  ? Return
  : never;

  type Num = GetReturnType<() => number>; // return 타입 number로 number를 return

  type Str = GetReturnType<(x: string) => string>; // return 타입 string으로 string을 return

  type Bools = GetReturnType<(a: boolean, b: boolean) => boolean[]>; // return 타입 boolean[]으로 boolean[]을 return
```

다만 declare를 이용해 함수의 타입을 선언한 경우에는 마지막 시그니처 (아마 모든 케이스로 허용되는 것으로 보이는) 함수 타입으로 추론하게 된다. 즉 모든 타입을 비교해 추론해주지는 않는다라는 것.

```
  declare function stringOrNum(x: string): number;
  declare function stringOrNum(x: number): string;
  declare function stringOrNum(x: string | number): string | number;

  type T1 = ReturnType<typeof stringOrNum>; // return (string | number)
```

## 분산적인 조건부 타입

유니온 타입을 같이 사용할 경우에 발생하겠지만 Generic타입에 조건부 타입을 선언할 경우 분산적으로 동작한다.

```
  type ToArray<Type> = Type extends any ? Type[] : never; //any라서 무조건 Type[]로 return
```

이 경우에 ToArray의 Type에 유니온 타입을 적용시킬 경우 똑같이 유니온 타입으로 타입을 return시켜준다라는 것.

```
  type ToArray<Type> = Type extends any ? Type[] : never; //any라서 무조건 Type[]로 return

  type StringOrNumber = ToArray<string | number>; // string[] | number[]
```

Generic에 유니온 타입을 넣을 때 부터 각각의 유니온 타입에 대해 검증을 해주고 검증한 타입들을 유니온 타입 형태로 묶어서 하나의 타입으로 가져다주는 방식이다.

return값이 유니온 타입이 아닌 하나의 타입을 return받고 싶을 경우에는 타입을 검증할 경우 전부 대괄호로 묶어서 확인하면 방지할 수 있다.

```
  type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never;

  type StrArrOrNumArr = ToArrayNonDist<string | number>; // (string | number)[]
```
