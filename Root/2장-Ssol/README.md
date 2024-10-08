# **2.1 JSX란?**

**XML 스타일의 트리 구조**로 표현하고 싶은 것들을 자바스크립트와 함께 작성하는 문법이다.

자바스크립트로 트랜스파일링되어 실행된다. **트리 구조를 토큰화해 ECMAScript로 변환!**

### **JSX의 구성 요소**

- `JSXElement`: HTML element와 비슷한 역할을 하는 컴포넌트
- `JSXAttributes`: JSXElement에 부여할 수 있는 속성, 필수가 아님 `isActive={true}`
- `JSXChildren`: JSXElement의 자식, 0개 이상 존재 가능
- `JSXStrings`: 문자열

https://velog.io/@hbsps/React-createElement로-리팩토링-하기-권장-X

### **리액트에서는 html이 아닌 JSX를 사용하는 이유**

- JS 코드와 UI 코드를 서로 가깝게 위치시킬 수 있어 데이터를 처리하고 그 결과를 바로 UI에 반영하는 것이 쉽다.
- JSX는 JS로 변환되기 때문에, 리액트가 JSX를 처리하면서 성능 최적화를 수행할 수 있다.
  - 예를 들어, 컴포넌트가 실제로 변경되었을 때만 DOM을 업데이트하는 등의 최적화
- JSX 없이도 리액트를 사용할 수 있지만, 활용하면 리액트의 장점을 보다 쉽게 활용할 수 있다.

# **2.2 가상 DOM과 리액트 파이버**

리액트의 특징 중 하나인 가상 DOM과 리액트 파이버에 대해 알아보자~

## 2.2.1 DOM과 브라우저 렌더링 과정

### 브라우저의 렌더링 과정

![스크린샷 2024-08-10 오후 5.01.48.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/994a9445-7449-42ce-b9f7-46457378580d/df73a8bf-5159-4fdb-9d45-d876509cbce6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-08-10_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.01.48.png)

1. **HTML 파일 다운로드:**
   - 브라우저가 사용자가 요청한 URL을 방문해 해당 HTML 파일을 다운로드합니다.
2. **DOM 트리 구성:**
   - 브라우저의 렌더링 엔진은 HTML을 파싱하여 DOM(Document Object Model) 트리를 만듭니다. 이 DOM 트리는 HTML 요소들 간의 계층적 구조를 나타냅니다.
3. **CSS 파일 다운로드:**
   - 브라우저는 HTML을 파싱하는 과정에서 CSS 파일을 참조하는 태그를 만나면 해당 CSS 파일을 다운로드합니다.
4. **CSSOM 트리 구성:**
   - 브라우저의 렌더링 엔진은 다운로드된 CSS 파일을 파싱하여 CSSOM(CSS Object Model) 트리를 만듭니다. 이 트리는 CSS 규칙들이 각 DOM 요소에 어떻게 적용되는지를 나타냅니다.
5. **렌더 트리 생성:**
   - 브라우저는 DOM과 CSSOM을 합쳐서 렌더 트리를 구성합니다. 여기서 중요한 점은 모든 DOM 노드가 렌더 트리에 포함되는 것이 아니라, 화면에 보이는 요소들만 포함된다는 점입니다. 예를 들어, `display: none`과 같이 화면에 보이지 않는 요소는 렌더 트리에 포함되지 않습니다.
6. **레이아웃 및 페인팅:**
   - 렌더 트리가 완성되면, 브라우저는 레이아웃과 페인팅 과정을 수행합니다. 이 부분이 비용이 가장 많이 든다
     - **레이아웃(Layout, Reflow):** 각 노드가 화면에서 정확히 어디에 위치할지를 계산합니다. 레이아웃이 완료되면 페인팅 과정이 시작됩니다.
     - **페인팅(Painting):** 레이아웃이 계산된 노드들에 색상, 이미지 등을 그려서 화면에 실제로 표시되도록 합니다.

### **가상 DOM 탄생 배경**

DOM의 모든 변경 사항을 추적하는 것 보다 결과적으로 만들어지는 DOM 결과물을 아는 것이 높은 개발 편의성을 제공해주기 때문!

### **가상 DOM은 어떤 문제를 해결하기 위해 도입되었을까?**

렌더링 이후 추가 렌더링 작업이 많아지며 DOM을 관리하는 과정에서 부담해야 할 비용이 커졌다.

