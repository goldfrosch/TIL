# Record

Record는 자바의 불변(immutable) 데이터 객체를 쉽게 생성할 수 있도록 하는 새로운 유형의 클래스

## Record 활용

AS-IS 불변 객체

```
@Getter
@RequiredArgsConstructor
public final class Test {
    private final int test1;
    private final String test2;
}
```

- 접근자 메소드 추가(getter)
- 상속 방지를 위해 class 자체를 final로 선언하는 경우도 있음
- 필드값 전체를 포함한 생성자
- 인스턴스 비교를 위한 hashcode, equals 재정의
- 로깅 출력을 위한 toString 재정의

TO-BE 불변 객체

```
public record Test (int test1, String test2) {}
```

레코드 클래스를 사용하면 훨씬 더 간결한 방식으로 불변 데이터 객체를 정의할 수 있다.

- 생성자 없어도 알아서 만들어줌
- 생성자 override를 통해 특정 조건 넣는 것도 가능함

## 제한

레코드는 암묵적으로 final class (상속 불가)고, abstract로 추상화 객체도 선언이 불가하다.

(public record == public final class // true)

다른 클래스를 상속 받을수도 없지만, interface 구현(implements)는 가능하다
