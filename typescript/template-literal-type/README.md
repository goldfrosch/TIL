# 템플릿 리터럴 타입이란

템플릿 리터럴 타입은 문자형 리터럴 타입 기반인 마치 정규식 처럼 타입을 지정할 수 있는 타입을 말한다.

우선 템플릿 리터럴이 무엇이길래 타입을 별도로 지정할 수 있는지 알아야한다.

## 템플릿 리터럴이란?

내장된 표현식을 허용하는 문자열 리터럴로 백틱(``)을 이용해서 사용한다. placeholder을 이용해 표현식을 넣을 수 있는데, 기존의 리터럴과 같이 ${}을 이용해서 사용한다.

### 그렇다면 장점은?

1. string 멀티라인

- 기존의 string을 두줄이상 사용하기 위해서는 \n을 붙여서 사용해줘야만 했다. 하지만 템플릿 리터럴을 사용할 경우에는 2줄처리는 그냥 enter로만 처리가 가능하다.

```
  // not use Template Literal
  console.log("string text line 1\n"+
  "string text line 2");
  // "string text line 1
  // string text line 2"

  // use Template Literal
  console.log(`string text line 1
  string text line 2`);
  // "string text line 1
  // string text line 2"
```

2. 표현식 삽입

- 리터럴 안에서는 연산또한 자유롭게 가능하다. 밑과 같은 방식으로 연산 후 출력 또한 가능해진다라는 것이다.

```
  var a = 5;
  var b = 10;

  console.log(`Fifteen is ${a + b} and
  not ${2 * a + b}.`);

  // "Fifteen is 15 and
  // not 20."
  const classes = `header ${ isLargeScreen() ? '' :
     `icon-${item.isCollapsed ? 'expander' : 'collapser'}` }`;
```

3. 태그 템플릿

- 템플릿 리터릴의 더욱 발전된 형태다. 템플릿 리터럴을 함수로 파싱하는 형태다.
- styled-component에서도 사용하고 있는 방식 ㄴㅇㄱ (styled.div`~~~~`)

  ```
    var person = 'Mike';
    var age = 28;

    function myTag(strings, personExp, ageExp) {

      var str0 = strings[0]; // "that "
      var str1 = strings[1]; // " is a "

      // 사실 이 예제의 string에서 표현식이 두 개 삽입되었으므로
      // ${age} 뒤에는 ''인 string이 존재하여
      // 기술적으로 strings 배열의 크기는 3이 됩니다.
      // 하지만 빈 string이므로 무시하겠습니다.
      // var str2 = strings[2];

      var ageStr;
      if (ageExp > 99){
        ageStr = 'centenarian';
      } else {
        ageStr = 'youngster';
      }

      // 심지어 이 함수내에서도 template literal을 반환할 수 있습니다.
      return str0 + personExp + str1 + ageStr;

    }

    var output = myTag`that ${ person } is a ${ age }`;

    console.log(output);
    // that Mike is a youngster
  ```

  조금 더 심화된 방법으로 태그 템플릿을 이용할 수 있다.

  ```
    // 리터럴을 통해 string값의 배열값이 우선 들어가게된다.
    // t1Closure을 예시로하면 ${0},${1},${0},! 총 4개의 요소에 3개는 리터럴 타입이라 빈 string이 마지막은 값이 들어있어 !로 반환된다.
    // keys는 템플릿 리터럴 타입인 0,1,0이 각각들어가게된다.
    function template(strings, ...keys) {
      return (function(...values) {
        var dict = values[values.length - 1] || {};
        var result = [strings[0]];
        keys.forEach(function(key, i) {
          var value = Number.isInteger(key) ? values[key] : dict[key];
          result.push(value, strings[i + 1]);
        });
        return result.join('');
      });
    }

    var t1Closure = template`${0}${1}${0}!`;
    t1Closure('Y', 'A');  // "YAY!"
    var t2Closure = template`${0} ${'foo'}!`;
    t2Closure('Hello', {foo: 'World'});  // "Hello World!"
  ```

## 템플릿 리터럴 타입이란?

String literal을 기반으로 새로운 타입을 만들 수 있는 문법이다.
유니온 string 타입도 당연히 적용되고 심지어 UpperCase, LowerCase, Caplitalize, Uncapitalize 이 4개의 형태와 같이 사용해 자유롭게 타입을 지정할 수 있는 것이 핵심이다.

```
  type URL = `https://${string}`;
  type Lang = "ko" | "en" | "ch";
  type userType = `user_${Lang}`; // user_ko | user_en | user_ch
  type event = "click" | "scroll";
  type handlerName = `on${Capitalize<event>}` // onClick, onScroll
```
