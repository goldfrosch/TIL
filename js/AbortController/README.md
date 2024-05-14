# AbortController

AbortController 인터페이스는 하나 이상의 웹 요청을 취소할 수 있는 API라고 할 수 있다.

## 사용법

```
const controller = new AbortController();
const { signal } = controller;

downloadBtn.addEventListener('click', fetchVideo);

abortBtn.addEventListener('click', function() {
  controller.abort();
  console.log('Download aborted');
});

function fetchVideo() {
  ...
  fetch(url, {signal}).then(function(response) {
    ...
  }).catch(function(e) {
    reports.textContent = 'Download error: ' + e.message;
  })
}
```

### 동작 플로우

AbortController는 우선 생성자를 통해 하나의 AbortController 객체 선언을 하고 signal을 사용해 abortcontroller 사용을 fetch에 등록한다.

이후 abortBtn이라는 DOM에서 클릭 이벤트가 발생 시 signal이 등록되어있는 요청을 controller 객체에서 abort 호출로 해당 호출을 무효화 시키는 행동을 한다.

### signal 이란?

정확하게는 AbortSignal이라고 부른다. AbortSignal 인터페이스는 DOM 요청과 통신하고 AbortControll 객체를 통해 취소할 수 있게 해주는 신호 객체를 의미하게 된다.

그렇기 때문에 위에 있는 예제의 경우 fetch에 signal을 등록해뒀기에 signal이 직접 중단 시키는 것은 불가능하나, 필요한 경우에 controller에서 직접 abort요청을 하게되면 그것을 aborted라는 boolean 속성의 property를 통해 요청이 취소되었는지 아닌지를 검증하고 취소되었다는 뜻의 True를 반환하게 되면 그대로 fetch 함수는 중단되는 로직이라고 할 수 있다.

#### 중단에 대한 관리

보통 AbortController는 API에서 주로 사용하지만 비동기에서도 사용할 수 있다. 이런 로직을 잘 활용하고 캔슬되었을 때의 결과를 반환하는 방식이다.

대표적으로 fetch를 사용하는 경우를 예시로 들어보자면 (axios도 fetch 기반이기에... /libs/adapter/fetch.js에 fetch를 사용하는 코드 존재) fetch는 AbortController를 선언하고 signal에 박은 이후 상황에 따라서 AbortController를 직접 호출하는 방식을 사용하는 것도 가능하다.

그러다 보니 상황에 따라선 단순히 중복 호출 뿐만 아니라 timeout을 클라이언트에서 기준을 잡아 abortController에서 취소를 해줄 수도 있다.

예를 들어 서버에서는 API 최대 소요 시간이 10초라고 가정할 때 클라이언트에서는 이것을 길다고 판단하게 되면 5초가 지나도 API 응답이 이루어지지 않으면 timeout이라고 판단해 abortcontroller에서 이것을 abort처리를 하는 것도 가능하다.

## 근데 왜 API만?

여태까지의 예시를 보면 abortController는 API만을 예시로 들지, 비동기 처리에 대해 예시를 명확하게 들지 않는다.

왜 API가 아닌 비동기 처리(Promise)에서는 취소를 제대로 하지 못할까?

### Promise 상태

Promise의 상태가 이유가 되기도 한다. Promise 객체의 응답은 성공(fullfilled), 진행중(pending), 실패(reject) 3개로 이루어져있다. 하지만 응답 중 취소에 대해서는 Promise의 응답 3개 중 어디에 넣어야 하는지가 애매해진다.

다른 언어 C#에서 취소는 일반 예외 처리와 동일하게 처리된다. 그렇기 때문에 중간 취소의 경우는 별도의 Exception 처리를 걸어줘야 하는 불편함이 생기게 된다.

JS에서도 만약 취소를 에러로 처리하게 된다면, 이 에러에 대해 매번마다 if문으로 어떤 에러인지 검증하고 취소인지를 해결해줘야하는 문제가 발생하게 된다. Axios같은 경우는 하나의 instance에서 설정해주면 되지만 Promise 자체에 대한 처리는 조금 다른 문제라 일일히 설정해야할 수 있다.

이 문제에 대해 then(), catch(), catch cancel() 이라는 새로운 옵션을 만들까 고민은 했지만 이렇게 된다면 취소를 위한 메소드를 추가해줘야 할 필요도 있기 때문에 웹 생태계를 관리하는 TC39 위원회에서는 이 문제에 대해 아직도 논쟁을 하고 있다.

참고자료
https://tech.kakao.com/2023/01/11/promise-cancelation-in-javascript/
