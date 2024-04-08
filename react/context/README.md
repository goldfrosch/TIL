# Context

React에서 제공하는 Context는 무엇을 위한 용도이고 왜 우리는 ContextAPI가 아닌 다른 상태관리 라이브러리를 쓰는지에 대해 알아보자

## Context란?

React에서는 부모 인자에서 자식 인자로 특정 값을 넘겨줄 때 Props라는 것을 이용해서 전달한다.

```
const Parent = () => {
    const [state, setState] = useState(0);
    return <Child test={state} />
}
```

그렇다면 최상단 컴포넌트에서 자식의 자식의 자식의... 자식에게 값을 전달해줘야 하려면 어떻게 해야할까?

```
const GrandParent = () => {
    const [state, setState] = useState(0);
    return <Parent test={state} />
}
const Parent = ({ test }) => {
    return <Child test={state} />
}
const Child = ({ test }) => {
    return <GrandChild test={state} />
}
const GrandChild = ({ test }) => {
    return <What test={state} />
}
```

특정 값을 내려줘야하는 곳까지 props를 내려줘야만 그 결과값을 전달해줄 수 있다. 이러한 문제를 Props drilling이라고 부르는데 이런 문제를 해결하기 위해 context를 이용해 일일히 props를 넘겨주지 않고 컴포넌트 트리 전체에 데이터를 제공해주는 역할을 수행하는 것이 Context의 기본 개념이라고 할 수 있다.

### 장점

역시 장점은 불필요한 props drilling이 사라진다라는 것이다. 불필요한 정보를 전달하는 일련의 과정들이 없어짐으로써 상태(state) 관리를 불필요한 props로 전달하는 문제 없이 사용이 용이해질 수 있게된다.

### 단점

사실 Context는 props driling을 막아준다라는 장점이 있음에도 불구하고 사람들이 많이 사용은 하지 않는다.

이유가 몇가지가 있는데 하나씩 알아보도록 하자

1. 확장성이 좁아지게 된다.
   Context를 사용하게 되는 경우는 별도의 Context를 등록하고 자식 컴포넌트들에서 Wrapper로 선언된 Context를 꺼내서 사용해야한다. 이렇게 된다면 Context를 사용하는 컴포넌트 밖의 컴포넌트들은 무조건적으로 Context를 사용할 수 밖에 없기 때문에 확장성이 떨어지고 1단계의 props임에도 Context를 선언해서 사용해야한다는 문제가 있다.

   ```
    const CounterContext = createContext();

    function CounterProvider({ children }) {
        const counterState = useState(1);
        return (
            <CounterContext.Provider value={counterState}>
            {children}
            </CounterContext.Provider>
        );
    }

    const TestChild = () => {
        // CounterContext에 의존성이 생기게 됨
        const value = useContext(CounterContext);
        return <div>{value[0]}</div>
    }
   ```

2. 불필요한 리렌더링이 발생한다.
   ContextAPI는 어찌되었든 선언한 밑의 컴포넌트들에게 특정 context를 공유하는 기능이다. 그러다보니 모든 정보를 담고 있는 contextAPI에서 값이 바뀌게 되면, Context.Provider로 선언된 하위 컴포넌트들은 전부 다 리렌더링 된다라는 치명적인 문제가 있다.
