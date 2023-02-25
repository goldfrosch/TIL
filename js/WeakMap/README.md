# WeakMap이란?

jotai에서 새로나온 store개념을 파악하기 위해 깃허브 원본코드를 분석하려고 했는데, createStore에서 구성하는 데이터를 weakMap을 사용해서 구현하는 것을 보았다.
weakmap이라는게 도대체 뭐길래 사용하고 있는지 조사해보았다.

JS 엔진은 도달가능한, 그리고 추후에 사용될 가능성이 있는 값을 메모리에서 유지해준다. 만약 특정 데이터가 null이 될 경우는 더이상 사용하지 않는다라고 판단해 메모리에서 제거해준다.

```
  // 처음에는 메모리에서 잘 저장되고 있다.
  let john = { name: "John" };

  // 하지만 객체가 null이 되면서 더이상 사용하지 않는다라고 가정, 메모리에서 제거된다.
  john = null;
```

하지만 객체가 null이 되더라도 특정 객체, 배열에서 사용하고 있을 경우에는 메모리에 남아있게 된다.

```
  let john = { name: "John" };

  let array = [ john ];

  john = null; // 참조를 null로 덮어씀

  // john이 null이 되더라도 배열의 요소중 하나기 때문에 GC의 대상이 되지 않음.
  alert(JSON.stringify(array[0]));
```

즉 Map을 사용하는 경우도 해당 객체의 요소가 null이 될 경우에는 사용하고 있다라고 판단해 GC에서 수집해가지 않는다.
그러나 Weakmap은 구조도, 수집하는 방식도 조금 다르다.

## Map vs Weakmap?

Map과 Weakmap의 결정적 차이는 Weakmap은 key가 오직 참조 타입이여야만 한다는 것이다 (원시타입은 불가능 함)
이 차이점으로 인해서 Weakmap의 key값이 수정되어 null이 될 경우에는 자동으로 GC에서 수거해주고, Weakmap에서도 해당 값이 삭제된다.

```
  let john = { name: "John" };

  let weakMap = new WeakMap();
  weakMap.set(john, "...");

  john = null;
```

이 key가 참조타입이 되면서 생기는 이점이 몇개 존재한다.

1. 부가적인 데이터
   부가적인 데이터는 특정 객체의 값이 살아있을 경우에만 존재해야만 하는 데이터의 경우일 때 유용하다.
   밑의 예시코드처럼 key가 되는 객체가 null이 되거나 사라질 경우 자동으로 수집이 되어 메모리 관리가 용이해질 수 있다.

   ```
    let john = { name: "John" };
    weakMap.set(john, "비밀문서");
   ```

   기존의 Map형식으로 사용할 경우에는 john이라는 객체가 null이 되더라도 Map에서는 계속 참조하고 있기 때문에 수집하지 않지만 Weakmap이 될 경우에는 key가 null이면 자동으로 수집해준다.

-- 중간저장 --
