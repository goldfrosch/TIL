# 전방 선언

C++에서는 간혹 특정 변수를 앞에 class를 붙여서 선언해주는 경우가 존재한다. 왜 이런식으로 선언해서 사용하는 지 더 자세히 알아보도록 하자

## 전방 선언의 정의

전방 선언은 실제 식별자를 정의하기 전 식별자의 존재를 컴파일러에 미리 알리는 것 이다.

```
#include <iostream>

int main()
{
    std::cout << add(3, 4) << endl;
    return 0;
}

int add(int x, int y)
{
    return x + y;
}
```

함수를 예시로 들어본다면 함수의 선언 전 위의 함수에서 사용하게 되는 경우 컴파일 에러가 발생하곤 한다. (2015 기준으로)

선언되기 전의 함수를 위에서 선언하게 되면서 발생하는 에러로 즉 선언 전에는 사용할 수 없다라는 것을 알 수 있다. (해당 내용은 JS const 사용 시에도 해당된다.)

해당 이슈는 컴파일러가 파일의 소스코드를 순차적으로 읽게 되면서 발생하는 이슈다.

### 그래서 왜 쓰는가?

전방 선언을 하게 되는 경우 컴파일러에 미리 존재를 알리고 함수를 호출 할 때 정의된 함수를 바로 사용할 수 있다.

#### 언리얼의 관점에서...

APawn.h, APawn.cpp와 APawn.h에 소속된 CapsuleComponent.h가 있다고 가정해본다.

CapsuleComponent.h에서는 UCapsuleComponent 타입의 포인터가 필요하기에 APawn.h에서 include 선언을 해준 상황이다.

다만 APawn.h에서는 UCapsuleComponent만 필요한 상황이기에 CapsuleComponent의 구현 정보가 굳이 필요하지는 않다. 즉 진짜 일부만 필요한 상황이라는 뜻이다. 그리고 포인터 타입이기에 단순 주소만 가져오고 있다.

이렇게 되면 APawn.cpp에 최종적으로 CapsuleComponent.h가 포함되게 설정이 되는데 다른 파일에 들어갈 때랑 어떤 것이 다른가에 대해 알아보자

헤더의 경우 다른 파일에 포함되도록 디자인 되어있는데 APawn.h에 CapsuleComponent.h가 포함되어 있다고 가정하고, 멀리에 있는 SomeClass.h에 APawn.h의 포인터를 넣는다라고 가정한다.

이렇게 된다면 복잡하게 APawn 포인터와 SomeClass를 전방 선언하는 대신에 일반적으로 선언하고 SomeClass.h에 APawn.h이 포함되게 된다면 APawn.h는 SomeClass.h에 APawn.h의 내용물과 CapsuleComponent.h 둘다 들어가게 되는데 SomeClass는 CapsuleComponent.h만 필요했는데 APawn.h정보가 들어가게 되고 이것이 확장되면 확장될 수록 단순한 하나의 기능을 사용하기 위해서 deps가 엮여있는 헤더의 정보들을 전부 가져와서 사용해야 한다라는 치명적인 문제가 발생하게 된다.

즉 언리얼의 관점에서 전방 선언은 필요한 정보만을 가져오게 한다. APawn.h의 정보만 필요하다면 APawn의 전방 선언으로 APawn만 가져와 사용하고 그 내부의 CapsuleComponent.h를 가져오지 않게 해 의존성에서 필요한 정보만 가져와 불필요한 메모리 소비를 방지해 줄 수 있게 된다.
