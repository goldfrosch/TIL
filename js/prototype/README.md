# Prototype Object

Java와 C++과 같은 클래스 기반 객체지향 언어와는 다르게 JS는 프로토타입 기반 객체지향 언어라고 부른다.

클래스 기반의 객체지향 언어는 객체 생성 이전에 class를 정의하고 그 틀을 기반으로 인스턴스를 생성해주었다면, 프로토타입 기반의 객체지향 프로그래밍 언어는 클래스 없이 객체를 생성할 수 있다.

## JS의 객체 생성 방법

JS에서 객체를 생성하는 방법은 매우 단순하다. 별다른 클래스 없이 중괄호로 묶어서 생성은 가능하지만 이런 모든 객체는 자신의 부모 역할을 담당하는 객체와 연결되어있다.

그래서 순수한 Object를 콘솔로 찍어보게 되면 단순하게 내가 넣은 값만 아니라 내가 넣지 않은 값을이 많이 들어가는 모습을 볼 수 있는데 이것은 객체지향의 상속 개념과 같이 부모 객체의 property or 메소드를 알아서 상속받아 사용할 수 있도록 해준다.

이런 부모 객체를 Prototype(객체) 라고 부른다. 객체라는 말은 생략해서도 부른다.

```
const obj = {
    a: 'test',
    b: 'hi!',
}

console.log(obj.hasOwnProperty('a')); // true가 나오는데 우리는 hasOwnProperty라는 함수를 만든 적이 없는데, 만들지 않아도 이미 객체를 만든 시점에서 JS가 Object라는 객체의 옵션들을 상속시켜 편하게 쓸수 있게된다.

console.log(obj); // 내가 넣은 값 말고도 [[Prototype]] 이라는 객체가 있고 이 객체안에 내가 넣지 않았던 메소드나 객체들이 담겨있다.
```

### Object의 prototype

ECMAScript 기본 스펙에서는 JS에서 선언하는 모든 객체에는 `[[Prototype]]`이라는 인터널 슬롯을 가지고, 이 값은 null이거나 객체이며 상속을 구현할 때 사용하게 된다. 이 객체의 데이터 property는 주로 get을 이용해 정보를 가져올 때를 위해 상속되어 자식 객체의 property처럼 사용이 가능하지만 set의 엑세스는 허가되지 않는다.

또한 이 `[[Prototype]]`은 `__proto__`로 접근이 가능한데, 놀랍게도 기존의 JS 동작과는 다르게 Object.prototype과 obj.`__proto__`가 일치하는 모습을 볼 수 있다.

비슷하게 Array같은 배열이나 함수에서도 동일하게 Property가 생성되는 것을 알 수 있다.

## [[Prototype]] 과 prototype 프로퍼티

위에서 모든 객체는 자신의 prototype 객체를 가리키는 `[[Prototype]]` 인터널 슬롯을 가지며 상속을 위해 사용되는 것을 확인했다. 함수도 동일하게 `[[Prototype]]`을 소유하지만 일반 객체와는 달리 prototype 프로퍼티도 소요하게 된다.

```
function Person(name) {
    this.name = name;
}

const foo = new Person('Hi');

console.log(Person); // [[Prototype]]이 존재하지 않고 함수 자체가 출력된다.
console.log(foo); // 동일하게 객체처럼 동작한다. [[Prototype]]이 존재함.
```

### 함수의 **proto**는?

함수의 Prototype Object는 함수 생성자이며, 그 안에 `[[Prototype]]`객체가 따로 존재하기 때문에 실제 그 함수로 만들어진 객체의 prototype을 얻으려면 1 depth를 더 접근해서 정보를 알아와야 한다. (`Person.prototype.__proto__`)
