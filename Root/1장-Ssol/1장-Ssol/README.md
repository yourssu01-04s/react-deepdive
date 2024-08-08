# **1.1 자바스크립트의 동등 비교**

- props의 동등 비교
  - 이에 따라 리액트 컴포넌트의 렌더링이 일어남
  - 객체의 **얕은 비교**를 기반으로 이뤄짐
  - 얕은 비교가 어떻게 작동하는지 모르면 렌더링 최적화하기 어려움

## **1.1.1 자바스크립트의 데이터 타입**

### **원시 타입**

> boolean, null, undefined, number, string, symbol, bigint (7개)

- 객체가 아닌 모든 타입
- 객체가 아니기에 이 타입들은 메서드를 갖지 않음
- `undefined`
  - 선언 후 값을 할당하지 않은 변수
  - 값이 주어지지 않은 인수에 자동으로 할당되는 값
- `null`
  - 아직 값이 없거나 비어있는 값 표현
  - 특별한 점! [null로 바꾸려는 시도가 있었지만 실패,,,](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/typeof#null)
  ```jsx
  typeof null === "object"; // true
  ```
- `undefined` vs `null`
  - `undefined` : **선언했지만 할당되지 않은 값**
  - `null` : **명시적으로 비어 있음을 나타내는 값**
- **`falsy`** 한 값
  - false, 0, NaN, 공백 없는 빈 문자열, null, undefined
- **`truthy`**
  - falsy 이외의 값들
  - 객체`{}`와 배열`[]`은 내부에 값이 존재하는지 여부와 상관없이 `truthy`로 취급됨
- `String`

  - **template literal**
    - 백틱 사용해 표현한 문자열
    - 줄바꿈이 가능
    - 문자열 내부에 표현식을 쓸 수 있음
  - 원시타입, 변경 불가능 (한 번 생성 후 문자열 변경 불가)
  - Q. 원시타입은 값이 재할당이 안된다는 뜻인가?
    A. 재할당 됨! 책의 예시가 const라 헷갈리는 거다,,,
    변경 불가능하다는 것은 변수에 대한 것이 아니라, **"값"**에 대한 것!
    값을 변경할 수 없다는 것은 "기존에 원시값을 할당한 변수에 새로운 원시값을 재할당할 경우, 기존 메모리 공간의 주소의 원시 값 자체를 새로운 원시 값으로 변경하는 것이 아니라, **새로운 메모리 공간을 확보하여 재할당한 원시 값을 저장하고 변수가 새롭게 재할당한 원시 값을 가리키게 하는 것**이다. 따라서 const가 아닌 var로 문자열을 생성했을 때 새로운 재할당은 된다는 소리~

    ```jsx
    var foo = "bar";

    console.log(foo[0]); // 'b'

    // 앞 글자를 다른 글자로 변경해보면
    foo[0] = "a";

    // 반영이 안 됨
    console.log(foo); // 'bar'

    // 그러나 변수 전체를 새로운 문자열로 재할당은 가능
    foo = "baz";
    console.log(foo); // 'baz'
    ```

- `Symbol`

  - 중복되지 않는 어떤 고유한 값
  - Q. 그럼 모든 `id`를 `symbol`로 만들면 되지 않을까? 근데 왜 잘 안쓰일까?
    **`Symbol`을 ID로 사용하지 않는 이유**

    1. `Symbol`은 직렬화할 수 없기 때문

       `Symbol` 값을 JSON 형식으로 변환하거나 데이터를 db에 저장하거나 네트워크를 통해 전송할 수 없다는 소리! [관련 이슈](https://stackoverflow.com/questions/32368189/is-it-a-good-practice-to-use-es6-symbol-as-unique-ids)

    2. 전역 관리가 불편함

       전역으로 id를 만들기 위해서는 `Symbol.for()` 로 따로 만들어서 관리해야함

    3. 중복으로 symbol을 할당해도 오류가 생기지 않음

       독립적인 값이 되기때문에 같은 string 으로 정의해도 같은 값이 아니기 때문

  - Q. 그럼 react 어디에 symbol이 쓰일까?

    1. **enum 사용할 때 좋음**

       ```jsx
       const POSITION_TOP = "TOP";
       const POSITION_RIGHT = "RIGHT";
       const POSITION_BOTTOM = "BOTTOM";
       const POSITION_LEFT = "LEFT";

       function getReversedPosition(position) {
         switch (position) {
           case POSITION_TOP:
             return POSITION_BOTTOM;
           case POSITION_LEFT:
             return POSITION_RIGHT;
           default:
             throw new Error(`Unknown position: ${position}`);
         }
       }
       ```

       이렇게 반대 포지션을 반환하는 함수가 있는데

       ```jsx
       const LOL_POSITION = {
         RIVEN: "TOP",
       };

       console.log(getReversedPosition(LOL_POSITION.RIVEN));
       ```

       또 다른 상수를 TOP이라고 정의해버리면 리벤은 탑인데 의도와 다르게 BOTTOM이 된다 🥲

       이럴 때 `symbol`을 사용

       ```jsx
       const POSITION_TOP = Symbol("TOP");
       const POSITION_RIGHT = Symbol("RIGHT");
       const POSITION_BOTTOM = Symbol("BOTTOM");
       const POSITION_LEFT = Symbol("LEFT");

       function getReversedPosition(position) {
         switch (position) {
           case POSITION_TOP:
             return POSITION_BOTTOM;
           case POSITION_LEFT:
             return POSITION_RIGHT;
           default:
             throw new Error(`Unknown position: ${position}`);
         }
       }

       const LOL_POSITION = {
         RIVEN: Symbol("TOP"), // 고유한 심볼을 생성하기 때문에 POSITION_TOP과 다름!
       };

       try {
         console.log(getReversedPosition(LOL_POSITION.RIVEN)); // Error: Unknown position: Symbol(TOP)
       } catch (e) {
         console.error(e.message); // 예상대로 오류 발생
       }
       ```

       **+ 비슷한 맥락으로 Redux의 액션 타입 지정**

       일일이 변수로 **`const INCREMENT= "INCREMENT"`**이렇게 쓰는 것 보다

       객체형태로 모아서 enum 상수를 만들어 주어 key만을 필요로하게 만들면 굿굿!

       ```jsx
       const ACTION_TYPES = {
         INCREMENT: Symbol("INCREMENT"),
         DECREMENT: Symbol("DECREMENT"),
       };

       const reducer = (state, action) => {
         switch (action.type) {
           case ACTION_TYPES.INCREMENT:
             return { count: state.count + 1 };
           case ACTION_TYPES.DECREMENT:
             return { count: state.count - 1 };
           default:
             return state;
         }
       };
       ```

### **객체 타입**

> object

- 7가지 원시 타입 이외의 모든 것
- `배열`, `함수`, `정규식`, `클래스` 등 포함
- 참조를 전달해 참조 타입(reference type)으로도 불림

## **1.1.2 값을 저장하는 방식의 차이**

### **원시 타입**

- 불변 형태의 값으로 저장됨
- 값을 전달, 따라서 밑에 코드가 당연함

  ```jsx
  let hello = "hello world";
  let hi = hello;

  console.log(hello === hi); // true
  ```

### **객체 타입**

- 프로퍼티를 삭제, 추가, 수정 가능해 변경 가능한 형태로 저장됨
- 값을 복사할 때도 값이 아닌 참조를 전달함

  ```jsx
  var hello = {
    greet: "hello, world",
  };

  var hi = {
    greet: "hello, world",
  };

  console.log(hello === hi); // false

  console.log(hello.greet === hi.greet); // true
  ```

  ⇒ 객체 간 비교 발생 시 내부 값이 같더라도 결과는 대부분 false일 수 있음을 인지해야 함

## **1.1.3 자바스크립트의 또 다른 공식, Object.is**

- **`==` (느슨한 동등)**: 타입 변환을 수행하여 값이 같으면 `true`를 반환한다.
  ```jsx
  console.log(5 == "5"); // true
  console.log(null == undefined); // true
  ```
- **`===` (엄격한 동등)**: 타입 변환을 수행하지 않고 값과 타입이 모두 동일해야 `true`를 반환한다.
  ```jsx
  console.log(5 === "5"); // false
  console.log(null === undefined); // false
  console.log(NaN === NaN); // false
  ```
- **`Object.is`**: 값이 정확히 같은지 비교한다. ===의 한계를 극복하기 위해 만들어짐.
  ```jsx
  console.log(Object.is(5, "5")); // false
  console.log(Object.is(NaN, NaN)); // true
  console.log(Object.is(+0, -0)); // false
  ```

## **1.1.4 리액트에서의 동등 비교**

리액트에서 사용하는 동등 비교는 **`==`**, **`===`**이 아닌 **`Object.is`**이다!

1. **NaN 비교**

   ```jsx
   console.log(null === undefined); // false
   console.log(Object.is(NaN, NaN)); // true
   ```

2. **+0와 -0 비교**

   ```jsx
   console.log(+0 === -0); // true
   console.log(Object.is(+0, -0)); // false
   ```

   **⇒ Object.is가 더 정확한 비교를 한다.**

- 그러나 `Object.is`가 모든 브라우저에서 지원되는 것은 아니기 때문에 리액트는 폴리필(polyfill, 브라우저나 자바스크립트 엔진에서 기본적으로 지원하지 않는 기능을 구현하기 위해 사용되는 코드)을 제공하여 이 기능을 대체한다.

### `objectIs` 함수

리액트에서 `Object.is`를 사용하거나, 그렇지 못한 경우 폴리필을 사용하는 `objectIs` 함수는 다음과 같이 정의된다.

```jsx
function is(x: any, y: any) {
  // objectIs 없으면 is로 대신 쓰겠당
  return (x === y && x !== 0) || 1 / x === 1 / y || (x !== x && y !== y);
}

const objectIs: (x: any, y: any) => boolean =
  typeof Object.is === "function" ? Object.is : is;

export default objectIs;
```

### `shallowEqual` 함수

리액트에서 `shallowEqual` 함수는 `objectIs`를 기반으로 얕은 비교를 한다.

- Q. 얕은 비교까지만 구현한 이유는?
  리액트에서 사용하는 JSX props는 객체이고, 여기에 있는 Props만 일차적으로 비교하면 되기 때문!
  리액트는 props에서 꺼내온 값을 기준으로 렌더링을 수행하기 때문에 일반적인 케이스에서는 얕은 비교로 충분할 것이다.
  ⇒ 따라서 props에 또 다른 객체를 넘겨준다면 렌더링이 예상치 못하게 작동한다!
  ex) `React.memo`: props가 깊어지는 경우(한 객체 안에 또다른 객체가 있으면) `React.memo` 는 컴포넌트에 실제로 변경된 값이 없음에도 불구하고 메모이제이션된 컴포넌트를 반환하지 못한다.
- Q. 싹 다 비교하기 위해 재귀문을 넣었으면 어땠을까?
  객체 안에 객체가 몇 개까지 있을지 알 수 없으므로 성능에 악영향을 미칠 것

```jsx
import is from "./objectIs"; // Object.is 폴리필 또는 기본 제공되는 Object.is
import hasOwnProperty from "./hasOwnProperty"; // Object.prototype.hasOwnProperty

function shallowEqual(objA: mixed, objB: mixed): boolean {
  // 1. 초기 비교
  if (is(objA, objB)) {
    return true;
  }

  // 2. 타입 및 null 검사! 객체가 아니거나 하나라도 null이면 바로 false 반환
  if (
    typeof objA !== "object" ||
    objA === null ||
    typeof objB !== "object" ||
    objB === null
  ) {
    return false;
  }

  // 3. 키 배열 추출 및 길이 비교, 길이가 다르면 false겠죠?!
  const keysA = Object.keys(objA);
  const keysB = Object.keys(objB);

  if (keysA.length !== keysB.length) {
    return false;
  }

  // 4. 각 키에 대한 비교, hasOwnProperty 메서드를 이용해 진짜 키가 있나 확인, 참조를 비교
  for (let i = 0; i < keysA.length; i++) {
    const currentKey = keysA[i];
    if (
      !hasOwnProperty.call(objB, currentKey) ||
      !is(objA[currentKey], objB[currentKey])
    ) {
      return false;
    }
  }

  // 5. 모든 검사를 통과한 경우 드디어 true 반환
  return true;
}

export default shallowEqual;
```

- Q. `shallowEqual` 이 어디에서 쓰일까?
  주로 라이프사이클 메서드에서 많이 쓰인다! 상태가 동일한 경우 리렌더링을 방지하는 목적으로 쓰임

  1. **PureComponent**에서 사용

     `React.PureComponent`는 `shallowEqual`을 내부적으로 사용한다.

     props와 state의 얕은 비교를 해서 불필요한 리렌더링을 방지한다.

     동일한 값으로 업데이트되는 경우는 리렌더링 XXX

  2. **shouldComponentUpdate** 메서드에서 사용

     `shallowEqual`을 직접 `shouldComponentUpdate` 메서드에서 사용한다.

  3. **React.memo**와 함께 사용

     **React.memo**는 \*\*\*\*props가 변경되었을 때만 컴포넌트를 리렌더링한다.

     `shallowEqual`을 **React.memo**의 두 번째 인자로 전달하여 커스텀 비교 함수를 만들어 쓸 수 있다.

     ```jsx
     // 특정 프로퍼티만 비교하는 커스텀 비교 함수 예시
     function **customCompare**(prevProps, nextProps) {
       return prevProps.value === nextProps.value;
     }

     const MyComponent = React.memo(({ value }) => {
       console.log('Rendering MyComponent');
       return <div>{value}</div>;
     }, **customCompare**);
     ```

     `shallowEqual`을 사용해 동일한 값으로 업데이트될 때 리렌더링이 발생하지 않게 할 수 있다.

     ```jsx
     const MyComponent = React.memo(
       ({ value }) => {
         console.log("Rendering MyComponent");
         return <div>{value}</div>;
       },
       (prevProps, nextProps) => shallowEqual(prevProps, nextProps)
     );

     function App() {
       const [value, setValue] = useState("Hello World");

       return (
         <div>
           <MyComponent value={value} />
           <button onClick={() => setValue("Hello World")}>Update Value</button>
         </div>
       );
     }

     export default App;
     ```

  4. **Redux** 연결 컴포넌트에서 사용

     Redux의 `connect` 함수에서 `mapStateToProps` 결과를 비교하는 데 사용된다고 한다~~~

# **1.2 함수**

## **1.2.2 함수를 정의하는 4가지 방법 + 제너레이터**

### **함수 선언문**

```jsx
function add(a, b) {
  return a + b;
}
```

- 가장 일반적으로 사용하는 방식
- 표현식이 아닌 일반 문(statement)으로 분류됨
- 어떠한 값도 표현되지 않았음
- 그러나 아래처럼 선언한다면..!

### **함수 표현식**

```jsx
const sum = function (a, b) {
  return a + b;
};
```

- 자바스크립트에서 함수는 일급 객체
  - **일급 객체** : 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체
  - 함수는 다른 함수의 매개변수 / 반환값 / 할당도 가능하므로 일급 객체의 조건을 모두 갖춤
- 함수 표현식 vs 선언 식
  💡 가장 큰 차이는 **호이스팅 여부**

  - **함수의 호이스팅** : 함수 선언문이 마치 코드 맨 앞단에 작성된 것처럼 작동하는 JS의 특징
  - 아래와 같은 코드가 정상 작동 함

  ```jsx
  console.log(typeof hello); // function

  hello(); // hello

  function hello() {
    console.log("hello");
  }
  ```

- 아래 코드처럼 **변수에 할당한 함수 표현식**에서는 **변수 호이스팅**이 발생하는데, 함수의 호이스팅과는 다르게 var가 undefined로 초기화된다.

  ```jsx
  console.log(typeof hello); // undefined

  hello(); // Uncaught TypeError: hello is not a function
  // 코드 맨 앞이 아닌 할당문이 실행되는 런타임에서 함수가 할당되기 때문에 에러남

  var hello = function () {
    console.log("hello");
  };
  ```

⇒ 뭐가 좋고 나쁜 것은 없다! 프로젝트 상황에 맞는 작성법을 일관되게 사용하면 됨

### **Function 생성자**

```jsx
const add = new Function("a", "b", "return a + b");
```

- 생성자 방식으로 함수 생성 시 함수의 클로저 또한 생성 X
- **권장되지 않음**
  - 매개 변수, 함수의 몸통을 모두 문자열로 작성해야 해서 별로

### **화살표 함수**

```jsx
const add = (a, b) => a + b;
```

- 이거 많이 씀!
- 화살표 함수에서는 **`constructor`**를 사용할 수 없음
- arguments가 존재하지 않음
- 화살표 함수와 일반 함수의 가장 큰 차이점 : **`this 바인딩`**
  ⇒ 별도의 작업을 추가로 하지 않고 this 접근 가능

  ```jsx
  class Component extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        counter: 1,
      };
    }

    functionCountUp() {
      console.log(this); // undefined
      this.setState((prev) => ({ counter: prev.counter + 1 }));
      // 👆 setState 못 찾음
    }

    ArrowFunctionCountUp = () => {
      console.log(this); // class Component
      this.setState((prev) => ({ counter: prev.counter + 1 }));
      // 👆 잘 작동함
    };
  }
  ```

### **제너레이터**

- 제너레이터 함수는 `function*` 키워드를 사용하여 정의한다.

```jsx
// 제너레이터 함수 정의 예시
function* generatorFunc1() {
  // 함수 내용
}

const generatorFunc2 = function* () {
  // 함수 내용
};
```

- 화살표 함수 사용 불가!
- 별표 위치는 `function` 키워드와 함수 이름 사이에 놓여야 하며, 함수 이름이 없는 경우에는 `function` 키워드 바로 뒤에 놓인다.

## **1.2.3 다양한 함수 살펴보기**

### **즉시 실행 함수 (IIFE: Immediately Invoked Function Expression)**

```jsx
(function (a, b) {
  return a + b;
})(
  10,
  24
)((a, b) => {
  return a + b;
})(10, 24);
```

- 함수를 정의하고 그 순간 즉시 실행되는 함수
- 단 한 번만 호출되고, 다시 호출할 수 없음! 그래서 일반적으로 이름을 붙이지 않음
- 장점
  - 글로벌 스코프를 오염시키지 않는 독립적인 함수 스코프를 가질 수 있다.
  - 코드를 읽을 때 다시 호출되지 않는다는 점을 각인시킬 수 있어 도움이 된다.
  - 재사용되지 않는 함수이고, 단 한 번만 실행되고 끝난다면 즉시 실행 함수의 사용을 검토해보자!

### **고차 함수 (HOF: Higher Order Function)**

- 자바스크립트의 함수가 일급 객체라는 특징 활용
  → 함수를 인수로 받거나 결과로 새로운 함수를 반환시킬 수 있음

```jsx
// 함수를 매개변수로 받는 대표적인 고차 함수
const doubledArray = [1, 2, 3].map((item) => item * 2);
```

- 이를 활용해 함수형 컴포넌트를 인수로 받아 새로운 함수형 컴포넌트를 반환하는 고차함수도 생성 가능

```jsx
// 함수를 반환하는 고차 함수
const add = function (a) {
  return function (b) {
    return a + b;
  };
};
```

- 고차 함수형 컴포넌트 생성 시 컴포넌트 내부에서 공통으로 관리되는 로직을 분리해 관리 가능해 효율적으로 리팩토링 가능

## **1.2.4 함수를 만들 때 주의해야 할 사항**

- 다들 생각하시는 적당한 줄 수와 뎁스 기준이 궁금해요!!!
  `Ssol` : **`줄 수`** : 50줄 이하, **`뎁스`** : 2 이하 근데 한 번도 그렇게 만든 적 없음

### **함수의 부수 효과를 최대한 억제하라**

- 함수의 부수효과(side-effect)
  - 함수 내의 작동으로 인해 함수가 아닌 함수 외부에 영향을 끼치는 것
- 순수 함수
  - 부수 효과가 없는 함수
  - 언제 어디서나 어떠한 상황에서든 동일한 인수에 동일한 결과를 반환
  - 언제 실행되든 항상 결과가 동일해 예측 가능하며 안정적
- 비순수 함수
  - 부수 효과가 존재하는 함수
    **⇒ 리액트의 부수 효과를 처리하는 훅인 useEffect의 작동을 최소화하는 것이 그 일환**

### **가능한 한 함수를 작게 만들어라**

<aside>
✨ 함수는 하나의 일을, 그 하나만 잘하면 된다. - 더글러스 매킬로이-

</aside>

### **누구나 이해할 수 있는 이름을 붙여라**

<aside>
✨ 함수에 이름을 잘 붙이자. - Ssol -

</aside>

- 리액트의 `useEffect`, `useCallback` 등의 훅의 콜백 함수에 [네이밍](https://dev.to/deckstar/react-pro-tip-1-name-your-useeffect-54ck)을 붙여주는 것도 팁이당
  ```jsx
  useEffect(function doSomethingAfterMounting() {
    // <--- name me!
    // Your effect code
  }, []);
  ```

# **1.3 클래스**

리액트 16.8 버전 이전까지는 모든 컴포넌트를 클래스로 작성했다. 생각보다 얼마 안돼서 알아두면 오래된 코드 읽기에 도움이 된다…

## **1.3.1 클래스란 무엇인가?**

- 특정한 객체를 만들기 위한 일종의 **템플릿**
- 특정한 형태의 객체를 반복적으로 만들기 위해 사용됨
- 클래스 활용 시 객체를 만드는 데 필요한 데이터나 이를 조작하는 코드를 추상화해 객체 생성을 더욱 편리하게 할 수 있음

⇒ 리액트에서 함수형 컴포넌트가 더 많이 쓰이기도 하고… 그래서 내용 정리는 패스하겠습니다

- 클래스 요소 내용 정리

  ### **constructor**

  - 생성자, 객체 생성 시 사용하는 특수한 메서드
  - 하나만 존재할 수 있으며, 생략 가능, 여러 개 사용 시 에러 발생

  ```jsx
  class Car {
    constructor(name) {
      this.name = name;
    }
  }
  ```

  ### **프로퍼티**

  - 인스턴스를 생성할 때 내부에 정의할 수 있는 속성값
  - 접근 제한자
    - #을 붙여서 `private` 선언하는 방법이 ES2019에 추가됨
    - 타입스크립트를 활용하면 `private`, `protected`, `public` 사용 가능
      - TS에서만 가능! JS에서는 모든 프로퍼티가 `public`
    - 과거에는 private으로 만들고 싶을 때 `_`를 붙이는 코딩 컨벤션이 있긴 했다~

  ### **getter와 setter**

  - getter는 앞에 get을 붙이면 되고, 클래스 필드의 값을 가져올 때 사용
  - setter는 앞에 set을 붙이면 되고, 클래스 필드에 값을 할당할 때 사용

  ```jsx
  class Car {
    constructor(name) {
      this.name = name;
    }

    get firstCharacter() {
      return this.name[0];
    }

    set firstCharacter(char) {
      this.name = [char, ...this.name.slice(1)].join("");
    }
  }
  ```

  ### **인스턴스 메서드**

  - 클래스 내부에 선언한 메서드
  - 실제로 JS의 prototype에 선언되어서 프로토타입 메서드로 불리기도 한다.

  ```jsx
  class Car {
    constructor(name) {
      this.name = name;
    }

    hello() {
      console.log("안뇽");
    }
  }

  const myCar = new Car("자동차");
  myCar.hello(); // 안뇽
  ```

  ### **프로토타입 체이닝**

  직접 객체에서 선언하지 않았음에도 프로토타입에 있는 메서드를 찾아서 실행을 도와줌!
  모든 객체는 프로토타입을 가지고 있는데, 특정 속성을 찾을 때 자기 자신부터 시작해 이 프로토타입을 타고 최상위 객체인 Object까지 훑음

  ### **정적 메서드**

  - 클래스의 인스턴스가 아닌 이름으로 호출할 수 있는 메서드
  - 정적 메서드 내부의 this는 인스턴스가 아닌 클래스 자신을 가리킨다.
    - 따라서, 다른 메서드에서 일반적으로 사용하는 this를 사용할 수 없다.
  - 인스턴스를 생성하지 않아도 사용할 수 있고, 여러 곳에서 재사용이 가능하다는 장점이 있다.
    ⇒ 애플리케이션 전역에서 사용하는 유틸 함수를 정적 메서드로 많이 활용

  ```jsx
  class Car {
    static hello() {
      console.log("안뇽");
    }
  }

  Car.hello(); // 안뇽
  ```

  **상속**

  - extends는 기존 클래스를 상속받아서 자식 클래스에서 이 상속받은 클래스를 기반으로 확장하는 개념

```
- constructor, 상속자
- 프로퍼티 : 클래스로 인스턴스를 생성할 때 내부에 정의할 수 있는 속성값
- getter, setter
- 인스턴스 메서드
- 정적 메서드 ⇒ 애플리케이션 전역에서 사용하는 유틸 함수를 정적 메서드로 많이 활용
- 상속
```

- 정적 메서드 예시 - 자바스크립트의 `Math` 객체
  ```jsx
  console.log(Math.PI); // 3.141592653589793
  console.log(Math.abs(-42)); // 42
  console.log(Math.max(10, 20, 30)); // 30
  console.log(Math.min(10, 20, 30)); // 10
  console.log(Math.random()); // 0과 1 사이의 난수
  ```

## **1.3.2 클래스와 함수의 관계**

- 클래스가 작동하는 방식은 자바스크립트의 프로토타입을 활용하는 것

# **1.4 클로저**

|                   | 각 컴포넌트 이해에 중요한 것 |
| ----------------- | ---------------------------- |
| 클래스형 컴포넌트 | 클래스, 프로토타입, this     |
| 함수형 컴포넌트   | 클로저                       |

⇒ 함수형 컴포넌트의 구조와 작동 방식, 훅의 원리, 의존성 배열 등 함수형 컴포넌트의 대부분의 기술이 모두 클로저에 의존하고 있음! 그래서 클로저를 잘 알아야 한다~

## **1.4.1 클로저의 정의**

<aside>
✨ MDN 왈 
클로저는 함수와 함수가 **선언된 어휘적 환경(Lexical Scope)**의 조합

</aside>

**선언된 어휘적 환경이란??**

```jsx
function add() {
  const a = 10;
  function innerAdd() {
    const b = 20;
    console.log(a + b);
  }
  innerAdd(); // 30
}

add();
```

- innerAdd가 add 안에 선언되어 있어서 add 안에 선언된 a 변수를 사용할 수 있다.
- 즉, `선언된 어휘적 환경`이라는 것은, **변수가 코드 내부에서 어디서 선언됐는지**를 말하는 것이다.
- 호출되는 방식에 따라 동적으로 결정되는 this와는 다르게 코드가 작성된 순간에 정적으로 결정된다.
  ⇒ 클로저는 이러한 어휘적 환경을 조합해 코딩하는 기법!

## **1.4.2 변수의 유효 범위, 스코프**

- `스코프` : 변수의 유효범위

### **전역 스코프**

- 전역 레벨에 선언하는 것
- 이 스코프에 변수 선언 시 어디서든 호출 가능
  | | 전역 객체 |
  | ------------- | --------- |
  | 브라우저 환경 | window |
  | Node.js 환경 | global |

### **함수 스코프**

- JS는 기본적으로 함수 레벨 스코프를 따름
- {} 블록이 스코프 범위를 결정하지 않음!

```jsx
if (true) {
  var global = "global scope";
}

console.log(global); // 'global scope'
console.log(global === window.global); // true
```

- var global은 분명 {} 내부에서 선언돼 있는데, {} 밖에서도 접근이 가능함

  - 물론 `var` 키워드로 변수를 선언했을 때만 그렇다
  - `let`이나 `const`로 선언된 변수는 블록 레벨 스코프를 따름
  - let const var 차이

    ```jsx
    if (true) {
      let blockScoped = "block scope";
      const anotherBlockScoped = "another block scope";
      var otherBlockScoped = "other block scope";
    }

    console.log(blockScoped); // Uncaught ReferenceError: blockScoped is not defined
    console.log(anotherBlockScoped); // Uncaught ReferenceError: anotherBlockScoped is not defined
    console.log(otherBlockScoped); // other block scope
    ```

```jsx
function hello() {
  var local = "local variable";
}

console.log(local); // Uncaught ReferenceError: local is not defined
```

- 함수문 블록 안에서는 안 됨

## **1.4.3 클로저의 활용**

- 선언된 함수 레벨 스코프를 활용해 어떤 작업을 할 수 있다는 개념이 바로 클로저!

```jsx
function outerFunction() {
  var x = "hello";
  function innerFunction() {
    console.log(x);
  }

  return innerFunction;
}

const innerFunction = outerFunction();
innerFunction(); // 'hello'
```

- 이게 왜 되냐면
  - 스코프는 정적으로 결정되므로 반환된 함수(inner)에는 x가 존재하지 않지만, 이 함수가 선언된 어휘적 환경인 outer에 x가 존재했기 때문에 접근할 수 있다.
  - 즉, `내부함수`가 x 변수가 존재하던 환경을 기억하고 있다.

그래서 이걸 어디에 써먹냐면!

### **클로저의 활용**

전역 스코프와 비교해보자!

- 전역 스코프는 어디서든 원하는 값을 꺼내올 수 있다는 장점이 있지만, 누구든 접근해서 수정할 수 있다는 뜻도 된다.
- 만약 리액트의 state가 전역에 저장되어 있다면?
  - 어디서나 접근할 수 있게 되므로 놉
  - 따라서 리액트의 state는 리액트가 별도로 관리하는 클로저 내부에서만 접근할 수 있음.
  - 아래 예시처럼 클로저를 활용하면 전역 변수의 문제점을 보완할 수 있다.

```jsx
function Counter() {
  var counter = 0;

  return {
    increase: function () {
      return ++counter;
    },
    decrease: function () {
      return --counter;
    },
    counter: function () {
      console.log("리턴 전에 로깅 가능");
      return counter;
    },
  };
}
```

위 예시처럼 클로저를 활용했을 때의 이점

1. 변수를 직접 노출하지 않아 직접 수정하는 것을 막는다.
2. 로그를 남기는 등 부차적인 작업도 가능하다.
3. 변수의 변경할 때 +1 혹은 -1만 가능하도록 제한해서 무분별하게 변경되는 것을 막는다.

### **리액트에서의 클로저**

**`useState`**: 클로저의 원리를 사용하는 대표적인 것

```jsx
function Component() {
  const [state, setState] = useState();

  function handleClick() {
    // useState 호출은 위에서 끝났지만 setState는 계속 내부의 최신값(prev)을 알고 있음
    // 클로저 사용해서 그럼
    setState((prev) => prev + 1);
  }
}
```

- **setState 내부에서 useState의 최신 값을 계속해서 확인할 수 있는 이유**
  클로저가 useState 내부에서 활용됐기 때문!
  `외부함수(useState)`가 반환한 `내부함수(setState)`는 부 함수(useState)의 호출이 끝났음에도 자신이 선언되었던 `외부함수`가 선언되던 환경(여기에 state가 저장되어 있겠지)를 기억하고 있음

## **1.4.4 주의할 점**

### **var의 악행 😣**

```jsx
for (var i = 0; i < 5; i++) {
  setTimeout(function () {
    console.log(i);
  }, i * 1000);
}
```

- 위 예시는 setTimeout의 익명 함수가 클로저로 i를 1초마다 잘 출력할 것처럼 생겼지만!
- 하지만, 실제로는 매 초마다 5만 출력된다.
- 이렇게 작동하는 이유는
  - i가 전역 변수로 작동하기 때문이다.
  - JS는 기본적으로 함수 레벨 스코프를 따르기 때문에 var 변수는 for문과 상관 없이 전역 스코프에 var i가 등록된다.
  - 게다가 setTimeout은 태스크 큐에 들어가므로 for문을 다 순회한 이후에 실행된다.. setTimeout이 실행될 때 var i는 이미 for문을 다 돌아서 5가 되어있는 것

이런 일을 막으려면

- **1. var을 쓰지 않는다. 대신 let을 쓴다.**
  - let은 기본적으로 블록 레벨 스코프를 가져 let i가 for문을 순회하며 각각의 스코프를 갖게 된다.
  - setTimout이 실행되는 시점에도 유효해 각 콜백이 의도한 i 값을 바라보게 할 수 있음.
- **2. 클로저를 제대로 쓴다.**
  ```jsx
  for (var i = 0; i < 5; i++) {
    setTimeout(
      (function (sec) {
        return function () {
          console.log(sec);
        };
      })(i), // 이 i가 sec에 들어감
      i * 1000
    );
  }
  ```
  - for문 내부에 `즉시 실행 익명 함수`를 선언했다.
  - 이 즉시 실행 함수는 i를 인수로 받아서 함수의 sec에 저장해두었다가 setTimeout의 콜백 함수에 넘겨준다.
  - 이렇게 되면 setTimeout의 콜백 함수가 바라보는 클로저는 이 `즉시 실행 익명 함수`가 되고, 이것은 각 for문마다 생성되고 실행되며 각각의 고유한 스코프, 즉 고유한 sec을 가지게 된다.

### **클로저의 추가 비용 💸**

- 클로저는 생성될 때마다 그 **선언적 환경을 기억해야 하므로** 추가로 비용이 발생한다.
- 클로저를 사용한 함수와 그렇지 않은 함수가 메모리에 미치는 영향을 비교해보면

```jsx
// 클로저를 사용하지 않은 함수
function 무거운함수() {
  const 무거운배열 = Array.from({ length: 10000000 }, (_, i) => i + 1);
  console.log(longArr.length);
}

button.addEventListener("click", 무거운함수);

// 클로저를 사용한 함수
function 클로저를쓴무거운함수() {
  const 무거운배열 = Array.from({ length: 10000000 }, (_, i) => i + 1);
  return function () {
    console.log(longArr.length);
  };
}

const 내부함수 = 클로저를쓴무거운함수();
button.addEventListener("click", function () {
  내부함수();
});
```

- **클로저를 사용하지 않은 경우**
  - `무거운함수`는 클릭 이벤트가 발생할 때마다 호출 + 동시에 선언 + 길이 구하는 작업도 스코프 내부에서 끝남
  - `무거운배열`은 함수가 종료되면 메모리에서 해제된다.
  - 실행 전후로 메모리 용량에는 큰 차이가 없음
- **클로저를 사용한 경우**
  - `클로저를쓴무거운함수`는 `무거운배열`을 메모리에 올려두고 시작함
    - 클로저가 선언된 순간 내부함수가 외부함수의 선언적인 환경을 기억해야 하므로!
    - 실제로는 onClick 내부에서만 쓸 것이지만 이를 알 방법이 없음

⇒ 결론 : 클로저에는 꼭 필요한 작업만 남겨두자!

# **1.5 이벤트 루프와 비동기 통신의 이해**

- 동기
  - 한번에 하나의 함수만 실행 → 하나의 함수가 실행되는 동안 나머지는 blocking
  - 싱글스레드, 싱글 프로세스
- 비동기
  - 한번에 여러 개의 함수 실행 → I/O를 수행하는 비동기 함수는 background에 넘김(Non-blocking)
  - 싱글스레드, 멀티 프로세스
  - 스레드가 실행되고 있을 때 다른 작업을 실행할 수 있는 것

## **1.5.1 싱글 스레드 자바스크립트**

- `프로세스` : 프로그램을 구동해 프로그램의 상태가 메모리 상에서 실행되는 작업 단위
  하나의 프로그램 실행은 하나의 프로세스를 가지고 그 프로세스 내부에서 모든 작업이 처리되는 것을 의미했다.
- `스레드` : 프로세스 보다 더 작은 실행 단위
  하나의 프로세스는 여러개의 스레드를 만들 수 있고, 스레드 끼리는 메모리를 공유함(여러 작업 동시 수행 가능)
- `호출 스택` : 자바스크립트에서 수행해야할 코드나 함수를 순차적으로 담아두는 스택
  ⇒ 이 호출 스택을 비어 있는지 확인하는 것이 **`이벤트 루프`**

## **1.5.2 이벤트 루프란?**

!https://velog.velcdn.com/images/snxwxh/post/891a1c1b-4c95-426d-b8dd-51fbd96761e6/image.png

- `콜스택`
  - JS에서 수행해야 할 코드나 함수를 순차적으로 담아두는 스택
- `이벤트 루프`
  - 호출 스택이 비어 있는지 여부를 확인하는 것
  - 이벤트 루프가 콜스택이 비워진 것을 확인 후 태스크 큐를 확인해 있는 내용을 콜스택에 들여보냄
- `태스크 큐`
  - 실행해야 할 태스크의 집합
  - 이벤트 루프는 태스크 큐를 한 개 이상 가짐
  - 이름과 다르게 queue가 아닌 set의 자료구조를 가짐
    ⇒ 선택된 큐 중 실행 가능한 가장 오래된 태스크를 가져와야 하기 때문
- 비동기 함수는 누가 수행할까?
  - 자바스크립트는 싱글 스레드 환경에서 동작하지만, 비동기 함수는 다른 스레드에서 처리된다.
    - 코드 실행은 싱글 스레드에서 이뤄지지만 이러한 외부 Web API 등은 모두 JS 코드 외부에서 실행되고 콜백이 태스크 큐로 들어감
    - **Web API란?**
      - 비동기 작업을 처리하기 위해 브라우저가 제공하는 API
      - 처리 가능한 비동기 작업들: DOM, AJAX, Timeout
      - 브라우저는 Web API의 비동기 작업을 JS 엔진과 별개의 스레드에 위임
      - 해당 스레드가 비동기 작업을 완료하면 함께 전달받은 콜백함수를 콜백 큐에 넣음

**동작 과정**

1. 콜 스택에 현재 실행 중인 컨텍스트가 있는지, 테스크 큐에 대기 중인 함수가 있는지 반복하며 확인
2. 만약 콜 스택이 비어있고, 테스크 큐에 대기중인 함수가 있다면 순차적으로 콜스택으로 이동 후 실행
3. 테스크 큐와 마이크로 테스크 큐에 대기 중인 함수가 있다면, 마이크로 테스크 큐를 우선적으로 처리 후 테스크 큐 처리

## **1.5.3 태스크 큐와 마이크로 태스크 큐**

### 테스크 큐

- Timer 관련 비동기 콜백 함수들이 저장
  - setTimeout()
  - setInterval()
  - setImmediate()

### **마이크로 태스크 큐**

- **Promise 등의 비동기 콜백 함수**들이 저장
  - Promise
  - async / await
  - process.nextTick
  - Object.observe
  - MutationObserver
- 마이크로 태스크 큐는 기존 태스크 큐보다 우선권을 가짐
  - 따라서 setTimeout, setInterval은 Promise보다 늦게 실행됨

**우선순위 :** **Microtask Queue > Animation Frames > Task Queue (브라우저마다 다를 수 있음)**

- 퀴즈 ㅎㅎ

  ```jsx
  function ssol() {
    console.log("휴학하고싶어요");
  }

  function bori() {
    console.log("보리보리쌀");
  }

  function mj() {
    setTimeout(juun, 3000);
    console.log("비액티브하지마세요");
  }

  function juun() {
    console.log("수강신청하셨나요");
  }

  function chaeri() {
    console.log("저토요일에도영이의홈런을보고왔습니다");
  }

  setTimeout(chaeri, 3000);
  setTimeout(mj, 0);

  Promise.resolve().then(bori).then(ssol);
  ```

  - 정답
    ```jsx
    보리보리쌀;
    휴학하고싶어요;
    비액티브하지마세요;
    저토요일에도영이의홈런을보고왔습니다;
    수강신청하셨나요;
    ```
    - bori: 마이크로태스크 큐에서 실행됨
    - ssol: 마이크로태스크 큐에서 실행됨
    - mj: 태스크 큐에서 실행됨
    - chaeri: 3000ms 후에 실행됨. 타이머 먼저 설정됨.
    - juun: 3000ms 후에 실행됨.

# **1.6 리액트에서 자주 사용하는 자바스크립트 문법**

## **1.6.1 구조 분해 할당**

- 객체나 배열의 값을 분해해 개별 변수에 즉시 할당하는 문법
- 객체나 배열에서 선언문 없이 즉시 분해해 변수를 선언하고 할당하고 싶을 때 사용

### **배열 구조 분해 할당**

- ,의 위치에 따라 값이 결정됨.

```jsx
const array = [1, 2, 3, 4, 5];
const [first, , , , fifth] = array;

console.log(first); // 1
console.log(fifth); // 5
```

- 기본값 선언 가능

```jsx
const array = [1, 2];
const [a = 10, b = 10, c = 20] = array;

console.log(a); // 1
console.log(b); // 2
console.log(c); // 20 (기본값)
```

- 전개 연산자를 사용하여 특정값 이후의 값을 배열로 할당 가능

```jsx
const array = [1, 2, 3, 4, 5];
const [first, ...rest] = array;

console.log(first); // 1
console.log(rest); // [2, 3, 4, 5]
```

### 객체 구조 분해 할당

- 새로운 이름으로 재할당 가능

```jsx
const object = { a: 1, b: 2 };
const { a: first, b: second } = object;

console.log(first); // 1
console.log(second); // 2
```

- 기본값을 줄 수 있음

```jsx
const object = { a: 1, b: 2 };
const { a = 10, b = 10, c = 10 } = object;

console.log(a); // 1
console.log(b); // 2
console.log(c); // 10 (기본값)
```

- 트랜스파일 시 번들 크기가 커질 수 있음

## **1.6.2 전개 구문**

- 배열이나 객체, 문자열과 같이 순회할 수 있는 값에 대해 전개해 간결하게 사용할 수 있는 구문

### 배열의 전개 구문

- 배열을 합성하거나 복사할 때 사용! 값만 복사함

```jsx
const arr1 = ["a", "b"];
const arr2 = [...arr1];

arr1 === arr2; // 값만 복사되니까 false
```

### 객체의 전개 구문

- 전개 순서에 따라 값이 덮어씌워짐! 순서 중요

```jsx
const obj = { a: 1, b: 2 };
const aObj = { ...obj, c: 10 };
const bObj = { c: 10, ...obj };

console.log(aObj); // { a: 1, b: 2, c: 10 }
console.log(bObj); // { c: 10, a: 1, b: 2 }
```

- 트랜스파일 시 코드가 커질 수 있음.

## 1.6.3 객체 초기자

- 객체를 간편하게 선언

```jsx
const a = 1;
const b = 2;
const obj = { a, b }; // -> {a:a, b:b} 과정을 한 번 거친다고 이해하면 편함
// {a: 1, b: 2}
```

## 1.6.4 Array 프로토타입의 메서드: map, filter, reduce, forEach

### map, filter, reduce

- 기존 배열을 변경하지 않고 새로운 배열을 만듦.

### forEach

- 반환값이 undefined로 의미가 없음.
- `break, resturn` 등 무엇을 이용해도 배열 순회를 멈출 수 없다. `O(n)`

```jsx
const result = array.forEach((element) => {
  console.log(element);
});

console.log(result); // 반환값이 undefined로 의미가 없음

array.forEach((item) => {
  // item이랑 같이 쓰면 됨
});
```

## 1.6.5 **삼항 조건 연산자**

- 중첩 삼항 조건 연산자는 피하자!
  잘 쓰면 좋은데 흑흑
  ```jsx
  const webFE = isLead
    ? "bori"
    : isViceLead
    ? "juun"
    : isYounger
    ? "jerome"
    : "mj";
  ```
- 근데 전 좋아함
- 찬반 투표 받겠습니다
  | 찬성 | 반대 |
  | --------- | ---- |
  | ㅎㅎ 저요 | |

# **1.7 선택이 아닌 필수, 타입스크립트**

## **1.7.1 타입스크립트란?**

- 타입 체크를 정적으로 런타임이 아닌 빌드(트랜스파일) 타임에 수행할 수 있게 해줌
- TS로 작성된 파일은 결국 JS로 변환돼 Node.js나 브라우저 같은 JS 런타임 환경에서 실행되는 것이 최종 목표

## **1.7.2 리액트 코드를 효과적으로 작성하기 위한 타입스크립트 활용법**

### **any 대신 unknown을 사용하자**

- `any`는 TS 사용의 이점을 모두 없애버림
- 불가피하게 타입을 단정할 수 없을 때는 `unknown`을 사용하자

  - `unknown`

    - 모든 값을 할당할 수 있는 top type
    - unknown으로 선언된 변수 사용 위해서는 type narrowing == 타입을 좁히는 작업이 필요

    ```jsx
    // 그냥 이렇게 쓰면 에러,
    function f(callback: unknown) {
      callback(); // callback is of type unknown
    }

    // 이렇게 타입을 좁혀줘야 함
    function f(callback: unknown) {
      if (typeof callback === "function") {
        callback();
        return;
      }

      throw new Error("callback은 함수여야 함");
    }
    ```

  - `never`

    - top type인 unknown과 반대되는 bottom type

    ```jsx
    type what1 = string & number; // string과 number를 둘 다 만족시키는 타입은 존재하지 않음
    type what2 = ("hello" | "hi") & "react"; // 양쪽 두 타입에 교차점이 없음
    ```

    - 어디에 쓰냐면, TS로 클래스 컴포넌트를 선언할 때 props는 없지만 state도 존재하는 경우, props가 없다는 의미로 사용할 수 있다.

    ```jsx
    type Props = Record<string, never> // 키는 string, 값은 never, 즉 어떠한 값도 올 수 없다는 뜻
    type State = {
        counter: 0
    }

    class SampleComponent extends React.Component<Props, State> { // 여기 이렇게 써야 해서 위에서 타입을 만들어준 것
        ...
    }
    ```

    ⇒ React.Component의 제네릭은 Props와 State를 순서대로 작성해야 한다.
    props가 없는 경우 위 예제처럼 never를 써서 어떠한 props도 받을 수 없도록 TS로 처리할 수 있다.

### **타입 가드를 적극 활용하자**

- 타입을 사용하는 쪽에서는 최대한 타입을 좁히는 것이 좋음
- `instanceof` : 지정한 인스턴스가 특정 클래스의 인스턴스인지 확인할 수 있는 연산자
  - ex) catch문의 err는 unknown으로 내려오는데, 이를 타입 가드를 통해 각 에러에 따라 처리할 수 있음
    ```jsx
    ...
    } catch (e){
        if(e instanceof UnAuthorizedError){
            ...
        }
        if(e instanceof UnExpectedError){
            ...
        }
    }
    ```
- `typeof` : 특정 요소에 대해 자료형을 확인하는 데 사용됨
- `in`

  - property in object로 사용됨
  - 주로 어떤 객체에 키가 존재하는지 확인하는 용도
  - ex) 타입에 여러 가지 객체가 존재할 경우 유용함

    ```jsx
    interface Student {
        age: number
        score: number
    }
    interface Teacher {
        name: string
    }

    function f(person: Student | Teacher) {
        if('age' in person){
            // 여기서는 age가 있는 Student가 해당
        }
        if('name' in person){
            // 여기서는 name가 있는 Teacher가 해당
        }
    }
    ```

