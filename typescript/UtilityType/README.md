# Utility Types

타입스크립트에서는 일반적인 타입의 변환을 더 쉽게하기 위해 몇 가지의 util 타입을 제공한다.

## Partial< T >

Generic T에 들어가는 타입의 모든 property를 optional 형태로 만들어 반환하는 역할을 한다.

```
    interface Person {
        name: string;
        age: number;
        gender: 'M' | 'W';
    }

    type PersonOptional = Partial<Person>;
    //{
    //    name?: string;
    //    age?: number;
    //    gender?: 'M' | 'W';
    //}

    const person: Person = {
        name: 'test',
        age: 10,
    }; // Property 'gender' is missing in type

    const personOption: PersonOptional

    const person1: personOption = {
        name: 'test',
        age: 10,
    }; // success
```

## Required< T >

Partial과는 반대로 T에 들어가는 모든 property들이 필수 값이 된다.

## ReadOnly < T >

해당 타입으로 선언될 경우 T의 객체가 한번 완성되면 그 이후로는 절대 수정이 불가능한 readonly 객체로 선언된다.

```
    interface Person {
        name: string;
        age: number;
        gender: 'M' | 'W';
    }

    const person: Readonly<Person> = {
        name: 'test',
        age: 24,
        gender: 'M',
    };

    person.age = 25 // Cannot assign to 'age' because it is a read-only property
```

### Readonly type vs as const

Readonly type은 타입에서, as const는 상수 단에서 사용이 된다.
주로 하나의 객체를 타입화 및 상수화 하기 위해 as const를 자주 사용하지만 가변 타입을 선언하는 방식을 사용할 것 이라면 Readonly 유틸 타입을 사용하는 것도 방법인 듯 보인다.

## Record < K, T >

K가 key의 타입이 되고 T에 key에 대한 타입을 지정할 수 있는 방식이다.
K에는 반드시 string | number | symbol 의 타입이 들어가야한다.
