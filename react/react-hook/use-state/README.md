# useState란

리액트의 rerendering에 기여하는 변수 값으로 useState에는 state와 setState 두개로 나뉘어져 있는데 setState를 통해 state의 값을 변경하게 되면 리액트에서는 reRendering 과정을 거치게 된다.

그렇다면 useState는 어떤 기능을 가지고 어떻게 동작하고 있을까?

## useState의 구조

useState는 2개의 배열 값을 가져오는 형식으로 이루어져 value를 저장하는 0번째 값, 그리고 그 0번째 값을 수정하는 setState라는 `React.Dispatch<SetStateAction>` 타입의 동기 함수가 1번째 값으로 들어가며, useState의 인자에는 initialValue가 들어가는데 value 자체의 객체가 들어가거나 `() => value` 형식으로 함수가 들어가곤 한다.

우선 코드를 하나하나 해석해볼 필요가 있는데 밑의 코드는 useState의 mount 시점을 표현하는 함수다. 실제로 선언된 함수명이 달라 헷갈릴 수 있으나. `HooksDispatcherOnMount`라는 객체를 참고한다면 useState 선언에는 mountState가 할당 되어있음을 확인할 수 있으니 안심해도 좋다.

```
// react/packages/react-reconciler/src/ReactFiberHooks.js 내용물

function mountState<S>(
  initialState: (() => S) | S,
): [S, Dispatch<BasicStateAction<S>>] {
  const hook = mountStateImpl(initialState);
  const queue = hook.queue;
  const dispatch: Dispatch<BasicStateAction<S>> = (dispatchSetState.bind(
    null,
    currentlyRenderingFiber,
    queue,
  ): any);
  queue.dispatch = dispatch;
  return [hook.memoizedState, dispatch];
}
```

우선 mountState의 인자를 살펴보면 union type으로 지정되어 있는데, () => S | S로 지정되어 있다. 바로 value 객체 값을 넣어도 되지만 arrow function을 사용해 값을 할당해도 된다라는 것이다.

일반적으로 S 즉 value에 대한 값을 넣지만 왜 함수를 통해 반환받는 과정을 거치게 했을까?

### initialState

initialState는 hook이라는 새로운 상수를 만들고 mountStateImpl로 값을 선언한다.

```
function mountStateImpl<S>(initialState: (() => S) | S): Hook {
  const hook = mountWorkInProgressHook();
  if (typeof initialState === 'function') {
    const initialStateInitializer = initialState;
    initialState = initialStateInitializer();
    if (shouldDoubleInvokeUserFnsInHooksDEV) {
      setIsStrictModeForDevtools(true);
      initialStateInitializer();
      setIsStrictModeForDevtools(false);
    }
  }
  hook.memoizedState = hook.baseState = initialState;
  const queue: UpdateQueue<S, BasicStateAction<S>> = {
    pending: null,
    lanes: NoLanes,
    dispatch: null,
    lastRenderedReducer: basicStateReducer,
    lastRenderedState: (initialState: any),
  };
  hook.queue = queue;
  return hook;
}
```

아까보다 조금 더 긴 코드가 나오게 되었는데, 여기서도 하나하나 살펴보자.

#### mountWorkInProgressHook

#### () => S 에 대한 처리

이제 다음으로 넘어오게 된다면

```
if (typeof initialState === 'function') {
    const initialStateInitializer = initialState;
    initialState = initialStateInitializer();
    if (shouldDoubleInvokeUserFnsInHooksDEV) {
      setIsStrictModeForDevtools(true);
      initialStateInitializer();
      setIsStrictModeForDevtools(false);
    }
  }
```

이 코드에 주목해야할 필요가 있다. useState의 initialState는 단순히 value를 받을 수도 있으나 함수를 받는 경우도 있다.

그렇다면 왜 함수를 받고 함수를 어떻게 처리하는지에 대해 집중해서 살펴보자면

