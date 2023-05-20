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
