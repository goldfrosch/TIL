# Decorator

TS 5버전 업데이트 내역에 Decorator라는 자바의 Annotation의 개념과 비슷한 방식이 공개되었다.
사실 이미 존재하는 기능인데 뭔가 정식으로 낸 느낌 같지만 사용하기 너무 좋아보여서 정리해보기로 했다.

## Decorator란?

Java의 Annotation과 비슷하게 @xxx 형식으로 클래스, 메서드, 접근자, 프로퍼티 그리고 매개 변수에 첨부할 수 있는 특수한 종류의 선언이며
재사용 가능한 방식으로 클래스와 멤버를 사용자 정의하는 방식이라고 할 수 있다.

@xxx 에서 xxx은 데코레이팅 된 선언에 대한 정보와 함께 런타임에 호출되어야하는 함수여야하만 한다. 즉 데코레이터 전용 함수를 만들어야한다라는 뜻

```
    // 매개 변수도 사용 가능하다
    function testDecorator(target) {
        // 데코레이터 안에서 target이라는 매개 변수를 이용해 무언가를 사용할 수 있다.
    }

    // @testDecorator가 생성된다.
```

## Decorator Factory

데코레이터 선언에 적용되는 방식을 팩토리 형태로 사용해 인자들을 받아 사용이 가능하다.

```
    function color(value: string) {
        console.log('test color', value);

        return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
            console.log(value);
        };
    }

    class TestClass {
        @color('HELLO')
        test() {
            console.log('함수 호출됨');
        }
    }
    const t = new TestClass();
    t.test();
```

데코레이터 팩토리의 params은 decorator에 직접 들어가는 변수며, value라는 param을 받고 데코레이터 함수를 반환해 실행시키는 방식이다.

자세히 조사하려다 도저히 쓰기는 힘들 것 같아 간단하게만 정리한다.
