# 포인터와 참조

C++에서는 특정 변수의 메모리 주소값을 가져와 작업하는 경우가 많다.

메모리 주소 값을 선언하는 pointer와 그와 비슷하게 저장하지만 다른 식으로 동작하는 참조(reference)에 대해 알아보자

## Pointer 와 Reference 차이점

|                                    | Pointer               | Reference                 |
| ---------------------------------- | --------------------- | ------------------------- |
| 저장 정보                          | 메모리 주소           | 메모리 주소               |
| 재할당 가능 여부(다른 주소 값으로) | 가능                  | 불가능                    |
| Nullable                           | 가능 (nullptr 선언)   | 불가능 (초기값 선언 필수) |
| 접근 방법 (변수의 정보 값)         | \*Variables           | Variables                 |
| 접근 방법 (메모리 주소)            | Variables             | &Variables                |
| 주소 변경 방법                     | PointerVar = &NewVar  | 불가능                    |
| 정보 값 변경 방법                  | \*PointerVar = NewVar | RefVar = NewVar           |

### 참조의 사용 방법

보통 함수의 매개변수는 넣은 값을 그대로 복사해서 가져와 내부적으로 사용하는 방식이다. 그렇기 때문이 거의 모든 언어의 가장 큰 특징 중 하나는 함수에 넣은 매개변수의 값을 변경하는 것이 불가능하다 라고도 선언한다.

```
// Java임

int a = 10;

void Test(int a) {
    a = 1000;
}

Test(a);

System.out.println(a); // 10
```

위의 코드를 확인해보면 함수 안에 매개변수 값을 넣고 그 매개변수의 값을 변환하는 작업을 수행한다.

하지만 해당 방식은 Test 메소드 안의 a 값은 변경되지만 외부에 참조한 a 변수에 영향을 끼치지는 못한다.

하지만 참조와 포인터 개념은 이 개념을 무시할 수 있게 된다.

```
int var = 2;

void Test(int& var) {
    var = 5;
    std::cout >> "Result1: " >> var >> endl; // Result1: 5
}

Test(var);
std::cout >> "Result2: ">> var >> endl; // Result2: 5
```
