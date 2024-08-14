## 튜링 완전이란?

**튜링 완전성(Turing Completeness)**

튜링 완전성은 어떤 프로그래밍 언어나 계산 모델이 튜링 기계와 동일한 계산 능력을 가진다는 의미!

**타입스크립트의 타입 시스템과 튜링 완전성**

타입스크립트의 타입 시스템은 튜링 완전함이 증명되었다고 한다. ts에서는 타입 수준에서 조건문, 반복문, 재귀 등의 논리를 구현할 수 있기 때문에 그렇다고 하네요…

**주요 특징**

1. **조건문**: 타입스크립트의 타입 시스템에서는 조건부 타입을 사용하여 조건문을 구현할 수 있습니다.
2. **반복문 및 재귀**: 재귀적인 타입 정의를 통해 반복문과 같은 효과를 구현할 수 있습니다.
3. **타입 연산**: 타입스크립트에서는 다양한 타입 연산을 통해 복잡한 타입을 정의하고 조작할 수 있습니다.

## 클로저는 일반 함수랑 형태가 어떻게 다른가?

질문 이해가 잘 안됐는데 이게 맞나요?? ㅜㅜ

**클로저의 프로토타입은 일반 함수와 동일하다!**

클로저도 함수 객체이며, `Function` 프로토타입을 상속받는다.

## 싱글톤 패턴이란?

싱글톤 패턴은 특정 인스턴스가 오직 하나만 존재하도록 보장!

애플리케이션에서 공통된 자원을 관리하거나 전역 상태를 유지하기 위해 사용되기 때문에 정적 메서드와 비교 가능!

**정적 메서드와 싱글톤 패턴 비교**

| 특성        | 싱글톤 패턴 (Singleton Pattern)                                         | 정적 메서드 (Static Method)                                     |
| ----------- | ----------------------------------------------------------------------- | --------------------------------------------------------------- |
| 목적        | 특정 클래스의 인스턴스를 하나만 생성하여 전역적으로 관리                | 인스턴스를 생성하지 않고 클래스 레벨에서 유틸리티 함수 제공     |
| 사용 사례   | 설정 관리, 로그 기록, 데이터베이스 연결 등 전역 상태 관리 → 상태 관련!! | 수학 계산 함수, 데이터 변환 함수 등 인스턴스와 무관한 기능 제공 |
| 인스턴스    | 하나의 인스턴스만 존재                                                  | 인스턴스 생성 없이 클래스 이름으로 접근 가능                    |
| 호출 방식   | Singleton.getInstance()                                                 | ClassName.methodName()                                          |
| 상태 관리   | 인스턴스를 통해 상태를 유지하거나 관리                                  | 상태와 무관한 함수 제공                                         |
| 초기화 시점 | 필요할 때 지연 초기화 가능                                              | 클래스 로드 시점에 정적 메서드 사용 가능                        |

https://chaeoff.medium.com/singleton-pattern-싱글톤-패턴-1131fae052f5

## 익명 함수와 같은 것들은 호이스팅이 되는가?

**익명 함수 자체는 호이스팅되지 않으며 변수의 호이스팅 규칙을 따른다!**

**화살표 함수**도 함수 표현식의 일종이기 때문에 똑같이 변수의 호이스팅이 작용함.

```jsx
console.log(typeof hello); // "undefined"
hello(); // error

var hello = () => {
  console.log("hello");
};
```

## 객체끼리 더하면 어떻게 될까?

**객체끼리 더하면 각 객체의 `toString` 메서드가 호출되어 문자열 결합이 된다!**

![스크린샷 2024-08-08 오후 5.10.13.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/994a9445-7449-42ce-b9f7-46457378580d/3de92944-4366-4d0b-9b9f-14ddbeff0369/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-08-08_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.10.13.png)

## **element도 객체인가?**

**element도 객체이다!**

https://developer.mozilla.org/en-US/docs/Web/API/Element

**일반 객체 예시**

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

- `hello`와 `hi`는 서로 다른 객체이므로 `===` 연산자로 비교하면 `false`가 된당
- `greet` 프로퍼티의 값은 같기 때문에 `hello.greet === hi.greet`는 `true`가 되고!

**element 예시**

```jsx
<!DOCTYPE html>
<html>
<body>

<p id="element1">Hello</p>
<p id="element2">Hello</p>

<script>
  const element1 = document.getElementById('element1');
  const element2 = document.getElementById('element2');

  console.log(element1 === element2); // false
  console.log(element1.innerHTML === element2.innerHTML); // true
</script>

</body>
</html>
```

- `element1`과 `element2`는 서로 다른 HTML 요소 객체이므로 `===` 연산자로 비교하면 `false`가 된다.
- 두 요소의 `innerHTML` 값은 같기 때문에 `element1.innerHTML === element2.innerHTML`은 `true`가 된다.
  ⇒ 일반 객체 비교과 똑같다!
