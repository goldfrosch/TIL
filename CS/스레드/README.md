# 스레드

프로세스의 실행 가능한 가장 작은 단위

프로세스는 여러개의 스레드를 가질 수 있고, 코드, 데이터, 스택, 힙 메모리 영역 중 스레드는 코드, 데이터, 힙 영역을 스레드 끼리 공유하고 스택 영역만 스레드 개별로 생성된다.

## 멀티스레딩

멀티 스레딩은 프로세스 내 작업을 여러 개의 스레드 즉 멀티 스레드로 처리하는 기법으로 스레드끼리는 서로 자원을 공유하기 때문에 효울성이 높다.

하나의 처리를 새 프로세스를 만들어서 처리하는 것이 아닌 새 스레드를 만들어서 처리함으로써 더 적은 리소스를 소비하고 스레드가 중단 되어도 다른 스레드가 실행 상태가 되어있기 때문에 중단되지 않은 빠른 처리가 가능하게 된다.

대신 한 스레드에 문제가 생기면 다른 스레드에도 영향을 끼쳐 스레드로 이루어진 프로세스 자체에도 영향을 줄 수 있다는 단점이 존재한다.

## 싱글 스레드

프로세스에 메인 스레드 하나로만 동작하는 방식으로 주로 간단한 앱들을 이렇게 사용하곤 한다.
