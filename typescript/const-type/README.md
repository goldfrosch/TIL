# const Type?

Typescript 5부터 나온 개념으로 객체의 리터럴 유형을 가져오기 더 편하게 그리고 함수 내 parameter에도 literal을 가져올 수 있게 하기 위한 개념이다.

TS에서의 객체 타입 동작 원리에 대해 살펴보면

```
const testLiteral = {
    hey: 'bro',
    poo: 'poof',
}

// 객체는 내부 property를 const로 선언해도 직접 변경이 가능하기 때문(참조 타입이라...)에 string으로 배정 받는다.
const testLiteral: {
  hey: string;
  poo: string;
}

const thisIsLiteral = {
    hey: 'bro',
    poo: 'poof',
} as const;

// as const로 상수화 시킴으로써 const를 직접 변경하지 않는다고 못박아 버리니 literal 타입이 될 수 있다.
const thisIsLiteral: {
    readonly hey: 'bro',
    readonly poo: 'poof',
};
```

하지만 필요하다면 이 객체들을 함수로 보내고 Generic을 이용해 리터럴로 반환을 가져와야하는 경우가 있다.

```
const parseNames = <T>(names:<T>) => {
  return names
}

const result = parseNames({
  paul: 'McCartney',
  john: 'Lennon',
})
```

하지만 parseNames는 {paul: string, john: string} 타입을 return한다. 인라인 객체니까 수정이 가능하다고 판단해 string으로 변환시키는 것이다.

물론 처음 위에 적어둔 대로 as const로 선언해 return시키는 것도 방법이다.

```
const parseNames = <T>(names:<T>) => {
  return names
}

const result = parseNames({
  paul: 'McCartney',
  john: 'Lennon',
} as const)
```

이러면 밑에 처럼 정상적으로 타입은 가져올 수 있으나 매번마다 인라인 객체에 as const를 쓸수도 없는 노릇이다.

```
{
    readonly paul: "McCartney";
    readonly john: "Lennon";
}
```

이래서 ts 5버전 부터 출시한게 const type으로 다음과 같이 구현할 수 있다.

```
const parseNames = <const T>(names:<T>) => {
  return names
}
```

위처럼 Generic T 타입 앞에 const를 선언함으로써 자동으로 타입에 상수 타입을 적용시켜 literal 타입을 사용할 수 있게 된다.

해당 방식을 가장 잘 이용하는게 ts-pattern 라이브러리다.
ts-pattern 5버전 이상부터는 TS 5버전 이상만 사용이 가능한 이유가 이 const type을 사용하기 때문이고, const type을 이용했기 때문에 인라인 객체를 넣어도 타입 추론을 원활하게 할 수 있다.
