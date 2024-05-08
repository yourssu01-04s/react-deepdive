# 01장: 리액트 개발을 위해 꼭 알아야 할 자바스크립트

- 토글 내의 목차를 통해 원하는 곳으로 바로 이동할 수 있습니다.

<br/>

<details>
<summary>목차</summary>
<div>

  - [1.1 자바스크립트의 동등 비교](#11-자바스크립트의-동등-비교)
    - [1.1.1 자바스크립트의 데이터 타입](#111-자바스크립트의-데이터-타입)
    - [1.1.2 값을 저장하는 방식의 차이](#112-값을-저장하는-방식의-차이)
    - [1.1.3 자바스크립트의 또 다른 비교 공식, Object.is](#113-자바스크립트의-또-다른-비교-공식-objectis)
    - [1.1.4 리액트에서의 동등 비교](#114-리액트에서의-동등-비교)
  - [1.2 함수](#12-함수)
    - [1.2.2 함수를 정의하는 4가지 방법](#122-함수를-정의하는-4가지-방법)
    - [1.2.3 다양한 함수 살펴보기](#123-다양한-함수-살펴보기)
    - [1.2.4 함수를 만들 때 주의해야 할 사항](#124-함수를-만들-때-주의해야-할-사항)
  - [1.3 클래스](#13-클래스)
    - [1.3.1 클래스란 무엇인가?](#131-클래스란-무엇인가)
    - [1.3.2 클래스와 함수의 관계](#132-클래스와-함수의-관계)
  - [1.4 클로저](#14-클로저)
    - [1.4.1 클로저의 정의](#141-클로저의-정의)
    - [1.4.2 변수의 유효 범위, 스코프](#142-변수의-유효-범위-스코프)
    - [1.4.3 클로저의 활용](#143-클로저의-활용)
    - [1.4.4 주의할 점](#144-주의할-점)
  - [1.5 이벤트 루프와 비동기 통신의 이해](#15-이벤트-루프와-비동기-통신의-이해)
    - [1.5.1 싱글 스레드 자바스크립트](#151-싱글-스레드-자바스크립트)
    - [1.5.2 이벤트 루프란?](#152-이벤트-루프란)
    - [1.5.3 태스크 큐와 마이크로 태스크 큐](#153-태스크-큐와-마이크로-태스크-큐)
  - [1.6 리액트에서 자주 사용하는 자바스크립트 문법](#16-리액트에서-자주-사용하는-자바스크립트-문법)
    - [1.6.1 구조 분해 할당](#161-구조-분해-할당)
    - [1.6.2 전개 구문](#162-전개-구문)
    - [1.6.4 Array 프로토타입의 메서드: map, filter, reduce, forEach](#164-array-프로토타입의-메서드-map-filter-reduce-foreach)
    - [1.6.5 삼항 조건 연산자](#165-삼항-조건-연산자)
  - [1.7 선택이 아닌 필수, 타입스크립트](#17-선택이-아닌-필수-타입스크립트)
    - [1.7.2 리액트 코드를 효과적으로 작성하기 위한 타입스크립트 활용법](#172-리액트-코드를-효과적으로-작성하기-위한-타입스크립트-활용법)
    - [1.7.3 타입스크립트 전환 가이드](#173-타입스크립트-전환-가이드)

</div>
</details>

<br>

## 1.1 자바스크립트의 동등 비교
### 1.1.1 자바스크립트의 데이터 타입

- null

  - 왜 typeof null은 object 일까? ([참고 자료](https://2ality.com/2013/10/typeof-null.html))

- boolean

  - 빈 배열 []과 빈 객체 {}는 모두 truthy한 값이다

- string
  
  - 문자열은 원시 타입이며 변경 불가능하다 (문자열 메서드도 새 문자열을 만들어서 반환하지 원본을 변경하진 않음)

    > 브라켓([ ]) 표기법을 사용하여 문자에 접근하는 경우, **이러한 프로퍼티들에 새로운 값을 할당하거나 삭제할 수는 없습니다.** <br/> 포함되어 있는 프로퍼티들은 작성할 수도, 수정할 수도 없습니다. - [MDN String](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String)

    ![image](https://github.com/yourssu/Soomsil-Web/assets/87255462/c3c21783-66a4-4116-9bd2-2ddc681b7a5b)

- Symbol

  - 중복되지 않는 어떠한 고유한 값을 나타내기 위한 타입

    ➡ JSX에서 `array.map()`을 사용할 때 Symbol을 key로 넣으면 고민할 필요 없지 않나? 라는 의문이 생겼는데 비슷한 이슈가 있었음 ([링크](https://github.com/facebook/react/issues/11996))


### 1.1.2 값을 저장하는 방식의 차이

- 원시 타입은 **불변 형태의 값**으로 저장된다

- 객체 타입은 **변경 가능한 형태**로 저장된다 = 같은 값이어도 다른 주소를 가졌다면 동등 비교에서 false를 반환한다

### 1.1.3 자바스크립트의 또 다른 비교 공식, Object.is
### 1.1.4 리액트에서의 동등 비교

- 리액트에서는 Object.is()를 기반으로 한 shallowEqual로 동등 비교를 진행한다.

  - is() ([코드 출처](https://github.com/facebook/react/blob/main/packages/shared/objectIs.js))
    ```js
    /**
     * Copyright (c) Meta Platforms, Inc. and affiliates.
    *
    * This source code is licensed under the MIT license found in the
    * LICENSE file in the root directory of this source tree.
    *
    * @flow
    */

    /**
     * inlined Object.is polyfill to avoid requiring consumers ship their own
    * https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is
    */

    function is(x: any, y: any) {
      return (
        (x === y && (x !== 0 || 1 / x === 1 / y)) || (x !== x && y !== y) // eslint-disable-line no-self-compare
      );
    }

    const objectIs: (x: any, y: any) => boolean =
      // $FlowFixMe[method-unbinding]
      typeof Object.is === 'function' ? Object.is : is;

    export default objectIs;
    ```

    `x !== x && y !== y`는 x와 y가 모두 NaN일 때만 참이 됨. 그래서 Object.is(Number.NaN, NaN)이 true였던 것

    > NaN은 **다른 NaN 값을 포함하여** 다른 값과 같지 않은 것으로 비교된다. (...) 또는 NaN은 자신과 같지 않다고 비교되는 유일한 값이므로 x !== x와 같은 자체 비교를 수행할 수 있습니다. - ([MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/NaN))
    

  - shallowEqual() ([코드 출처](https://github.com/facebook/react/blob/main/packages/shared/shallowEqual.js))
    ```js
    /**
     * Copyright (c) Meta Platforms, Inc. and affiliates.
    *
    * This source code is licensed under the MIT license found in the
    * LICENSE file in the root directory of this source tree.
    *
    * @flow
    */

    import is from './objectIs';
    import hasOwnProperty from './hasOwnProperty';

    /**
     * Performs equality by iterating through keys on an object and returning false
    * when any key has values which are not strictly equal between the arguments.
    * Returns true when the values of all keys are strictly equal.
    */
    function shallowEqual(objA: mixed, objB: mixed): boolean {
      if (is(objA, objB)) {
        return true;
      }

      if (
        typeof objA !== 'object' ||
        objA === null ||
        typeof objB !== 'object' ||
        objB === null
      ) {
        return false;
      }

      const keysA = Object.keys(objA);
      const keysB = Object.keys(objB);

      if (keysA.length !== keysB.length) {
        return false;
      }

      // Test for A's keys different from B.
      for (let i = 0; i < keysA.length; i++) {
        const currentKey = keysA[i];
        if (
          !hasOwnProperty.call(objB, currentKey) ||
          // $FlowFixMe[incompatible-use] lost refinement of `objB`
          !is(objA[currentKey], objB[currentKey]) // 💥
        ) {
          return false;
        }
      }

      return true;
    }

    export default shallowEqual;
    ```

    첫번째 조건문: 원시 타입의 비교를 위한 조건문

    두번째 조건문: 둘다 유효한 객체임을 보장하기 위한 조건문

    💥: value를 `is()`로 비교하므로 value가 객체 타입이면 shallowEqual()이 false를 반환하게 됨

     - prop이 객체 타입이면 리렌더링 될 수 밖에 없음
     
       > React는 얕은 비교를 기준으로 이전 props와 새로운 props를 비교합니다. 즉, 각각의 새로운 prop가 이전 prop와 참조가 동일한지 여부를 고려합니다. <br/>부모가 리렌더링 될 때마다 새로운 객체나 배열을 생성하면, 개별 요소들이 모두 동일하더라도 React는 여전히 변경된 것으로 간주합니다. - [React.memo 문서](https://ko.react.dev/reference/react/memo#my-component-rerenders-when-a-prop-is-an-object-or-array)

       ➡ prop이 새롭게 생성되는 걸 막기 위해 `useMemo`를 사용하거나 / 비교 함수를 사용자가 정의해서 내려줄 수 있음
      
<br>

## 1.2 함수

### 1.2.2 함수를 정의하는 4가지 방법

- 일급 객체: 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체

  함수는 일급 객체이기 때문에 변수에 할당 / 파라미터로 전달 / 리턴 값으로 사용이 가능하다.

### 1.2.3 다양한 함수 살펴보기

- 즉시 실행 함수

  함수를 통해 로직을 재사용 가능한 단위로 분리한다고 생각했는데, 즉시 실행 함수는 이 방식과 달라서 신기했음

  "단 한번만 수행된다"는 점에서 `useEffect(setup, [])`가 생각났는데 아직 useEffect 내에서 즉시 실행 함수를 사용하는 경우 밖에 못봄

### 1.2.4 함수를 만들 때 주의해야 할 사항

부수 효과를 (아예 없앨 수는 없지만) 최대한 억제하자

- 부수 함수: 함수의 핵심 목적에서 벗어나 **외부 세계에 영향을 주는 행위가 포함된** 함수

  외부의 정보가 끼어드는 순간 실행 시점, 횟수 등에 따라 결과가 달라질 수 있음

  (예: 서버 데이터를 받아오는 함수는 서버가 잘 돌아갈 때랑 터졌을 때 결과가 다르다)

  ✨ `Math.random()`, `new Date()` 같은 메서드도 매번 다른 값을 생성하니까 순수 함수가 아님

- 억지 순수 함수를 만들지 않게 주의하자

<br>

## 1.3 클래스

함수형 컴포넌트를 더 많이 작성한다고 해도 클래스형 컴포넌트를 아예 배제할 수 없음

예를 들어 라이브러리 없이 error boundary를 구현하려면 클래스형 컴포넌트를 사용할 수 밖에 없다

### 1.3.1 클래스란 무엇인가?

클래스를 만들 때 고려하면 좋을 점

- 불필요한 필드가 없는지 점검한다

  필드 수가 많으면 객체가 복잡해지고, 버그 발생 가능성을 높일 수 있다.

- 외부에서 객체 상태에 직접 접근하는 것을 최소화한다

- 무분별한 getter를 만들기보단 객체가 로직을 수행하도록 하자

  모든 필드에 대한 getter를 생성해놓고 객체 외부에서 로직을 수행하는 코드는 객체지향적이지 못하다

  [getter를 사용하는 대신 객체에 메시지를 보내자](https://tecoble.techcourse.co.kr/post/2020-04-28-ask-instead-of-getter/)

### 1.3.2 클래스와 함수의 관계

프로퍼티 플래그 = 특별한 속성이라고 생각하면 됨 ([프로퍼티 플래그와 설명자](https://github.com/javascript-tutorial/ko.javascript.info/blob/master/1-js/07-object-properties/01-property-descriptors/article.md))

- `writable`: 값을 수정할 수 있는가?

- `enumerable`: 반복문을 통해 나열할 수 있는가?

- `configurable`: 프로퍼티 삭제나 플래그 수정이 가능한가?

<br>

## 1.4 클로저

### 1.4.1 클로저의 정의

[꼬재의 클로저](https://youtu.be/PJjPVfQO61o?si=PVkIaj4IVWl97vEE)에서 좀 더 심화된 내용을 다루고 있습니다 👍

- 클로저는 어휘적 환경(Lexical scope)을 조합해서 코딩하는 기법이다

  - 어휘적 환경 = 코드 내부에서 어디서 선언됐는지 (정적으로 결정됨)

- 다른 정의

  - [MDN 정의](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)

    주변 상태(어휘적 환경)에 대한 참조와 함께 묶인(포함된) 함수의 조합

  - 코어 자바스크립트 정의
  
    함수 A에서 선언한 변수 a를 참조하는 내부 함수 B를 외부로 전달할 경우,

    A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상

    - 함수 선언 시점에, B의 실행 컨텍스트에서는 외부 함수의 어휘적 환경을 기억하기 때문에 이게 가능함

### 1.4.2 변수의 유효 범위, 스코프

- 전역 스코프

- 함수 스코프

  - "js는 블록 레벨 스코프가 아니다" ➡ `var`에만 해당하는 이야기임. `let`/`const`는 블록 스코프다

### 1.4.3 클로저의 활용

- `setState`는 `useState` 호출 이후에도 `state`의 최신 값을 기억하고 있다
 
  - [2019 JSConf.Asia - Can Swyx recreate React Hooks and useState in under 30 min?](https://www.youtube.com/watch?v=KJP1E-Y-xyo)
  - [한국어 정리 글](https://www.fronttigger.dev/2022/react/react-hook-closure)

 

### 1.4.4 주의할 점

- 부수 효과가 없고 순수해야 한다는 목적을 달성하기 위해 적극적으로 사용되는 개념이다

- 외부 함수를 기억해야 하므로 메모리 성능에 악영향을 줄 수 있음

<br>

## 1.5 이벤트 루프와 비동기 통신의 이해

### 1.5.1 싱글 스레드 자바스크립트

- 싱글 스레드 = 1개의 Call Stack을 가진다 = 한번에 하나의 작업만 동기로 처리한다

  - 동기 = 하나의 작업을 끝낸 뒤에 다음 작업을 처리할 수 있다

- 프로세스 = 실행 중인 프로그램

  - 스레드 = 프로세스 내의 실행 흐름 단위. 프로세스 내의 스레드들은 자원을 공유함 (코드/데이터/힙 영역)

    여러 스레드로 프로세스를 동시에 실행하는 것 = 멀티 스레드 -> 동시성 문제 / 한 스레드가 죽었을 때 다른 스레드에 주는 영향을 고려해야함 👎

### 1.5.2 이벤트 루프란?
### 1.5.3 태스크 큐와 마이크로 태스크 큐

[병민의 브라우저의 Event Loop](https://www.youtube.com/watch?v=YpQTeIqjC4o) 영상에서 좀 더 심화된 내용을 다루고 있습니다 👍

- 이벤트 루프는 반복적으로 Call Stack에 실행 중인 코드가 있는지 / MacroTaskQueue에서 대기 중인 함수가 있는지 확인

  Call Stack이 다 비면 ➡ MicroTaskQueue에 있는 작업 실행 ➡ MacroTaskQueue에서 대기 중인 작업 중 오래된 것부터 실행

- 우선 순위

  - Call Stack

  - MicroTaskQueue : Promise, MutationObserver

  - MacroTaskQueue : 비동기 함수의 콜백, 이벤트 핸들러 등

- 렌더링 기회는 MicroTaskQueue 작업이 끝날 때마다 주어진다 

  ➡ 그럼 78p에서 MicroTaskQueue 한번 끝날 때마다 렌더링 해야 맞는 거 아닌지?
   
     **MicroTaskQueue는 처리 중간에 작업이 더 들어오면 바로 스트레이트로 처리하기 때문에** 책 내용이 맞음

- 코드가 동기식으로 실행되는 `메인 스레드`가 있고 태스크 큐가 할당되는 `별도의 스레드`가 있다

  JS는 분명 싱글 스레드라고 했는데 이게 무슨 말이냐

  - 코드 실행은 싱글 스레드고 (메인 스레드)

  - 비동기 작업은 브라우저나 Node.js (아무튼 JS 외부)에서 실행되고 콜백만 MacroTaskQueue로 넣어줌

<br>

## 1.6 리액트에서 자주 사용하는 자바스크립트 문법

### 1.6.1 구조 분해 할당

- 객체의 구조 분해 할당이 바벨로 트랜스파일된 걸 보면 `objectWithoutPropertiesLoose()`랑 `objectWithoutProperties()`랑 뭔 차이지?

  = `if(Object.getOwnPropertySymbols)`의 유무
    
  해당 메서드는 객체 내에 존재하는 심볼 속성을 반환하고, 조건문 내에서는 아래 두 가지를 판단함
    
  - source 객체 중 심볼 속성을 추출 ➡ (각 심볼에 대해) excluded 배열에 접근해서 존재하는지 (제거 대상이면 continue)

  - 열거 가능한 속성인지 (enumerable = false면 continue)

    - 객체에서 열거 불가능한 속성이 뭐가 있냐: 심볼 속성, 메서드, 등등....

- `ramda.omit`은 어떻게 생겼을까

  - [코드 출처](https://github.com/ramda/ramda/blob/master/source/omit.js) (책에 있는 링크로 안 들어가짐)

    ```js
    // R.omit(['a', 'd'], {a: 1, b: 2, c: 3, d: 4}); //=> {b: 2, c: 3}
    var omit = _curry2(function omit(names, obj) {
    var result = {};
    var index = {};
    var idx = 0;
    var len = names.length;

    while (idx < len) {
      index[names[idx]] = 1; // index = { a: 1, d: 1 }
      idx += 1;
    }

    for (var prop in obj) {
      if (!index.hasOwnProperty(prop)) { // a, d는 !true = false라서 조건문으로 안 들어옴
        result[prop] = obj[prop];
      }
    }
    return result;
    });
    ```

- 리액트에서의 객체 구조 분해 할당 사용 예시

  - 컴포넌트에 props를 내려줄 때 / 값을 꺼내올 때 객체의 구조 분해 할당을 사용해서 간결하게 표현할 수 있음 

    ```tsx
    const TestComponent1 = () => {
        const props = { name: 'test', color: 'tomato' }
        return <TestComponent2 {...props} />;
    }
    
    const TestComponent2 = ({ name, color }) => {}
    ```

### 1.6.2 전개 구문

- 리액트에서의 사용 예시

  - 배열 상태를 업데이트 할 때 (기존 상태의 참조값과 다른 새 배열을 만들어야 리렌더링이 일어남)

    ```ts
    const [arr, setArr] = useState([])

    const addElement = (num) => {
        setArr((prev) => [...prev, num])
    }
    ```

### 1.6.4 Array 프로토타입의 메서드: map, filter, reduce, forEach

- map : 배열 순회하면서 반환 값이 있을 때
- forEach : 배열 순회만 하면 될 때

### 1.6.5 삼항 조건 연산자

- 조건부 렌더링에 많이 사용함

<br>

## 1.7 선택이 아닌 필수, 타입스크립트

- 해당 챕터는 <이펙티브 타입스크립트>와 같이 읽으시면 좋을 거 같아서 관련 챕터 적어놨습니다

- 책 볼 시간이 없으면 Web FE팀에서 진행했던 [이펙티브 타입스크립트 스터디 자료](https://www.notion.so/yourssu/ef84ad56ceb9425f9a04e9113fb05148) 보는 것도 추천 ^^...

### 1.7.2 리액트 코드를 효과적으로 작성하기 위한 타입스크립트 활용법

- any 대신 unknown을 사용하자 (이펙티브 타입스크립트 아이템 42)

  - any 쓸 거면 TS 쓰는 의미가 없다

  - top type인 unknown과 반대되는 bottom type never
    
    타입 계층도를 생각하면 이 말을 이해하기 쉬울 것 같음

    ![타입 계층도](https://github.com/yourssu/Soomsil-Web/assets/87255462/6bb871c1-3cdb-4873-b94f-13ea89bf7b9d)

    (사진 출처: [2023 인프콘 : 타입 스크립트는 왜 그럴까? 집합으로 이해하는 타입 스크립트](https://www.inflearn.com/course/%EC%9D%B8%ED%94%84%EC%BD%982023-%EB%8B%A4%EC%8B%9C%EB%B3%B4%EA%B8%B0) 강의자료)

- 타입 가드를 적극 활용하자 (이펙티브 타입스크립트 아이템 22)

- 인덱스 시그니처 (이펙티브 타입스크립트 아이템 54)

  - 객체의 키에 포괄적으로 대응하기 위해 `Object.keys()`는 string[] 타입을 제공한다 ➡ `keyof`로 단언하기

    `Record<string, VALUE TYPE>` 타입이 아닌 객체에는 `Object.keys()`에서 얻은 키로 접근할 수 없다

    ![image](https://github.com/yourssu/Soomsil-Web/assets/87255462/00f1f971-8565-43fb-be87-d3678a49dd49)

  - `Exact` 타입에 대한 논의는 어떻게 되어가고 있을까? ➡ 아직도 open 되어있음 ([링크](https://github.com/Microsoft/Typescript/issues/12936))


### 1.7.3 타입스크립트 전환 가이드

- tsconfig.json 먼저 작성하기 (이펙티브 타입스크립트 아이템 2)

- JSDoc과 @ts-check를 활용해 점진적으로 전환하기 (이펙티브 타입스크립트 아이템 59)
