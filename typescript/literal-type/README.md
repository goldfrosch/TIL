# 리터럴 타입

기본 집합 타입의 더 하위 즉 조금 더 구체적인 타입이라고 할 수 있다.
리터럴 타입은 string, number, boolean 3가지를 이용해 구현이 가능하다.

## 유형

- String Literal

  문자열 리터럴 유형이라고도 한다. 유니온 타입, 타입 가드, 타입 aliasing과도 잘 어울리는 방식인데 union type 처럼 특정 string을 선언해 두는 Java의 Enum 형태로 봐도 무방하다.

  Ex.

  ```
  type Test = 'hi' | 'bye' | 'go'
  ```

- Number Literal
  문자열 리터럴과 동일한 형식이다.
  Ex.

  ```
  type Test = 0 | 1 | 2;
  ```

- Boolean Literal
  위의 2개와 정말 같다기에는 true, false 옵션이 2개 뿐이라 애매하지만 둘 중 하나로만 타입 제한을 걸 수 있다.

## 주의사항

const a = 'test';
같이 상수로 값을 지정하는 경우에는 리터럴 타입으로 설정이 되지만 값이 변경 가능한 var나 let을 사용하게 된다면 리터럴 타입이 아닌 그냥 원시타입으로 지정된다. (값이 언제든지 바뀔 수 있으니 굳이 리터럴로 처리할 이유가 없기 때문)