- 특히 SPA의 도입으로!
  싱글 페이지 애플리케이션(SPA)은 모든 콘텐츠를 하나의 페이지에서 동적으로 렌더링한다.
  페이지가 변경될 때마다 전체 HTML을 다시 로드하지 않고 필요한 부분만 업데이트한다는 뜻!
  이로 인해 사용자는 빠르고 매끄러운 페이지 전환을 경험할 수 있지만 DOM을 자주 조작하고 업데이트해야 하기 때문에 브라우저 성능에 부담이 될 수 있다.
  특히, 라우팅이 변경될 때 기존 요소를 삭제하거나 새로운 요소를 삽입하는 작업이 빈번히 발생하면서 DOM 관리가 복잡해지고, 성능 비용이 증가할 수 있음.

### **가상 DOM에 대한 일반적인 오해**

**가상 DOM은 직접 DOM을 조작하는 것 보다 빠르다?? 놉 🙅**

⇒ 가상 DOM의 diffing, 배치 업데이트 과정에 추가적인 리소스 소모가 있을 수 있다.

- diffing이란?
  이전 트리와의 difference를 구하는 작업, 컴포넌트가 어떻게 바뀌었는지를 계산하는 것!

실제로 직접 DOM을 조작해서 리액트보다 더 빠른 속도를 가진 라이브러리들이 존재한다.

(ex: Svelte, Solid.js)

### 가상 DOM의 이점

1. 상태 변경에 따라 전체 UI를 새로 그리는 것 처럼 개발할 수 있으나 실제 DOM 반영은 일부만 되는 것
2. 컴포넌트 트리를 가상 DOM이란 레이어로 추상화하여 실제로 그리는 렌더러를 바꿀 수 있는 것

- 그럼 가상 DOM이 느릴 수 있는 경우는?

  - 변경 사항이 매우 간단하거나 단일 요소만 업데이트하는 경우 오히려 오버헤드를 발생시킬 수 있다.

    - 오버헤드 예시
      **단일 요소의 간단한 스타일 변경**
      버튼을 클릭하면 버튼의 배경색이 바뀌는 간단한 스타일 변경!

      ```jsx
      function MyButton() {
        const [color, setColor] = useState("blue");

        return (
          <button
            onClick={() => setColor("red")}
            style={{ backgroundColor: color }}
          >
            Click me
          </button>
        );
      }
      ```

      **가상 DOM에서의 동작**

      1. 사용자가 버튼을 클릭하면, `setColor('red')`가 실행되어 상태가 변경된다.
      2. 리액트는 이 변경을 감지하고, 가상 DOM 트리를 새로 생성한다.
      3. 새 가상 DOM 트리와 기존 트리를 비교하여 변경된 부분(버튼의 `backgroundColor`)을 찾아낸다.
      4. 변경 사항을 실제 DOM에 반영한다.
         **오버헤드 발생 가능성**

      - 리액트는 간단한 변경을 처리하기 위해 전체 가상 DOM을 새로 생성하고 비교하는 과정을 거친다. 우우…👎👎👎
      - 순수 JavaScript나 jQuery를 사용했다면??
        ```jsx
        document.getElementById("myButton").style.backgroundColor = "red";
        // 한 줄의 코드로 직접 DOM 요소를 선택하여 스타일을 변경하는 것이 가능!
        ```

  - 가상 DOM은 메모리에 전체 DOM의 사본을 유지하므로, 메모리 사용량이 증가할 수 있다. 따라서 메모리 자원이 제한된 환경에서 문제가 될 수 있다.

<aside>
✨ dan abramov : 가상 DOM은 무조건 빠른 것이 아니라 대부분의 상황에 웬만한 애플리케이션을 만들 정도로 충분히 빠르다.

</aside>

---

## **리액트 파이버로 알아보는 리액트 렌더링!**

### 리액트의 렌더링

보통의 렌더링 : 브라우저에서 HTML과 CSS를 기반으로 웹페이지에 필요한 UI를 그리는 과정!

리액트에서의 렌더링 : 브라우저에 필요한 DOM 트리를 생성하는 과정! → 렌더링 프로세스가 따로 있다

**리액트의 렌더링 과정**

1. **렌더링 유발 단계**: `createRoot`의 실행이나 `state` 업데이트가 발생할 때 렌더링이 유발 된다.
2. **렌더링으로 컴포넌트 호출 단계**:
   - `createRoot`로 인해 렌더링이 발생하면 루트 컴포넌트가 호출된다.
   - `state`의 업데이트로 인해 렌더링이 발생하면 해당 `state`가 속한 컴포넌트가 호출된다.
