# 리액트의 렌더링이란?

## 렌더링이란 state, props의 상태에 따라 UI를 어떻게 구성할 지 컴포넌트에 요청하는 작업을 의미한다.

- 렌더링 과정

  1. 컴포넌트 트리의 루트부터 시작해서 변경점이 있는지 탐색함
  2. 변경점이 있어 플래그가 지정된 컴포넌트 들에 대해 컴포넌트를 호출하고 렌더링 출력을 저장함.
     - FunctionComponent(props) - 함수형
     - classComponentInstance.render() - 클래스형
  3. 이후 JSX로 작성된 리액트 컴포넌트 파일이 React.createElement()라는 구조를 설명하는 순수 JS객체로 변환된다.

  ```
      return <SomeComponent a={42} b="testing">Text here</SomeComponent>

      // 이것을 호출해서 변환된다.
      return React.createElement(SomeComponent, {a: 42, b: "testing"}, "Text Here")

      // 호출결과 element를 나타내는 객체로 변환된다.
      {type: SomeComponent, props: {a: 42, b: "testing"}, children: ["Text Here"]}
  ```

  4. 이 후 모든 컴포넌트에서 렌더링 결과물들을 수집하고 리액트에서 새로운 오브젝트 트리 (가상 돔 -> Virtual Dom)와 비교하고, 실제 DOM을 의도한 출력을 하기 위한 모든 변경사항들을 수집한다.
     이렇게 비교하는 과정을 Reconciliation 이라고 한다.

  5. 이후 계산된 모든 변경사항을 하나의 동기 시퀀스로 DOM에 저장한다.

## 리액트의 동작 방식

- 리액트는 동작하는 과정을 크게 2개로 분류했다.

  - Render phase -> 컴포넌트를 렌더링하고 변경된 부분을 계산하는 작업
  - Commit phase -> 돔에 변경사항을 적용하는 작업

    리액트에서 DOM에 변경사항을 적용하면서 요청된 DOM 노드 및 컴포넌트 인스턴스를 가리키도록 모든 ref를 업데이트한다.

    그 이후 componentDidmount와 componentDidUpdate, 함수형에서는 useLayoutEffect를 동기적으로 실행한다.

    그 후 짧은 timeout을 설정하고 useEffect훅을 실행시킨다. 이러한 과정을 수동 효과라고 부른다.
