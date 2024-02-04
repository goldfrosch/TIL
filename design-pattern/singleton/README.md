# 싱글톤 패턴

싱글톤 패턴은 하나의 클래스에는 하나의 인스턴스만을 가지는 패턴을 의미한다.

하나의 클래스를 기반으로 여러 인스턴스를 만들어서 사용해도 무방하나
그렇게 하지 않고 하나의 클래스를 만들면 하나의 인스턴스만을 만들어서 사용하게한다.

DB 연결 모듈에 주로 사용하는 방식이며 Spring framework도 이 디자인 패턴을 사용한다.

- 클래스: Java의 클래스를 의미하며 가상의 틀을 말함 (객체지향의 개념의 class를 말함)
- 인스턴스: class의 정의를 통해 만들어진 객체
- 객체: class의 타입으로 선언되었을 때 그것을 객체라 부르고 실제로 메모리에 할당되어 사용 되는 것을 인스턴스라고 부름
- Java에서 객체지향은:
  - 클래스로 부터 인스턴스화된 객체를 사용하는 언어라고 할 수 있다.

## 예시

### JS

JS에서는 리터럴 {} or new Object로 객체를 생성하게 되면 다른 어떤 객체와도 같지 않기 때문에 이 자체만으로도 싱글톤 패턴을 구현할 수 있다.

JS에서는 같은 property가 들어가 있어도 다른 객체라고 인지하기 때문이다. Typescript에서도 특정 object의 타입을 단순 type으로 구분하지 못하는 것도 이런 이유다

조금 어거지지만 예시를 들어보면

```
const obj1 = { a: 27 };
const obj2 = { a: 27 };

console.log(obj1 === obj2); // false
```

obj1과 obj2는 같은 값처럼 보이지만 새롭게 Object Class 기반으로 선언된 각각의 독립적인 인스턴스라서 같은 값이라고 보지 않는다.

이 방식이 싱글톤 패턴을 설명하기에는 매우 부족하나 이런 방식이다 정도는 알 수 있다.

조금 더 잘 설명하려면 JS class를 직접 사용해보면 알 수 있다.

```
class Test {
    constructor() {
        // class 내부에 instance가 선언되어 있지 않지만 JS라서 가능했음... 새로 생긴다고 생각하면 됨.
        if (!Test.instance) {
            // 없으면 this 즉 해당 class로 할당함
            Test.instance = this;
        }
        return Test.instance;
    }

    getInstance() {
        return this;
    }
}

const a = new Test();
const b = new Test();

console.log(a === b); // true
```

위 처럼 하나의 class를 같은 모듈로 사용하게 하는 것. 이미 선언된 class가 있다면 그것을 계속 사용하는 방식이 싱글톤 패턴이다.

위 방식으로 처리해 하나의 class에서 여러개의 객체를 선언해도 하나의 인스턴스를 바라보게 된다.

### Java

```
class Test {
    private static class testInstance {
        // class 내부에 하나의 final static instance를 생성함.
        private final static Test INSTANCE = new Test();
    }

    public static Test getInstance() {
        // 이미 만들어진 객체를 기반으로 return해 인스턴스로 활용
        return testInstance.INSTANCE;
    }
}

public class Main {
    public static void main(String[] args) {
        Test a = Test.getInstance();
        Test b = Test.getInstance();

        // 같은 메모리 주소에 존재하는 값을 사용하기 때문에 hashCode값이 동일하다.
        System.out.println(a.hashCode() == b.hashCode()); // true
    }
}
```

자바의 경우는 위의 예시처럼 하나의 class를 생성 시 미리 객체를 내부에서 만들고 그 객체를 이용한 인스턴스만 활용할 수 있게끔 처리해 싱글톤 패턴을 유지할 수 있다.

## 장점

DB 연결 모듈이 주로 이런 방식을 사용한다.

DB 커넥션 관련 모듈은 하나의 인스턴스가 선언되면 그것을 계속 사용하면 되지, 굳이 선언할 때 마다 커넥션을 새로 걸고 인스턴스를 새로 만들 필요는 없다. (계속 커넥션을 거는 것도 DB 입장에서는 부담이기도 하니까...)

JS 기반으로 예시를 하나 더 들어본다면...