3. **커밋 단계**: 변경된 사항들을 실제 DOM에 적용하는 단계 ⇒ `commitWork()`
   - 첫 커밋의 경우 `appendChild`를 사용하여 스크린에 있는 모든 노드를 생성한다.
   - 첫 커밋이 아니라면 최소한의 작업을 통해 변경사항만을 실제 DOM에 적용한다. 그리고 이 변경사항은 렌더링 중에 계산된다.
   - 이후 렌더링에서는 최소한의 작업만을 통해 변경사항을 DOM에 적용한다.

<aside>
✨ **가상 DOM을 활용한 Reconciliation(재조정) 과정은 두 번째 단계인 렌더링 단계에서 이루어진다.**

</aside>

### 리액트의 렌더링은 언제 일어날까?

1. **최초 렌더링**: 사용자가 어플리케이션에 처음 진입할 때, 리액트는 해당 화면을 보여주기 위해 최초 렌더링을 수행한다.
2. **리렌더링**: 최초 렌더링 이후, 어플리케이션에서 발생하는 모든 추가적인 렌더링을 의미한다. 리렌더링은 다음과 같은 경우에 발생한다.
   - **State 변경**: 함수형 컴포넌트에서 `useState()`의 setter 함수가 실행될 때 컴포넌트의 상태가 변경되며 리렌더링이 발생한다.
   - **Props 변경**: 부모 컴포넌트로부터 전달받은 props가 변경되면 해당 props를 사용하는 자식 컴포넌트에서 리렌더링이 일어난다.
   - **Key props 변경**: 리액트에서 `key`는 특수한 props로 주로 리스트 내 하위 컴포넌트를 식별하기 위해 사용된다. `key`가 변경되면 해당 컴포넌트에서 리렌더링이 발생한다.

- `useReducer()`의 동작이나 클래스형 컴포넌트의 경우에도 리렌더링이 발생할 수 있지만 생략,,,

### 가상 DOM VS 실제 DOM

- **DOM 개념**: DOM(Document Object Model)은 웹 페이지(==Document)를 조작할 수 있게 해주는 API
- **리액트의 렌더링 프로세스**: **DOM 결과를 브라우저에게 제공할 것인지 계산하는 과정**
  ⇒ 리액트는 결과를 어떻게 계산할까?
  실제 DOM을 추상화하여 만들어진 가상 DOM의 변화를 감지해서 변경 결과를 도출한다.
  컴포넌트의 상태가 변경되면 리액트는 이 변경을 감지하고 기존 가상 DOM 트리와 변경된 가상 DOM 트리을 비교한다. 이런 알고리즘을 **Reconciliation**(재조정)이라고 하며 가상 DOM의 탐색 성능을 최적화했다.
- **Reconciliation에 대해서**
  리액트에서 말하는 재조정이란 무엇일까?
  ⇒ 리액트에서 변경된 부분만 새롭게 렌더링 하기 위해 가상 DOM과 실제 DOM을 비교하는 작업(알고리즘)
  리액트는 `render()`를 통해 리액트 엘리먼트 트리를 만들고 state나 props가 갱신되면 `render()`함수는 새로운 리액트 엘리먼트 트리를 반환하게 된다.
  그렇다면… n개의 엘리먼트가 있는 트리를 다른 트리로 변환하는 알고리즘의 복잡도는??
  - 정답
    최소 O(n^3)을 가지게 된다.
    - 이유가 궁금하신 분들을 위해…
      트리의 모든 노드를 서로 비교해야 하는데 만약 트리에 `n`개의 노드가 있다면 노드들을 비교하는데 `O(n^2)`의 시간이 걸린다. + 트리는 재귀적인 구조이기 때문에 최종적으로 `O(n^3)` 이라고는 하는데 n개의 엘리먼트가 있는 트리를 다른 트리로 변환하는 알고리즘의 복잡도는 **최소 O(n^3)** 이라고 그냥 그렇게 알려져있다네요
      암튼 이는 너무 비싼 연산이므로 리액트에서는 두 가지 가정을 통해 O(n)의 복잡도를 가지는 휴리스틱 알고리즘을 구현했다.
      **Reconciliation 특징**
  - 컴포넌트 유형이 다르면 비교(diff)를 진행하지 않고 트리 자체를 새로운 트리로 대체한다.
  - 리스트의 diff는 key를 기준으로 수행된다. 이때 key는 안정적이고 예측 가능하며 유니크한 값이어야 한다.
