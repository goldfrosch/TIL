# Service Worker

사용자는 느리거나 불안정한 네트워크 환경 or 오프라인 상태에서도 앱이 동작하기를 기대하곤 한다.

물론 네트워크 환경이 안 좋거나 오프라인 환경에서라면 최신 정보를 가져오거나 하는 것 들이 불가능하지만 최대한의 작업을 하길 원한다. (미디어 트랙, 여행 일정, 티켓 정보 확인 등등...), 최신 정보를 못가져와도 그것을 알려주는 것을 기대하기도 한다.

웹에서는 이 부분을 처리하기에는 한계가 있다. HTML, CSS, JS 같은 리소스는 네트워크가 잘 돌아가야 다운로드가 되니까... 하지만 이 문제를 개선하게 된다면 사용자의 사용성 증가 즉 이것은 수익성으로도 이어질 수 있어 구글에서는 Service Worker를 제공하게 된다.

## 서비스 워커의 역할?

사용자가 오프라인일 때를 포함해 앱이 서비스 워커 범위에 포함되는 리소스 요청 시 서비스 워커가 Proxy역할을 해 특정 네트워크 요청 (CDN을 통한 리소스 or 서버 API 요청 등)을 가로채 서비스 워커의 Storage(Cache Storage API)에서 캐시를 꺼내 리소스를 제공할 지, 로컬 알고리즘에서 리소스를 생성할 지를 정하는 것이 가능해, 오프라인이여도 동작을 가능하게 해준다.

### 주의사항?

서비스 워커는 일부 브라우저에서는 지원되지 않고, 표시되더라도 최초 로드 시 혹은 활성화 대기 중일 때는 사용이 불가능하다. 그렇기 때문에 필수사항이라고 보기는 어려우며, 핵심 기능을 담당하기에도 무리가 있다.

## 서비스 워커 동작 방식

서비스 워커는 수명 주기(lifeCycle)은 비슷하지만 점진적 개선 방식을 사용한다. 새로운 서비스 워커를 설치하는 웹페이지를 처음 방문 시 서비스 워커 다운로드와는 별개로 웹사이트의 기본 기능은 제공되고 워커의 설치가 완료되고 활성화되면 개선된 안정성과 속도를 제공하도록 그 페이지를 서비스 워커가 제어하게 된다.

## 등록 방법

보통 PWA에서 service-worker를 사용하지만 꼭 PWA가 아니여도 등록은 가능하다. 서비스 워커는 Web API이므로 window.navigator에 존재한다.

```
if ('serviceWorker' in navigator) {
   navigator.serviceWorker.register("/serviceworker.js");
}
```

위의 코드처럼 navigator에 serviceWorker가 지원되는 사이트에서 서비스 워커 등록 작업을 진행하게 된다.

## JS 기반 캐싱 API

서비스 워커 기술의 필수 중 하나는 HTTP 캐시와는 완전히 별개의 캐싱 매커니즘인 Cache 인터페이스를 사용하는 것이다. 이 Cache 인터페이스의 경우 서비스 워커 범위와 기본 스레드(메인 스레드)범위 내에서 엑세스가 가능하다.

### Cache Interface?

Service Worker의 생명주기의 일부로 캐시 된 request와 response를 나타내며, 도메인은 여러개의 이름이 지정된 Cache 객체를 가질 수 있고 그 객체는 Service Worker가 제어하기에 워커에서 어떻게 컨트롤(CRUD) 할 지 구현해야한다.

### 캐싱 활용

- 정적 에셋 첫번째 로드 이후 캐싱 처리함으로써 이후 모든 요청을 캐시에서 정적 에셋을 제공하는 방식
- 페이지 마크업을 캐시에 저장하고 오프라인 환경에서만 캐시된 마크업을 제공
- 캐시의 에셋의 응답을 먼저 제공 후 백그라운드에서 네트워크 요청으로 새로운 데이터로 갱신

위 3가지 방법 말고도 더 있지만 공통점은 오프라인 환경을 가능하게 하고 지연 시간이 긴 재검증을 회피함으로써 더 나은 성능을 제공해줄 수 있다.

## 비동기 동작 원리

네트워크 데이터 전송은 본질적으로 비동기 방식을 사용한다. (event-loop) 에셋을 요청하고 서버가 응답 후 데이터를 내려주는 플로우에는 시간이 걸리고 그 시간이 일정하지 않고 불확실하기 때문에 서비스 워커에서는 특정 이벤트 들에 대한 콜백을 사용가능하게 함으로써 비동기 동작을 원활하게 한다.

그리고 서비스 워커에서 특정 이벤트에 대한 callback 등록은 JS에서도 익숙한 addEventListener를 사용해 등록이 가능하며 이 이벤트들은 전부 Cache 인터페이스와 상호 작용이 가능하다.

### 비동기 콜백 이벤트 종류

- 서비스 워커 설치 시
- 서비스 워커 활성화 중일 때
- 서비스 워커의 네트워크 요청 감지

## 사전 캐싱 및 런타임 캐싱

서비스 워커와 Cache 인스턴스 간의 상호작용에는 사전 캐싱과 런타임 캐싱이라는 서로 다른 캐싱 개념이 포함되 서비스 워커의 이점을 늘려준다.

### 사전 캐싱

일반적으로 서비스 워커 설치 중 에셋을 미리 캐시하는 프로세스다. 사전 캐싱을 사용하면 오프라인 엑세스에 필요한 주요 정적 에셋과 자료를 다운로드해 Cache 인스턴스에 저장할 수 있고 이 유형의 캐싱은 또한 사전에 캐싱된 에셋이 필요한 후속 페이지의 페이지 속도를 개선해준다.

### 런타임 캐싱

런타임 시 네트워크에서 요청될 때 자산에 캐싱 전략이 적용되는 경우인데, 이런 종류의 캐싱은 사용자가 이미 방문한 페이지 및 자산에 대한 오프라인 엑세스를 보장한다.

## 기본 스레드와의 격리

서비스 워커는 JS 성능의 상태를 더욱 개선시키기 위한 과제도 해결하고 있다. 그러기 때문에 서비스 워커에서 수행하는 모든 작업은 자체적인 스레드에서 발생하기 때문에 기본 스레드(메인 스레드)의 다른 태스크들에 의해 중단되거나 영향을 끼치지 않는다.