1. 함수일 경우 initialStateInitializer라는 initialState의 복사본을 만든다.

   특이하게 parameter를 새로운 상수로 할당하고 다시 그 parameter를 새로 할당한 상수로 다지 지정하는 방식을 활용하는 구조를 사용한다.

   밑에 처럼 사용한 이유는 잠시 함수를 복사하고 기존의 initialState에는 함수 실행의 결과를 저장하기 위함

   ```
   const initialStateInitializer = initialState;
   initialState = initialStateInitializer();
   ```

2. shouldDoubleInvokeUserFnsInHooksDEV의 상태에 따라 어떻게 처리할 지 분기 처리를 해준다.

   기본 값은 false이나 상황에 따라 값을 재할당해준다.

   ```
   const shouldDoubleRenderDEV =
     __DEV__
     && debugRenderPhaseSideEffectsForStrictMode
     && (workInProgress.mode & StrictLegacyMode) !== NoMode;
   shouldDoubleInvokeUserFnsInHooksDEV = shouldDoubleRenderDEV;
   ```

3. setIsStrictModeForDevtools로 [strict dev trigger를 컨트롤](https://github.com/facebook/react/blob/cb6dc7a6a03ea10a38b84e9e5737739e0d468435/packages/react-reconciler/src/ReactFiberDevToolsHook.js#L202) 하면서 initialStateInitializer()를 실행시킨다.

#### workInProgressHook에 정보 저장

```
 hook.memoizedState = hook.baseState = initialState;
  const queue: UpdateQueue<S, BasicStateAction<S>> = {
    pending: null,
    lanes: NoLanes,
    dispatch: null,
    lastRenderedReducer: basicStateReducer,
    lastRenderedState: (initialState: any),
  };
  hook.queue = queue;
  return hook;
```

그 다음에 initialState에서 나온 결과 값을 hook.memoizedState랑 hook.baseState에 할당하고 새로운 queue를 만들어 값을 넣어준다.

이 과정을 거침으로써 useState라는 하나의 hook 정보에는 baseState와 memoizedState에 초기값(initialState)가 들어가게 된다.

### useState 값 반환

위에서 initialize하면서 초기값을 할당하는 작업까지 굉장히 복잡한 일련의 과정을 겪었다. 그 후 queue의 dispatch에 어떤 액션을 실행 시킬 지 정보를 넣어준다.

평소에는 사용하지 않는 function.bind를 이용해서 parameter를 바인딩 시킨 후 return 해줌으로써 호출 할 때 binding된 객체들을 이용해 함수를 호출할 수 있도록 만들어주었다

```
const queue = hook.queue;
  const dispatch: Dispatch<BasicStateAction<S>> = (dispatchSetState.bind(
    null,
    currentlyRenderingFiber, // any로 타입이 지정되어있어 열받지만... 일단 현재 실행중인 queue를 업데이트하거나 현재 진행중인 hook의 타입 등을 저장하는 용도로 보인다.
    queue,
  ): any);
  queue.dispatch = dispatch;
  return [hook.memoizedState, dispatch];
```

#### dispatchSetState

밑의 코드는 해석이 불필요해 보이는 or 과도한 코드들은 제거했다. (개발환경 용 코드 and 주석)

```
function dispatchSetState<S, A>(
  fiber: Fiber,
  queue: UpdateQueue<S, A>,
  action: A,
): void {
  const lane = requestUpdateLane(fiber);

  const update: Update<S, A> = {
    lane,
    revertLane: NoLane,
    action,
    hasEagerState: false,
    eagerState: null,
    next: (null: any),
  };

  if (isRenderPhaseUpdate(fiber)) {
    enqueueRenderPhaseUpdate(queue, update);
  } else {
    const alternate = fiber.alternate;
    if (
      fiber.lanes === NoLanes &&
      (alternate === null || alternate.lanes === NoLanes)
    ) {
      const lastRenderedReducer = queue.lastRenderedReducer;
      if (lastRenderedReducer !== null) {
        let prevDispatcher;
        try {
          const currentState: S = (queue.lastRenderedState: any);
          const eagerState = lastRenderedReducer(currentState, action);
          update.hasEagerState = true;
          update.eagerState = eagerState;
          if (is(eagerState, currentState)) {
            enqueueConcurrentHookUpdateAndEagerlyBailout(fiber, queue, update);
            return;
          }
        }
      }
    }

    const root = enqueueConcurrentHookUpdate(fiber, queue, update, lane);
    if (root !== null) {
      scheduleUpdateOnFiber(root, fiber, lane);
      entangleTransitionUpdate(root, queue, lane);
    }
  }

  markUpdateInDevTools(fiber, lane, action);
}
```

##### 변수 선언

```
const lane = requestUpdateLane(fiber);

const update: Update<S, A> = {
    lane,
    revertLane: NoLane,
    action,
    hasEagerState: false,
    eagerState: null,
    next: (null: any),
  };
```

총 2개의 변수를 선언해주는 작업을 진행한다.

1. lane: `/packages/react-reconciler/src/ReactFiberWorkLoop.js`에 존재하는 함수로 (TODO: 추후 조사후 링크 삽입) work loop돌려 재조정(reconciler) 위해 정보를 설정해주고 그 결과값을 반환
2. update: 다음 업데이트를 위한 객체 정보 설정 후 반환

##### 메인 로직

```
if (isRenderPhaseUpdate(fiber)) {
    enqueueRenderPhaseUpdate(queue, update);
  } else {
    const alternate = fiber.alternate;
    if (
      fiber.lanes === NoLanes &&
      (alternate === null || alternate.lanes === NoLanes)
    ) {
      const lastRenderedReducer = queue.lastRenderedReducer;
      if (lastRenderedReducer !== null) {
        let prevDispatcher;
        try {
          const currentState: S = (queue.lastRenderedState: any);
          const eagerState = lastRenderedReducer(currentState, action);
          update.hasEagerState = true;
          update.eagerState = eagerState;
          if (is(eagerState, currentState)) {
            enqueueConcurrentHookUpdateAndEagerlyBailout(fiber, queue, update);
            return;
          }
        }
      }
    }

    const root = enqueueConcurrentHookUpdate(fiber, queue, update, lane);
    if (root !== null) {
      scheduleUpdateOnFiber(root, fiber, lane);
      entangleTransitionUpdate(root, queue, lane);
    }
  }
```

isRenderPhaseUpdate의 상태에 따라 다음 큐를 동작시키기 위한 작업을 진행한다.

###### isRenderPhaseUpdate

setState, setReducer에서 주로 사용하는 함수로 상태 업데이트를 위한 queue의 동작을 실행시키고 queue 내용물을 다음으로 옮기는 작업을 수행한다.

```
function enqueueRenderPhaseUpdate<S, A>(
  queue: UpdateQueue<S, A>,
  update: Update<S, A>,
): void {
  /**
   이 과정은 렌더 페이즈(계산 작업)를 위한 작업이다. queue형태에서 linked list 형태의 updates로 지연 생성된 맵에 업데이트 내역을 저장한다.
   렌더 페이즈가 마무리 되면 work-in-progress hook에 저장된 가장 첫번째 업데이트를 적용한다.

   주의) 위 코멘트는 리액트 주석을 작성자가 번역해 오역이 있을 수 있다.
  */
  didScheduleRenderPhaseUpdateDuringThisPass = didScheduleRenderPhaseUpdate =
    true;
  const pending = queue.pending;
  if (pending === null) {
    // This is the first update. Create a circular list.
    update.next = update;
  } else {
    update.next = pending.next;
    pending.next = update;
  }
  queue.pending = update;
}
```

주석에 따르면 렌더 페이즈를 위해 queue 형태에서 linked-list 형태의 update로 지연 생성된 맵에 업데이트 내역을 저장 후 workInProgress hook 정보에 저장되어있는 첫번째 업데이트를 적용하기 위한 로직이다.

didScheduleRenderPhaseUpdateDuringThisPass, didScheduleRenderPhaseUpdate는 `스케쥴링의 업데이트를 위한 변수 (아닐 수 있음)`로 ReactFiberHooks에서만 사용하는 변수다.

- isRenderPhaseUpdate -> fiber 정보를 기반으로 현재 렌더링 중인지 정보를 가져오는 함수 (boolean으로 return)