- **엘리먼트 타입 비교 방식**
  리액트에서 두 개의 트리를 비교하는 과정은

  1. 루트 엘리먼트부터 시작한다.
  2. 엘리먼트의 타입에 따라 처리 방식이 달라진다.

  ### **엘리먼트 타입이 다른 경우**

  ```jsx
  <div>
    <Element /> // LagacyElement
  </div>

  <span>
    <Element /> // NewElement
  </span>
  ```

  - **처리 방식 :** 두 트리의 루트 엘리먼트 타입이 다르면 (`div` vs `span`) 리액트는 기존 트리를 그대로 두지 않고 새로 마운트한다.
    ⇒ `div` 안에 있던 `Element`가 변하지 않았어도 `span` 아래에 새로운 `Element`가 마운트된다.
  - **결과** : 부모 엘리먼트의 타입이 변경되면 전체 트리가 리렌더링 된다는 뜻이 이거다~~

  ### **엘리먼트 타입이 같은 경우**

  ```jsx
  <div className="before" title="stuff" />

  <div className="after" title="stuff" />
  ```

  - **처리 방식** : 두 트리의 루트 엘리먼트가 같은 타입(`div`)인 경우, 리액트는 동일한 속성은 유지하고, 변경된 속성(`className` 등)만 업데이트한다.
    이후 리액트는 자식 노드들을 재귀적으로 처리합니다. 이게 먼소리냐면~~<,,

  ### **자식 노드의 재귀적 처리**

  - 리액트는 자식 리스트를 동시에 순회하며 차이점이 있으면 변경한다. 이 과정에서 공통된 부분이 있어도 리스트의 맨 앞에 새로운 엘리먼트가 추가되면 성능이 저하될 수 있다.
  - **성능 저하 예시**

    ```jsx
    <ul>
      <li>Duke</li>
      <li>Villanova</li>
    </ul>

    <ul>
      <li>Connecticut</li>
      <li>Duke</li>
      <li>Villanova</li>
    </ul>
    ```

    여기서 첫 번째 리스트와 두 번째 리스트의 자식 순서가 달라지면 리액트는 모든 자식을 변경하게 되어 성능이 저하된다!

  ### **Key 속성을 통한 성능 최적화**

  ```jsx
  <ul>
    <li key="2015">Duke</li>
    <li key="2016">Villanova</li>
  </ul>

  <ul>
    <li key="2014">Connecticut</li>
    <li key="2015">Duke</li>
    <li key="2016">Villanova</li>
  </ul>
  ```

  - **처리 방식 :** 리액트는 `key` 속성을 사용해 리스트의 자식 엘리먼트들을 추적한다. `key`가 있으면 리액트는 각 엘리먼트를 고유하게 식별해 불필요한 업뎃을 줄임!

### 리액트가 가상 DOM을 선택한 이유

브라우저의 렌더링 과정은 크게 다음과 같은 단계로 이루어진다.

**DOM 트리 생성 ⇒ CSSOM 생성 ⇒ Render 트리 생성 ⇒ Layout ⇒ Paint**

1. **성능 문제**

   이 과정에서 가장 많은 브라우저 리소스가 소모되는 부분은 **Layout**과 **Paint이다.**

   그리고 이 두 과정은 Render 트리가 변경될때마다 재실행된다.

   우리가 흔히 말하는 브라우저 성능 최적화 == Render 트리 변경의 최소화!

2. **리액트의 접근 방식**

   리액트는 가상 DOM과 Reconciliation(재조정)을 통해 변경사항만을 실제 DOM에 적용함으로 Render 트리 변경을 최소화한다. == 브라우저가 해야할 할 일을 덜어줌으로 성능을 최적화한다.

3. **한계와 리액트 파이버**

   그러나 리액트 팀은 가상 DOM과 재조정만으로 해결할 수 없는 성능 문제에 직면하는데,,,

   이를 해결하기 위해, **React 16**에서 리액트 파이버(Fiber)를 도입한다!

### 리액트 팀은 왜 파이버를 도입했을까?

**Stack Reconciler: React 16 이전의 렌더링 방식**

React 16 이전의 Reconciler 알고리즘은 **Stack Reconciler**라고 불렸다. 이 알고리즘은 이름에서 알 수 있듯이 **스택 구조**를 사용해 렌더링 작업을 처리했다!

**Stack Reconciler의 동작 방식**

1. **스택 기반 작업 처리**: 렌더링에 필요한 모든 작업이 하나의 스택에 쌓인다. 이 작업들은 동기적으로 처리되며 스택이 빌 때까지 중단되지 않는다.
2. **렌더링 과정**
   - DOM 트리의 루트 엘리먼트부터 시작해서 자식 엘리먼트(컴포넌트)까지 재귀적으로 모든 컴포넌트의 `.render()`를 호출한다.
   - Virtual DOM에서 변경 사항을 확인하고, 업데이트가 필요한 컴포넌트를 파악한다.
   - 파악된 컴포넌트와 그 자식 컴포넌트들이 모두 업데이트가 필요한지 확인한 후 이를 순차적으로 업데이트 한다.
