# getStaticPath와 fallback

next.js 에서 ISR [링크](https://github.com/goldfrosch/TIL/tree/main/nextjs/side-rendering#isr)를 사용하게 되면 기본적으로는 getStaticProp 함수를 사용하나 간혹 동적인 path로 구성된 경우 getStaticPath라는 함수를 추가로 이용해 페이지를 캐싱처리한다.

getStaticPath의 정확한 동작 방식과 옵션 fallback에 대해 더 알아본다.

## getStaticPath

ISR은 기본적으로 getStaticProps라는 함수를 이용해 미리 사전에 렌더할 정보를 저장하고 어느 시점에서 재갱신을 할 지 정하는 함수라고 할 수 있다.

그리고 getStaticPath는 동적 path를 가진 페이지에서 getStaticProps를 보조해 동적 페이지들에 대해 사전에 어떻게 렌더할지, 어떻게 저장할지를 정해주는 역할을 한다. (이 말은 즉 getStaticPath는 getStaticProps에 추가로 사용되는 함수라고도 할 수 있다)

### 특징

// TODO: 런타임에서는 동작하지 않는 함수라면 fallback 옵션은 어디서 동작하게 되는지

- getStaticProps는 production 빌드 시에만 동작하고 런타임에서는 동작하지 않는 함수다. getStaticProps는 런타임 환경에서 주기적으로 돌지만 getStaticPath는 역할 상 빌드 시에 모든 것들을 해결해준다.
- getServerSideProps 즉 SSR과는 같이 사용이 불가능하다.

### 옵션

getStaticPath는 빌드 중에 생성되는 페이지를 fallback 옵션을 통해서 제어가 가능하다.

getStaticPath에서 처음에 많은 동적 path의 페이지를 저장한다고 가정한다면, 그만큼의 페이지를 만들어야 하기 때문에 빌드 시간이 굉장히 길어질 것이다.

getStaticPath에서 반환(return) 하는 객체의 값은 크게 2가지가 존재한다.

```
return { path: [], fallback: false, // true | false | 'blocking' }
```

#### path

path 옵션은 어떤 동적 path 기반의 페이지들을 저장할 지 설정해주는 값이다.

기본적으로 `{ params: { [key in //(동적 path)]: any} }` 이라는 타입의 배열을 가지고 있으며 이 path안에 들어간 갯수만큼 페이지가 생성된다고 생각하면 된다.

예시를 들어보자면 `pages/post/[id]/[commentId].js` 라는 파일에서 ISR getStaticPath를 사용한다고 하면 이런식으로 return 할 수 있다.

```
return {
    path: [
        {params: {id: '1', commentId: '3'}},
        {params: {id: '2', commentId: '32'}}
    ],
    ...
}
```

#### fallback

fallback의 경우 처음에 빌드되지 않은 페이지에 대한 경우의 대응을 어떻게 할지를 설정해준다.

타입은 boolean or 'blocking' 으로 각 타입에 따른 대응 방식을 알아보자

##### fallback: false

false 옵션은 빌드된 동적 path가 아닌 다른 동적 path로 접근하는 경우.

(ex. /post/[id] 에서 id: 1,2,3을 빌드했는데 /post/4 로 접근하는 경우를 말함)

기본적으로 설정한 404 페이지로 이동 시켜주는 것이 큰 특징이다

그렇기 때문에 만약 새로운 동적 path를 적용시키기 위해서는 다시 빌드를 해줘야한다는 특징이 존재한다.

해당 옵션은 적은 페이지를 만드는 경우나 특정 path 외에는 전부 없는 페이지(빌드하지 않으면)로 처리해야하는 경우에 사용된다.

##### fallback: true

false랑 반대로 404로 보내주지는 않고 첫 요청이 발생하게 되면 새롭게 해당 페이지를 빌드해주게 되는데,

첫 요청일 경우 빌드 동안 빈 페이지를 보여주는 것이 아닌 fallback 페이지를 보여준다.

###### fallback 페이지란?

next/router를 사용하게 되면 router 옵션 중 isFallback이라는 boolean 옵션이 있는데, 이것을 사용해 로딩 UI를 만드는 경우가 있다. 즉 suspense 처럼 로딩 중인 경우(fallback 경우)에 대한 UI 처리를 해주는 것이라고 볼 수 있다.

```
function Test() {
    const router = useRouter();

    if (router.isFallback) {
        return <div>이건 로딩이야</div>
    }

    return <div>난 로딩이 아니라 알락꼬리꼬마도요야</div>
}
```

대신 웹 크롤러의 경우 (구글 엔진 같은?) fallback 페이지를 보여주는 것이 아닌 fallback: 'blocking'과 동일한 동작을 하게된다.

또한 next/link 라이브러리 혹은 next/router 를 이용해 페이지를 이동하게 되는 경우도 fallback 페이지가 보여지는 것이 아닌 fallback: 'blocking' 과 동일한 동작을 한다.

이 후 백그라운드에서는 HTML과 JSON 파일을 만들고 getStaticProps에서 동작하게 만들고 브라우저는 동적 path를 구성하고, 그것이 자동으로 페이지를 렌더하기 위한 props 정보를 가져오도록 해준다. 이후 fallback 페이지에서 정상적인 페이지를 노출시켜주게 된다.

```
// JSON 정보
{"pageProps":{ ... getStaticProps로 들어온 정보값 },"__N_SSG":true}
```

이 모든 일련의 과정이 끝나면 앞으로 새로 만들어진 동적 path는 이미 빌드와 캐싱처리가 완료되어 미리 캐싱된 페이지를 보여줄 수 있게 된다.

###### 사용처

getStaticPath는 빌드 시점에서 path에 들어가있는 페이지들을 빌드해주는 역할을 수행한다. 그렇기 때문에 동적 데이터 기반의 페이지는 데이터가 많아지면 많아질수록 빌드 시간은 길어지고 빌드 파일도 많아지게 된다.

이렇게 되면 간혹 유저가 잘 보지 않는 페이지도 빌드해야하는 경우가 발생할 수 있고 이런 문제를 방지하기 위해 fallback: true를 사용하곤 한다.

##### fallback: 'blocking'

위 fallback: true에서 특정 경우 fallback: 'blocking' 처럼 동작한다고 하는 그 경우에 대해서 알아보자.

fallback이 'blocking' 옵션이 된다면 SSR 처럼 동일하게 생성될 때 까지 기다린 이후 캐싱 처리 된다. 즉 fallback 페이지가 보여지는 것이 아닌 SSR 처럼 대기 상태가 되는 것을 의미한다.

그렇기 때문에 깜빡이는 현상 없이 HTML이 만들어지기 전까지 이전 페이지에서 대기하고 있기 때문에 빠르게 렌더가 가능한 페이지라면 자연스럽게 넘어가지만, 렌더가 느린 페이지의 경우는 오랫동안 반응이 없어 유저 입장에서는 렉으로 반응할 수 있어, 동적인 데이터 path이면서도 금방 넘어갈 수 있는 정도의 데이터 양 or api 호출되는 페이지에 사용하면 될 것 같다.