- **type VS interface**
  | | interface | type |
  | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
  | 선언 병합 (Declaration Merging) | 선언 병합 지원 | 지원하지 않음 |
  | 확장성 | extends 로 다른 인터페이스 상속 가능 | 상속 키워드 X, 교차 타입 (& 연산자)를 이용해 타입을 조합할 수는 있음 |
  | 사용의 유연성 | 주로 객체의 모양을 정의하는 데 사용 / 튜플이나 유니온을 직접 표현할 수 없지만, 인덱싱 가능한 타입(ex.배열)은 정의할 수 있음 | 객체, 원시 값, 배열, 튜플, 유니온, 인터섹션 등 다양한 타입의 조합을 정의할 수 있음 |

  - 퀴즈 ㅎㅎ 다음 코드에서 틀린 부분은?

    ```tsx
    type Person = {
      name: string;
      age: number;
    };
    type Person = {
      job: string;
    };

    interface Person {
      name: string;
      age: number;
    }
    interface Person {
      job: string;
    }
    ```

    - 정답
      interface를 중복으로 선언하면 필드가 자동으로 합쳐지고 type은 오류 발생한다
      따라서 type으로 선언한 것은 중복 선언을 제거하고 한 번에 선언하도록 수정해야 한다

### **제네릭**

- 단일 타입이 아닌 다양한 타입에 대응할 수 있게 도와주는 도구
- `useState()`로 쓰면 값을 undefined로 추론해버리는데, 제네릭으로 기본값을 선언해주면 이런 문제를 TS가 방지해줌