3. **하지만 문제점…**
   - 스택 기반 렌더링 과정 예시를 보자면
     ```jsx
     function Stack() {
       return (
         <Parent>
           <ChildA />
           <ChildB />
           <ChildC />
         </Parent>
       );
     }
     ```
     밑에 코드를 실행하면 사진처럼 동작하게 되는데
     ![스크린샷 2024-08-14 오전 3.12.35.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/994a9445-7449-42ce-b9f7-46457378580d/f896648c-7751-4144-9269-0c90e07ca697/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-08-14_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_3.12.35.png)
     한 번 업데이트가 진행되면 깊은 콜 스택을 만들어 동기로 진행
     ⇒ 다른 작업이 우선시 되어 수행되고 싶어도 중단할 수 없는 문제가 발생!

**프레임 드랍과 버벅거림 문제**

- 프레임 드랍 : 화면이 부드럽게 렌더링되지 못하고 끊기는 현상이다. 일반적으로, 부드러운 화면을 위해서는 초당 30프레임(FPS) 이상이 필요하며 최신 모니터들은 60FPS를 기본으로 지원한다.
- 리액트 작업이 16.67ms 이내에 완료되지 않으면, 프레임 드랍이 발생해 화면이 버벅거리게 되는데…
- reconciliation 알고리즘이 업데이트가 있을 때마다 **React 내 트리를 다 순회하고 렌더링을 하는데 16ms가 넘어버린다면?** 자연스럽게 프레임이 드랍되고 애니메이션이 버벅거리는 화면이 나타남!
  https://claudiopro.github.io/react-fiber-vs-stack-demo/stack.html

**⇒ 따라서 파이버를 도입하게 되었다!!!**

**파이버의 주 역할**

- **연산 중단 및 재개**: 긴 작업을 중단하고 필요 시 다시 시작할 수 있다.
- **우선순위 부여**: 작업에 따라 우선순위를 부여하여 중요한 작업을 먼저 처리할 수 있다.
- **연산 재사용**: 이미 완료된 연산을 재사용하여 효율성을 높인다.
- **불필요한 연산 취소**: 필요 없어진 연산을 중간에 취소할 수 있다.

정리하자면 이전의 Stack Reconciler와 다르게

1. 재조정을 렌더링과 동시에 수행하지 않음
2. 각 작업의 우선순위에 따라 점진적으로 처리함

   ⇒ 이를 **증분 렌더링**이라고 한다! 작업을 작은 단위로 나누어 순차적으로 가상 DOM을 업데이트하는 방식이다. 이로 인해 렌더링 단계와 커밋 단계가 분리되었다.

- **렌더링 및 커밋 단계 요약**
  1. **렌더링 단계 1**: 리액트는 UI에 반영될 모든 변경 사항을 리스트로 저장한다. 이 단계에서 작업을 언제든지 중지할 수 있다.
  2. **렌더링 단계 2**: 변경사항 리스트가 완성되면 리액트는 다음 단계에서 실행할 변경 사항을 예약한다.
  3. **커밋 단계 1**: 예약된 변경 사항 중 특정 사항만을 렌더링하도록 지정할 수 있다.
  4. **커밋 단계 2**: 커밋이 시작되면, 변경 사항이 브라우저에 렌더링된다.
  <aside>
  ✨ **여기서 중요한 점은!!! 
  렌더링 단계는 언제든 중지할 수 있지만, 커밋 단계는 시작되면 멈출 수 없습니다는 점**
    </aside>

### 리액트 파이버의 작동 원리 (증분 렌더링의 원리)

**Fiber**는 렌더링 작업을 관리하는 JavaScript 객체로 **Fiber Reconciler**에 의해 관리된다.

Fiber를 노드로 구성된 트리를 **Fiber 트리**라고 하며 이를 웹 환경에서는 흔히 **가상 DOM**이라고 부르지만 Fiber와 가상 DOM은 정확히 동일한 개념은 아니다!

**Fiber 트리의 구조 및 각 필드의 역할**

- **type**: 생성된 함수형 컴포넌트나 클래스형 컴포넌트의 타입을 나타낸다.
  리액트 요소의 타입과 비슷!
- **key**: 리스트를 렌더링할 때 각 요소를 고유하게 판단한다.
  ⇒ type과 key는 Fiber가 재사용될 수 있는지 판단
