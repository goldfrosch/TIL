# Annotation

Annotation은 Java 5부터 추가된 문법 요소로, 코드 사이에 주석처럼 쓰이며 특별한 의미 혹은 기능을 수행하도록 하는 기술이다.

## 용도

- 컴파일러에게 코드 작성 문법 에러 체크를 할 수 있도록 정보 제공
- 소프트웨어 개발 툴이 빌드나 배치 시 코드를 자동으로 생성할 수 있도록 정보 제공
- 런타임 동안 특정 기능을 실행하도록 정보 제공

## 순서

- 어노테이션 정의
- 클래스에 어노테이션 배치
- 코드 런타임 중 Reflection을 이용해 추가 정보 획득 후 기능 실행

## Spring의 어노테이션 종류

### @ComponentScan

@Component(Controller, Service 등도 이에 해당됨) 어노테이션이 붙은 클래스 Bean들을 찾아 Context에 Bean을 등록해주는 어노테이션

해당 어노테이션은 직접 사용하지 않아도 @SpringBootApplication 어노테이션에 내장되어 있고, SpringBootApplication 어노테이션은 최상단에서 선언되기에 Spring 내부에 선언되는 모든 Component들을 알아서 등록해준다.

### @Component

개발자가 작성한 Class를 Bean으로 등록하기 위한 Annotation으로, 위의 ComponentScan 기능에 의해 스캔될 때 Component 어노테이션이 적용된 클래스를 식별 후 빈을 생성 해 ApplicationContext에 등록한다.

### @Autowired

Autowired는 스프링 컨테이너에 등록된 빈에게 의존 관계 주입이 필요할 때 DI를 도와주는 어노테이션이다.

스프링 컨테이너에 빈들을 모두 등록한 후에 의존성 주입 단계가 이루어진다.
이 때 @Autowired 어노테이션이 부여된 메서드가 실행되며 필요한 인스턴스를 주입한다.

```
@Service
public class UserService {

  @Autowired
  private UserRepository userRepository;

  // ...
}
```

예를 들어 위의 경우 UserRepository를 주입시키는 경우 Autowired를 이용하게 되면 UserRepository 유형의 bean에 대한 application context를 검색 후 주입한다.

Autowird를 사용하면 자동 종속성 주입을 허용해 코드의 양을 줄여주는 어노테이션이라고 할 수 있다.

#### Bean 탐색 방법

#### @Autowired를 지양해야 하는 이유?

1. Autowired를 사용하게 되면 클래스 간의 강한 결합으로 이어진다.
2. 순환 참조가 발생할 수 있다.
3. final을 붙일 수 없어 객체의 변경이 가능해진다.

Autowired는 클래스의 종속성을 자동으로 연결한다. 편리한 기능이지만 종속성과 밀접하게 연결되게 하는 것을 의미하기도 해 코드의 유연성이 떨어지고 장기적인 유지관리가 어려워질 수 있다.

#### @Qualifier

의존성 주입 시 빈을 선택하고 주입하는 어노테이션

같은 인터페이스를 구현한 여러 클래스가 있을 때 명시적으로 Bean을 선언해 주입하는 방식을 의미함
