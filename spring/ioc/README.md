# IOC란

Inversion of Control을 줄여 부르는 단어로 영어의 뜻 그대로 제어의 역전을 의미한다.

메소드나 객체의 호출작업을 개발자가 결정하는 것이 아닌 외부에서 결정하는 것 (의존성 주입[DI]을 한다)을 의미한다.

## DI (Dependency Injection)

개발을 하다보면 객체 간에는 의존성이 생기기 마련이다.

```
public class FishBread {
	private Bread bread;
	private Sauce sauce;

	public FishBread() {
		bread = new Bread();
		sauce = new Sauce();
	}

	public void changeSauce(Sauce newSauce) {
		// 이 메소드는 예시 코드에는 없지만 대충 함수라고 알아먹자
		sauce.setSauce(newSauce);
	}
}
```

위 클래스 객체에서 changeSauce가 실행될 때 setSauce를 위해 Sauce 클래스가 필요하다.

이런 상황을 FishBread가 Sauce에 의존성을 가지고 있다라고 한다.

의존성이 꼭 나쁘다는 아니지만 의존성이 생길 때의 문제점은 다음과 같다.

### 문제점

1. Unit Test의 어려움.

- - 내부에서 직접 생성하는 객체를 mocking해서 사용하는 것이 불가능해져 단위 테스트가 어려워진다.

2. Code 변경의 이슈 가능성 발생

- - 위 코드는 당장 문제는 없지만 Sauce 즉 의존하고 있는 클래스가 변경되는 상황이 되면 FishBread도 같이 변경을 해줘야하는 상황이 된다.

### DI 활용

위 문제점의 이유로 인해 의존성을 주입하는 방식이 생겼다.

#### 의존성 주입 방법

1. 생성자를 통한 주입

```
public class FishBread {
	private Bread bread;
	private Sauce sauce;

	public FishBread(Bread bread, Sauce sauce) {
		this.bread = bread;
		this.sauce = sauce;
	}
	.
	.
	.
}
```

2. setter를 이용한 주입

```
public class FishBread {
	private Bread bread;
	private Sauce sauce;

	public void setSauce(Sauce sauce) {
		this.sauce = sauce;
	}
	.
	.
	.
}
```

위 두가지 방법을 이용해 주입함으로써 외부에서 의존성을 주입하는 방식이기 때문에 내부에서 직접 주입하는 것보다 예외처리나, 객체간의 결합이 감소함으로써 재사용이 더 용이해질 수 있다.

### Spring에서의 활용

스프링에서는 IOC Container가 개발자 대신 xml 파일 정의 or @Controller, @Components 등으로 Bean객체를 생성하고 의존성을 대신 주입시켜줄 수 있다.

## IOC Container

Spring에서 사용자가 작성한 메타데이터(xml bean 주입 or Annotation 선언)에 따라 Bean 클래스를 생성 및 관리하는 Spring의 핵심 개념

### 컨테이너 등록 방법

방법은 크게 2가지가 있다.

#### Component Scanning

@ComponentScan 어노테이션과 @Component 어노테이션을 사용해서 Bean을 등록하는 방법이다.

ComponentScan은 어느 지점부터 컴포넌트를 찾으라고 알려주는 역할을 하고, Component는 실제로 찾아서 빈으로 등록할 클래스를 의미한다.

Spring-Boot의 경우 SpringBootApplication 어노테이션은 ComponentScan으로 하위에 컴포넌트 어노테이션을 탐색 후 등록하는 역할을 수행하고, Component를 등록하는 커스텀 어노테이션은 자주 사용하는 (RestController, Service, Repository) 어노테이션이 있다.

#### Bean 설정 파일에 등록하기

Bean 설정 파일은 XML과 자바 설정 파일 2가지 방법으로 작성하는데 최근에는 자바 설정파일을 조금 더 많이 사용한다.

자바 설정파일의 경우 클래스를 만들어서 작성이 가능하며 ~~~~Configuration이라는 이름으로 명명해 설정파일을 만든다. (스프링 국룰 컨벤션)

그리고 그 클래스에 Configuration 어노테이션을 붙이고 그 안에 Bean 어노테이션을 사용해 직접 빈을 지정한다.

```
@Configuration
public class SampleConfiguration {
    @Bean
    public SampleController sampleController() {
        return new SampleController;
    }
}
```
