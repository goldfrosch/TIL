# React Hook

리액트에서는 16.8 버전 이후로 함수형 컴포넌트를 본격적으로 지원하기 위해 hook이라는 개념을 사용해 함수형 컴포넌트의 활성화를 진행했다. 대표적인 것들이 useState, useEffect, useRef 등인데... 각각의 hook이 어떻게 동작하고 어떻게 리렌더링을 유발하는지, 변수를 어떻게 저장하는지 등에 대해서 정리해보자.

글이 길어 이 메인페이지를 기점으로 하나씩 추가하려고 한다.

## 위치

리액트의 훅이 선언된 부분에 비해 생각보다 실제 로직의 위치를 찾기가 매우 힘들어 남기는 코드로, 실제로 선언된 곳과 동작하는 부분이 다르니 참고해두는 것이 좋다.

실제 선언 위치: [[링크]](https://github.com/facebook/react/blob/5de8703646cdd3838cb1686f761b10c0692743aa/packages/react/src/ReactHooks.js)

밑은 실제로 동작하기 위해 할당된 hook의 로직들 위치다.

```
// 디렉토리: react/packages/react-reconciler/src/ReactFiberHooks.js
const HooksDispatcherOnMount: Dispatcher = {
  readContext,
  use,
  useCallback: mountCallback,
  useContext: readContext,
  useEffect: mountEffect,
  useImperativeHandle: mountImperativeHandle,
  useLayoutEffect: mountLayoutEffect,
  useInsertionEffect: mountInsertionEffect,
  useMemo: mountMemo,
  useReducer: mountReducer,
  useRef: mountRef,
  useState: mountState,
  useDebugValue: mountDebugValue,
  useDeferredValue: mountDeferredValue,
  useTransition: mountTransition,
  useSyncExternalStore: mountSyncExternalStore,
  useId: mountId,
};
```

## Hook 들의 설명

위의 코드 순서대로는 아니지만 중요하고 많이 쓰는 순서대로 각각의 역할과 동작 원리에 대해 정리해보기로 한다.

### useState
