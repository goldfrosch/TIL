# 팩토리 패턴

객체를 사용하는 코드에서 객체 생성 부분을 분리해 추상화한 패턴이자 상속 관계에 잇는 두 클래스에서 상위 클래스가 중요한 뼈대를 결정하고, 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정하는 패턴

상위, 하위 클래스가 분리되기 때문에 느슨한 결합을 가지며 상위 클래스에서는 인스턴스 생성 방식에 대해 전혀 알 필요가 없기에 더 많은 유연성을 가지게된다.

하위 클래스가 객체 생성에 대한 설계를 담당하고, 상위 클래스가 오히려 생성 공정을 담당하는 역할을 하게된다

## 예시

### JS

JS에서의 팩토리 패턴은 간단하게 new Object()로 구현이 가능하다.

```
const num = new Object(42);
const str = new Object('abc');

num.constructor.name; // Number
str.constructor.name; // String
```

parameter로 숫자나 문자열을 주는가에 따라 다른 타입의 객체가 생성된다.

즉 전달받은 값에 따라 다른 객체를 생성하고 인스턴스 타입 등을 정하는 방식이다.

커피 공장을 객체로 만든다는 가정 하에 새로운 class를 만들어 보자면

```
class Latte {
    constructor() {
        this.name = "latte";
    }
}

class Espresso {
    constructor() {
        this.name = "Espresso";
    }
}

class CoffeeFactory {
    static createCoffee(type) {
        const factory = factoryList[type];
        return factory.createCoffee();
    }
}

class LatteFactory extends CoffeeFactory {
    static createCoffee() {
        return new Latte();
    }
}

class EspressoFactory extends CoffeeFactory {
    static createCoffee() {
        return new Espresso();
    }
}

const factoryList = { LatteFactory, EspressoFactory };

const main = () => {
    const coffee = CoffeeFactory.createCoffee("LatteFactory");
    console.log(coffee.name) // latte;
}
```

위의 예시처럼 CoffeeFactory 상위 클래스가 중요한 뼈대를 결정하고 하위 클래스인 LatteFactory가 구체적인 내용을 결정한다.

이것을 의존성 주입으로도 볼 수 있는데, CoffeeFactory에서 LatteFactory의 인스턴스를 생성하는 것이 아닌 LatteFactory에서 생성한 인스턴스를 CoffeeFactory에서 주입해 사용하고 있기 때문.

또한 class에서 static 키워드로 정적 메소드로 선언해 사용하고 있는데 이 정적 메소드를 사용함으로써 인스턴스 안에서 메소드가 정의되 사용되는 것이 아닌 클래스 객체 자체에 정의되어 사용됨으로 클래스 기반으로 객체를 만들지 않고도 호출이 가능하며, 메모리 할당을 한번만 진행한다는 장점이 있다.

JS의 static은 Java의 static 과 매우 유사한 기능을 한다.
Java의 경우도 프로그램이 실행될 때 메모리에 고정적으로 할당이 되며, 객체 생성이 되지 않더라도 class 자체에서 선언된 값이기에 선언 없이도 사용이 가능하다.

### Java

당연히 모든 언어 중 하나인 자바로도 구현이 가능하다.

```
    enum CoffeeType {
        LATTE,
        ESPRESSO,
    }

    // get함수 따로 선언하기 귀찮아서 어노테이션 처리...
    @Getter
    abstract class Coffee {
        protected String name;
    }

    class Latte extends Coffee {
        public Latte() {
            name = "Latte";
        }
    }

    class Espresso extends Coffee {
        public Espresso {
            name = "Espresso";
        }
    }

    class CoffeeFactory {
        public static Coffee createCoffee(CoffeeType type) {
            switch(type) {
                case LATTE:
                    return new Latte();
                case Espresso:
                    return new Espresso();
                default:
                    throw new IllegalArgumentException("invalid coffee type", type);
            }
        }
    }

    public class Main {
        public static void main(String[] args) {
            var coffee = CoffeeFactory.createCoffee(CoffeeType.LATTE);
            System.out.println(coffee.getName()); // latte
        }
    }
```