- **child**: 해당 컴포넌트의 가장 왼쪽에 있는 자식 노드를 참조한다. (Fiber의 첫 번째 자식 Fiber를 가르킴)
  ```jsx
  function Parent() {
    return <Child />;
  }
  ```
- **sibling**: 해당 컴포넌트의 형제 노드를 참조한다. 렌더 메서드가 여러 자식을 반환하는 경우!
  ```jsx
  function Parent() {
    return [<Child1 />, <Child2 />];
  }
  ```
  ⇒ child와 sibling은 Fiber 트리의 재귀적 구조를 묘사
- **return**: Fiber가 처리된 후 돌아갈(parent로 돌아가는) Fiber를 가리킨다. 부모 Fiber!

**Left-Child Right-Sibling Tree**

- 파이버는 모든 자식 노드를 포함하는 것이 아닌 가장 왼쪽에 있는 자식 하나만을 참조하는데 이를 통해 트리 구조가 연결 리스트와 유사하게 구성된다고 한다.
- 이 방식은 모든 자식 노드를 일괄적으로 관리하는 대신, 첫 번째 자식 노드와 형제 노드들 간의 관계를 관리함으로써 메모리 사용을 최적화하고, 재귀적인 구조로 노드를 쉽게 탐색할 수 있게 한다.

### 리액트 파이버 트리

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/994a9445-7449-42ce-b9f7-46457378580d/25ff2f8d-0e69-4450-89b5-a29b82790364/Untitled.png)

리액트 파이버 트리는 **두 개**의 트리로 구성되어 있다.

1. **`current` 트리**: 현재 렌더링된 UI의 상태를 담고 있는 트리!

   ⇒ 사용자가 현재 보고 있는 UI를 반영하고 있다.

2. **`workInProgress` 트리**: 업데이트가 진행 중인 상태를 나타내는 트리!

   ⇒ 새로운 데이터나 상태 변화에 따라 변경된 UI를 준비하는 역할을 한다.

- 두 개의 트리가 전환되는 기법, **더블 버퍼링(Double Buffering)**
  - 리액트에서 `current` 트리와 `workInProgress` 트리를 전환할 때 **더블 버퍼링** 기술을 사용한다.
  - **더블 버퍼링 :** 컴퓨터 그래픽스에서 사용하는 기법, 보이지 않는 곳에서 다음에 그려야 할 그림을 준비한 후 그림이 완성되면 현재 상태를 새 그림으로 교체하는 방식! ⇒ 가상돔 st
  - 리액트에서는 이 작업이 **커밋 단계**에서 수행된다. 작업이 완료되면 `workInProgress` 트리를 `current` 트리로 바꿔치기 하여 새로운 UI가 사용자에게 보이게 된다.

### 파이버 동작 방식

1. **업데이트 발생**: `setState` 또는 다른 상태 변경으로 인해 업데이트가 발생하면 리액트는 `workInProgress` 트리를 빌드하기 시작한다. 이 단계에서는 상태가 변경된 부분을 반영하여 새로운 트리를 준비한다. (렌더 단계)
2. **커밋 단계**: `workInProgress` 트리가 준비되면 이를 실제 DOM이 화면에 반영한다. 이때 더블 버퍼링을 통해 `workInProgress` 트리가 `current` 트리로 전환되며, 새롭게 렌더링된 UI가 사용자에게 보이게 된다.

<aside>
✨ 중요한 점은… 리액트 파이버와 DOM/네이티브 렌더링은 별개다!

⇒ 계산하는 과정이 파이버고 실제로 그리는 ui부분은 **따로**라는 소리

- **리액트 파이버**: 컴포넌트 상태와 트리 구조를 관리하고, 업데이트가 필요한 부분을 계산하는 역할.
- **렌더링 엔진**: 파이버가 계산한 결과를 실제 화면에 반영하는 역할.
</aside>

### 리액트 파이버의 도입으로 얻은 효과

1. **성능 향상!**

   재조정하는 동안 다른 작업을 중지하지 않기 때문에 리액트는 필요할 때마다 작업을 일시 중지하거나 렌더링을 시작할 수 있게 됐다.

2. 훨씬 깔끔한 방식으로 **오류**를 처리할 수 있게 됨!

   자바스크립트 런타임 오류가 발생할 때마다 흰색 화면을 표시하는 대신 Error Boundary를 설정하여 문제가 발생할 경우 백업 화면을 표시할 수 있게 됐다.