```jsx
const [state, setState] = useState < string > "";
```

### **인덱스 시그니처**

- 객체의 키를 동적으로 정의할 수 있음.
- `Record<Key, Value>`를 사용하여 객체의 타입을 정의.

```tsx
type Hello = Record<"hello" | "hi", string>;

const hello: Hello = {
  hello: "hello",
  hi: "hi",
};
```

## 1.7.3 타입스크립트 전환 가이드

### **tsconfig.json 먼저 작성하기**

```jsx
{
  "compilerOptins": {
	  "outDir": "./dist",
	  "allowJs": true,
	  "target": "es5"
  },
  "include": ["./src/**/*"]
}
```

- `outDir`: 트랜스파일된 결과를 저장할 폴더, tsc 실행 시 결과물이 여기에 들어감
- `allowJs`: JS 파일 허용 여부
- `target`: 트랜스파일된 JS의 버전
- `include`: 트랜스파일할 파일의 경로

### **JSDoc과 @ts-check 사용**

- 파일 상단에 `//@ts-check`를 선언하고 JsDoc을 활용하여 변수나 함수에 타입을 제공하면 ts파일로 전환 안해도 타입 체크 가능!

### **@types 모듈 설치하기**

- 과거 JS 기반으로 작성된 라이브러리를 TS에서 사용하려면 `@types` 모듈을 설치해야 함
  - `@types/react`, `@types/react-dom`
  - 최신 라이브러리는 자체적으로 TS 지원 기능이 내장되어 있을 수 있음.
- “Cannot find module ‘lodash’ or its corresponding type declarations” 에러
  - 라이브러리 내부에 별도로 d.ts 같은 타입 파일 제공하지 않아서임
- @types를 검색해 별도 타입 제공하는 라이브러리가 있는지 확인하고 설치하자

### **파일 단위로 전환**

- 상수, 유틸 등 의존성이 없는 파일부터 전환
- 파일을 하나씩 TS로 전환하고, 상수는 string, number와 같이 원시값 대신 가능한 한 타입을 좁혀보자!
  → 이거를 해봤는데요,,, 하다가 포기했습니다
  그냥 처음부터 ts로 하는게 더 빠른 것 같아요
  ㅎㅎ
  ㅎㅎ..
