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
    // $FlowFixMe[incompatible-use]: Flow doesn't like mixed types
    initialState = initialStateInitializer();
    if (shouldDoubleInvokeUserFnsInHooksDEV) {
      setIsStrictModeForDevtools(true);
      // $FlowFixMe[incompatible-use]: Flow doesn't like mixed types
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

   // TODO: 의미가 있는 방법일까...? 오히려 안티패턴으로 보이나 우선은 넘어가기로 한다.

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

```
const queue = hook.queue;
  const dispatch: Dispatch<BasicStateAction<S>> = (dispatchSetState.bind(
    null,
    currentlyRenderingFiber,
    queue,
  ): any);
  queue.dispatch = dispatch;
  return [hook.memoizedState, dispatch];
```

#### dispatchSetState