```
const URL = "mongodb://localhost:27017/kundolapp;
const createConnection = (url) => ({
    url,
});

class DB {
    constructor(url) {
        if (!DB.instance) {
            DB.instance = createConnection(url);
        }
        return DB.instance;
    }

    connect() {
        return this.instance;
    }
}

const a = new DB(URL);
const b = new DB(URL);

console.log(a === b) // true;
```

간결한 예시코드지만 createConnection에는 훨씬 더 많은 로직이 들어갈 것이다. (DB 서버에 연결하는 로직이라던가...)

이미 연결이 되어있으면 연결된 모듈만 가져오게 처리함으로써 DB 연결하는 인스턴스 생성 비용을 아낄 수 있다.

- 만약 DB connection instance를 계속 만들게 된다면?
  - DB connection을 끊고 새로운 연결을 거는 것 자체가 DB에서는 부담이 있고 시간이 든다. 이런 경우가 많아지면 DB 부담이 많이 심해질 수 있다.
  - 리소스 부족이 생긴다. 동시에 처리할 수 있는 연결 수가 있는데 이 제한을 초과하게 될 때 lock이걸릴 수도 있다.
  - 계속 인증을 해야하니 그만큼의 리소스가 추가로 든다.

## 싱글톤의 단점

### TDD

싱글톤 패턴은 TDD(Test Driven Development)를 할 때 걸림돌이 된다.

TDD는 주로 단위 테스트를 하게 되는데 단위 테스트는 어떤 순서로든 진행이 가능해야한다.

하지만 싱글톤 패턴의 경우 미리 생성된 하나의 인스턴스를 기반으로 동작하기 때문에 순서 상관없이 동작하는 것이 어렵고 각각의 독립적인 인스턴스를 만들어서 진행하기도 어렵다.

### 의존성 주입

싱글톤 패턴은 사용하기가 쉽고 실용적이다만 모듈 간의 결합을 강하게 만들어 떼기가 어렵다라는 단점이 있다.

모듈 간의 결합이 강하다면 각각마다 의존성들이 생겨 유지보수가 어렵다는 단점이 있다.

이 때 의존성 주입 (Dependency Injection)을 통해 모듈 간의 결합을 조금 더 느슨하게는 할 수 있다.

#### 싱글톤 패턴의 의존성

##### AS-IS

메인 모듈 -> 하위 -> 하위

| -> 하위

| -> 하위

##### TO-BE

메인 모듈 <- 의존성 주입자

하위 <---------|

하위 <--------|

하위 <-------|

| -> 하위

모든 것들을 메인에서 의존을 가지고 있기 보다 별도의 의존성 주입자를 만들고 의존성 주입자가 메인 모듈의 의존성을 가로채 메인 모듈이 간접적으로 의존성을 주입하게 하는 방식이다.

이 방식을 통해서 메인 모듈(상위)는 하위 모듈에 대한 의존성이 떨어지게 되는데 이런 방식을 흔히 디커플링이 된다 라고도 부른다.

이것을 가장 잘 활용한 프레임워크가 Spring Framework다.

// TODO: 스프링 프레임워크랑 bean 어노테이션에 대해 구체적으로 적어보기

#### 장점

모듈들을 쉽게 교체할 수 있는 구조가 되어 테스팅하기 쉽고 마이그레이션 하기도 수월하다.

또한 구현할 때 추상화 레이어를 넣고 그것을 기반으로 한 구현체를 넣어 주기 때문에 에플리케이션 의존성 방향이 일관되고, 쉽게 추런이 가능하며 관계가 명확해질 수 있다.

#### 단점

모듈들이 다 분리되면서 클래스 수가 늘어 복잡성이 증가될 수 있고 런타임 패널티가 생길 수 있다. (복잡성 문제 때문에 순환 참조 이슈도 발생하지 않나 싶음)

#### 원칙

- 상위 모듈은 하위 모듈에서 어떠한 것도 가져오지 않아야 한다.
  - React에서 상위 컴포넌트에서 하위 컴포넌트 정보를 가져오지 않는 단방향 원칙 같은 느낌.
- 둘다 추상화에 의존해야하고 추상화는 세부 사항에 의존하지 말아야 한다.