<aside>
✨ **리액트 파이버의 도입으로 Error Boundary, Suspense, React.Lazy, Fragment 그리고 Concurrency Mode가 가능해졌다**

</aside>

### Key Props와 리액트 파이버의 활용

**Key Props의 중요성**

리액트에서 `key`는 컴포넌트 리스트에서 각 요소를 고유하게 식별하는 역할을 한다.

`key`가 없거나 중복된 경우, 리액트는 요소의 변화를 정확히 추적하지 못해 성능이 저하되고 예상치 못한 렌더링 결과가 나타날 수 있다.

component를 map으로 만들때 key를 꼭 넣어달라는 error에

→ key를 index 값으로 줬었는데

```jsx
const items = ["A", "B", "C"];

function App() {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item}</li>
      ))}
    </ul>
  );
}
```

이런 방법은 리스트의 순서가 변경될 때 문제가 발생할 수 있다!

리스트에 새로운 항목이 중간에 추가되면 기존 항목의 `key` 값도 변경되므로 리액트는 이를 새로운 컴포넌트로 간주하고 불필요하게 다시 렌더링하는 문제 흑흐ㄱ

이 문제를 파이버 트리로 해결할 수 있는데…

**리액트 파이버의 key와 문제 해결 방법**

- **리액트 파이버 트리는 key를 이용한다!**
  위에서 설명했던 리액트 파이버 트리를 다시 떠올려보자! 리액트 파이버 트리는 총 2개가 있으며 변경 사항이 있을 경우를 리액트는 키를 이용해서 구별한다. key가 존재한다면 두 트리에 있는 컴포넌트는 key 기준으로 구별한다. 만약 없다면 내부 프로퍼티인 siblings index를 기준으로 판단한다.

위 문제는 중복된 키를 가진 컴포넌트가 존재했기에 발생한 문제였고 이는 리액트가 가상 DOM을 업데이트할떄 사용하는 리엑트 파이버 방식에 따른 것!

**문제 해결 방법**

1. **컴포넌트 언마운트**: `key` 값을 변경하여 컴포넌트를 강제로 언마운트하고 다시 마운트시켜 중복된 컴포넌트를 제거한다.
2. **중복된 ID 필터링**: 상태에 중복된 데이터를 추가하지 않도록 필터링하여 중복된 컴포넌트가 생성되지 않게 한다.

### 리액트는 Value UI 라이브러리이다

리액트는 UI를 값으로 관리하여 변수에 UI 관련 값을 보관하고, 이를 관리하고 표현하는 라이브러리이다.

- **Virtual DOM 용어 폐기**: 리액트 개발자인 Dan Abramov는 "Virtual DOM"이라는 용어를 폐기할 것을 권장했는데 그 이유는 Virtual DOM이 리액트의 동작 방식을 오해하게 만들 수 있기 때문이라고 한다. 대신, UI를 값으로 다룬다는 의미에서 "**Value UI**"라는 표현을 사용하길 권고한다고 한다.
- **파이버의 역할**: 리액트 파이버는 컴포넌트가 처음 마운트될 때 생성되며, 이후 가급적 재사용된다. 이는 리액트가 UI를 효율적으로 관리하고 업데이트하기 위해 설계된 방식이다.

---

# 2.3 **클래스 컴포넌트와 함수 컴포넌트**

### **클래스 컴포넌트, 함수 컴포넌트**

리액트에서는 클래스와 함수를 기반으로 컴포넌트를 만들 수 있다.

클래스 컴포넌트는 사실상 레거시인데, 다만 생명주기 메소드 중 함수 컴포넌트로 구현 불가능한 것이 필요할 경우에는 불가피하게 사용한다.

ex) **Error Boundaries와 관련된 메서드**

### **클래스 컴포넌트의 한계 → 함수 컴포넌트의 장점**

- 데이터 흐름을 추적하기 어렵다. → this와 관련된 혼란을 줄이고 Hooks API를 통해 일관된 흐름 유지.
- 로직 재사용이 어렵다. → 커스텀 훅으로 로직 재사용 가능.
- 클래스의 한계로 번들 크기 증가 → 사용하지 않는 함수 등을 트리쉐이킹 가능.
- instance 내부에 state를 관리하여 핫 리로딩에 불리하다 → 리액트 아키텍처 내부 클로저에 state 저장하여 핫 리로딩에 최적화 되어있다.

### **클래스 컴포넌트 vs 함수 컴포넌트**

- 클래스 컴포넌트는 props, state의 값을 항상 this로부터 가져온다!
  - mutable하여 렌더링 이후에 변경된 값을 읽을 수 있음
