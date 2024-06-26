# Programming Language vs Scripting Language

개발하면서 문득 프로그래밍 언어인지 스크립팅 언어인지를 구분하는 단위가 무엇인지 정확하게 설명할 수 없는 자신을 발견해 어떤 차이인지 확실하게 알아보자

## 용도 관점

스크립팅 언어와 프로그래밍 언어는 둘다 특정 기능을 수행하기 위한 소프트웨어를 구축하는데 필요한 소통을 특정 규칙에 따라 작성하는 언어다.

다만 스크립팅은 일반적으로 특정 프로세스를 자동화 하거나 이미 존재하는 기존의 애플리케이션을 구성하는데 도움(JS 같은 경우도 이미 존재하는 V8엔진을 사용해 특정 기능을 구현함)을 주기위해 작성한다면, 프로그래밍 언어는 애플리케이션 자체를 개발하는데 사용된다.

## 동작 관점

스크립팅 언어의 특징 중 하나는 언어가 기존 프로그램에 의존하기에 컴파일 없이 인터프리터를 사용해 한번에 하나의 코드를 런타임에서 실행하는 작업을 해야하지만, 프로그래밍 언어의 경우는 빌드되어서 런타임에서 실행되기 전 컴파일 과정을 거쳐야만 한다.

### 컴파일러

소프트웨어를 구축하기 위해 작성한 고급 언어를 저급 언어(기계어)로 변경하는 작업을 해주는 기능을 말한다.

모든 컴파일러의 수행 작업은 거의 동일한데 대표적으로 문법 및 구문 분석, 의미 분석, 코드 생성 및 최적화 등을 진행한다.

또한 컴파일러의 장점 중 하나는 소프트웨어를 빌드하는 과정에서 모든 코드를 한번만 읽고 에러를 전체적으로 분석한 뒤 에러가 없으면 기계어로 이루어진 프로그램이 빌드가 되기에 빌드 이후의 실행 속도가 빠르다.

다만 그 빌드하는 시간이 오래 걸린다라는 단점이 존재한다.

대표적인 언어로는 C, C#, C++ 등이 있다.

### 인터프리터

직역하면 해석기라는 뜻으로 프로그래밍 언어(실제르는 스크립팅 언어겠지만)의 원시 코드 명령어들을 한 번에 한줄 씩 읽어 실행하는 프로그램이라고 할 수 있다.

컴파일러가 존재함에도 인터프리터를 사용하는 이유는 컴파일 과정과 다르게 기계어로 번역해 빌드하는 과정이 필요가 없어지기에 크기가 큰 프로그램의 경우 기계어로 빌드하는 시간을 줄이고 인터프리터를 통해 직역하면 된다는 장점이 존재한다.

다만 매번마다 별도의 컴파일 없이 소스 코드를 인터프리터를 통해 기계어로 번역하고 실행하는 과정이 존재하기 때문에 실행 속도가 느려질 수 있다.

대표적인 언어로는 Python이 있다.

### Java는...?

자바의 경우 .java 파일을 javac(Java Compiler)가 바이트 코드 기반의 .class 확장자 파일로 변환해주는 컴파일러 역할을 수행한다.

그리고 Java 고유의 인터프리터가 Java Compiler에 의해 변환된 바이트 코드를 특정 환경의 기기에서 실행될 수 있도록 리딩하는 작업을 수행한다.

### JavaScript는...?

JavaScript는 평소에 인터프리터 언어라고 우리는 인지하고 있지만 실상은 조금 다르다.

인터프리터 언어의 특징인 소스코드를 한줄 한줄 읽으면서 기계어로 번역하고 실행하는 과정이 존재한다.

다만 여러 문구 중 하나가 문법적인 에러가 발생할 상황을 집어넣고 실행하면 어떻게 될까?

```
console.log("Hello World");
console.log("Hello World");
console.log("Hello World");
console.log("Hello World");
console.log("Hello World");
console.log("Hello World // 마지막은 불편해도 이해하길...
```

인터프리터의 규칙에 따르면 Hello World가 5번은 찍히고 syntax error 가 발생해야 한다. 다만 실제로 이 코드를 실행시킨다면 console.log가 노출되지 않음을 확인할 수 있다.

이것의 이유는 JS 엔진은 실행 전에 코드를 컴파일 하고 그 컴파일 단계에서 구문 오류가 감지되면 엔진이 **JIT** 컴파일러를 실행하는 동안 즉시 코드를 발생하게 되기 때문이라고 할 수 있다.

### JIT Compiler?

JIT (Just In Time) 컴파일러는 바이트 코드 컴파일러라고도 불리며, 소스 코드(고급 언어)를 기계어는 아니지만 가상 머신에 의해 기계어로 손쉽게 변환할 수 있는 코드인 바이트 코드로 변환하고 가상 머신에 의해 JIT 컴파일러가 런타임에서 바이트 코드를 읽어 빠르게 기계어(저급 언어)를 생성시키는 역할을 수행하는 런타임 환경의 컴포넌트로 Java(JVM)와 JS(V8, node.js) 그리고 .NET에서 주로 사용되는 컴파일러다.

일반적인 인터프리터 언어인 C언어 기반의 python인 CPython의 경우 바이트 코드나 소스코드를 별도의 최적화 없이 번역하기 때문에 성능이 낮지만 정적으로 컴파일 하는 언어의 경우 실행 전 컴파일 과정이 필수적이라 컴파일 시간의 소요가 많이 되곤 한다.

JIT 컴파일러는 동적 컴파일 환경으로 런타임 환경에서 컴파일을 할 수 있게 설계 했는데, 빌드 과정에서 바이트 코드로 변환하는 작업을 먼저 수행하고 이후 런타임에서 컴파일 과정을 거쳐 빠르게 기계어로 번역시켜 실행 속도를 단축시키는 작업을 수행해 일반적인 인터프티러 언어에 비해 높은 성능을 낸다.

#### 왜 이런 작업을 하는걸까?

유저를 위해서라면 사실 빌드 시간이 느리더라도 빌드 후 런타임에서 빠르게 수행되는 것이 옳다. 하지만 컴파일러만 수행한다면 단점 또한 존재하는데, 컴파일러는 프로그램이 작성되고 컴파일 하는 기계상에서 매우 효율적으로 실행하는 작업을 수행하게 된다.

하드웨어, 임베디드 시스템의 프로그래밍 언어가 C로 되어 있는 이유 또한 특정 기기에 종속되고, 플랫폼에 종속될 수 있는 문제가 발생한다.

반면에 인터프리터 언어는 소스코드를 한줄한줄 즉시 번역해 기계어로 돌리기 때문에 엔진이 존재한다면 바로 최적화를 계산하면서 기계어로 번역이 되기 때문에 플랫폼에 종속당하지 않는 소프트웨어에게는 해당 문제를 해결해줄 수 있다.

다만 인터프리터는 한줄 한줄을 소스코드 -> 기계어 로 번역하는 작업을 수행하기 때문에 매우 느리고, 이것을 방지하기 위해 바이트 코드로 변환해 한줄 한줄 읽게 시키기 위한 인터프리터 작업을 수행하고, 이후 JIT 컴파일러가 런타임에서 컴파일 과정을 거쳐 빠르게 기계어로 전부 변환해 속도를 증가시키는 역할을 수행하는 것이다.

또한 JIT 컴파일러는 Java 기준으로 바이트 코드를 읽을 때 이미 읽은 메소드는 메모리에 저장해두고, 다시 그 메소드가 호출되는 경우 메모리에 있는 번역 값을 그대로 넣어주기 때문에 매우 효율적으로 움직일 수 있다.
