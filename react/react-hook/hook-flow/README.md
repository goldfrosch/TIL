# React Hook Flow

리액트에서 사용하는 hook의 전체적인 구조에 대해 알아보자

## workInProgressHook

ReactFiberHook.js에 선언된 변수 값으로 hook의 working 여부를 관리하는 hook을 재귀 객체로 관리한다.

```
// workInProgressHook의 타입
type Hook = {
    memoizedState: any
    baseState: any

    baseQueue: Update<any, any> | null
    queue: any

    next: Hook | null
  };
```

### mount

workInProgressHook의 mount 과정인 mountWorkInProgressHook()에 대해 더 자세히 알아보자면...

```
type Update<S, A> = {
  lane: Lane,
  revertLane: Lane,
  action: A,
  hasEagerState: boolean,
  eagerState: S | null,
  next: Update<S, A>,
};

function mountWorkInProgressHook(): Hook {
  const hook: Hook = {
    memoizedState: null, // type: any

    baseState: null, // type any
    baseQueue: null, // type Update<any, any> | null
    queue: null, // type any

    next: null, // type Hook | null
  };

  if (workInProgressHook === null) {
    // This is the first hook in the list
    currentlyRenderingFiber.memoizedState = workInProgressHook = hook;
  } else {
    // Append to the end of the list
    workInProgressHook = workInProgressHook.next = hook;
  }
  return workInProgressHook;
}
```

또 함수로 선언되는데 몇가지 특징을 가지고 있다.

1. memoizedState와 baseState의 존재
2. baseQueue와 queue의 존재
3. next의 존재

hook안에 state와 queue 그리고 다음 hook은 어떤 것인지에 대해 저장해두고 전역으로 선언되어있는 변수 (workInProgressHook)가 null인 경우는 새로운 값 할당을 아니라면 next 정보에 현재 새로 만들 hook 정보를 저장 후 사용하던 `workInProgressHook`이라는 변수 값을 반환한다.

즉 mountWorkInProgressHook의 역할은 workInProgressHook라는 전역 변수에 현재 상태들과 다음 진행해야할 정보들을 계속해서 저장해 queue 처럼 차례대로 처리하는 로직을 만들었다.