- 함수 컴포넌트는 props를 함수의 인자로 받고, 컴포넌트가 그 값을 변경할 수 없다.
  - 렌더링이 일어난 순간의 값을 참조

---

# **2.4 렌더링은 어떻게 일어나는가?**

위에서 한 번에 설명함!

**리액트의 렌더링이 일어나는 이유만 정리하자면 이게 전부라고 함**

1. **최초 렌더링**: 사용자가 앱에 처음 진입할 때.
2. **리렌더링**:
   - `setState` 호출 시.
   - `useReducer`의 `dispatch` 실행 시.
   - 클래스 컴포넌트에서 `forceUpdate` 사용 시.
   - 컴포넌트의 `key` props 변경 시.
   - `props` 변경 시.
   - 부모 컴포넌트가 렌더링될 때 자식 컴포넌트도 함께 리렌더링됨.

---

# **2.5 컴포넌트와 함수의 무거운 연산을 기억해 두는 메모이제이션**

리액트에서 React.memo, useMemo, useCallback은 각각 컴포넌트, 값, 함수를 메모이제이션하는 기능이다.

메모이제이션은 성능 최적화를 위한 기술 but 비용이 들기 때문에 신중해야 한다.

### **2.5.1 주장1 - 메모이제이션은 꼭 필요한 곳에만**

메모이제이션은 값을 어딘가에 저장해두어야 하기 때문에 리소스가 추가로 들고 성능에 영향이 미미한 작업까지 메모이제이션 할 필요는 없다는 관점!

- **1. 메모이제이션 비용**
  - 메모이제이션도 비용이 든다. 💸
    - 값을 비교하고 렌더링 또는 재계산이 필요한지 확인하는 작업
    - 이전에 결과물을 저장해 두었다가 다시 꺼내와야 함
    - 이 비용이 리렌더링 비용보다 저렴하다고 할 수 있을까? 👉 상황에 따라 다를 것
  - 섣부른 최적화는 경계해야 한다.
  - 모조리 메모이제이션이 좋았더라면 **진작에 리액트에서 모든 컴포넌트를 PureComponent로 만들거나 memo로 감싸두는 작업을 했을 것**
- **2. 기억한다는 것이 보장되지 않음**
  - 메모이제이션을 한다고 해서 값을 기억하는 것을 항상 보장할 수는 없다.
    - 가능한 한 오랫동안 캐시 결과를 저장하려 하겠지만 메모리가 해제되는 등의 이유 캐시가 무효화되는 경우도 있을 수도??

### **2.5.2 주장2 - 렌더링 과정의 비용은 비싸다, 모조리 메모이제이션해 버리자**

모든 것을 메모이제이션 하는 비용보다 필요한 곳에 메모이제이션이 적용되지 않았을 때 드는 비용이 훨씬 크므로 메모이제이션을 적용할 곳을 선별하지 말고 모두 메모이제이션하자는 관점!

- **1. 잘못된 최적화로 인해 역으로 비용을 지불해야 할 수도**
  - **잘못된 최적화 비용**: 렌더링 비용이 적거나 빈번히 일어나지 않는 컴포넌트에 `memo`를 사용하면 props의 얕은 비교 비용을 지불하게 된다.
  - 🤔 렌더링 결과물도 저장해둬야 하지 않나요?
    - 어차피 리액트는 원래 이전 렌더링 결과 저장하고 있음! (리액트의 재조정 알고리즘을 위해)
    - 그래서 props에 대한 얕은 비교 비용만 지불하면 된다.
  - **memo를 사용하지 않았을 때의 비용**: 렌더링, 복잡한 로직의 재실행, 자식 컴포넌트의 반복된 재실행, 리액트 트리 비교가 발생할 수 있다
  - 결론은 memo를 하지 않았을 때의 **잠재적 비용이 더 크다.**
- **2. useMemo와 useCallback은 언제 유용하지?**
  - 리렌더링이 발생할 때 메모이제이션을 해두지 않으면 모든 객체가 재생성되어 참조가 달라진다.
  - 이 참조에 대한 값을 어디서든 안 쓰면 상관 없지만!
  - 이 값이 의존성 배열 쓰인다면... 내부의 값은 같지만 계속 호출되겠쥥
  - 참조 투명성: 객체를 useMemo로 감싸두면 값이 변경되지 않는 한 같은 결과물을 가질 수 있어서 **사용하는 쪽에서도 변하지 않는 고정된 값을 사용할 수 있다는 믿음**을 줄 수 있다.

⇒ 결론 : 알아서 잘 하자… 판단을 잘 하자…
