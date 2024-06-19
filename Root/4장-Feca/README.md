# 04. 서버 사이드 렌더링

> 이 글은 노션을 기반으로 작성되었습니다.  
> https://odd-blinker-340.notion.site/04-985e340b9bc4414e8943735a21b5ee1a

서버 사이드 렌더링이 무엇이고, 왜 중요한 기술이 되었는지,

리액트에서 서버 사이드 렌더링을 어떻게 구현하는지,

SSR 라이브러리인 Next.js 사용법을 간단하게 알아보겠습니다.

## SPA (Single Page Application)

말 그대로 서버로 부터 받아온 **‘HTML 파일 하나’** 로

동적인 페이지들을 구성하는 것을 말합니다.

SPA의 정말 간단한 예시로,

아래처럼 서버로부터 콘텐츠가 없는 빈 HTML 파일을 받아옵니다.

```html
...

<body>
  <div id="root">
    <!-- 비어있음! -->
  </div>

  <script ... />
</body>
```

그리고 동적으로 콘텐츠를 생성해줍니다.

```tsx
window.onload = () => {
  // 렌더링할 루트 경로를 가져오고
  const root = document.getElementById("root");

  // 거기에 뷰를 렌더링합니다!
  root.innerHTML = getViewOfRoute("/");
};
```

필요하다면 라우팅 처리도 해줍니다.

```tsx
// 이전 경로에 대한 뷰를 지우고,
removeView(previousRoute);

// 현재 경로에 대한 뷰를 렌더링합니다!
renderView(newRoute);

// 새로고침 없이 URL만 새롭게 바꿔줍니다.
window.history.pushState(null, "New Page", `${ROOT_PATH}/${newRoute}`);
```

위 내용이 보통 SPA를 설명할 때 사용되는 내용이지만,

사실 SPA의 범위는 이 정도로 국한되는 것이 아닙니다.

### SPA는 굳이 비어있는 HTML 을 받아올 필요가 없다.

SPA의 핵심은,

하나의 HTML로 **‘새로고침 없이’,**

동적으로 페이지들을 렌더링하는 것 입니다.

그러면

> 무조건 비어있는 HTML을 받아와야 하는가?

에 대한 의문이 들 수 있습니다.

사실 아래와 같이 서버로부터 받아온 HTML에

미리 렌더링된 정적인 콘텐츠가 존재해도 아무런 상관이 없습니다.

```tsx
...
<div id="root">
	<h1>List</h1>
	<ul>
		...
	</ul>
</div>
```

그 이후는 똑같이 라우팅처리를 해주고, 필요한 부분에 이벤트를 달아주고,

동적으로 콘텐츠를 렌더링해주면 되는거죠.

### 그러면 SPA 구현을 위해 어떤 방식을 사용해야 하는가?

여기서 알 수 있는 것은

> 일단 HTML을 받고, 동적으로 콘텐츠를 렌더링하는 것이든,

> 서버로부터 아예 데이터까지 박혀있는 정적인 콘텐츠를 받아오든,

그저 SPA를 구현하기 위한 ‘방법론’ 들에 지나지 않는다는 점 입니다.

그래서 사람들은 SPA를 구현하는 방법론들에 이름을 붙입니다.

각각 클라이언트 사이드 렌더링(CSR) 과 서버 사이드 렌더링(SSR) 입니다.

```html
<!-- CSR -->
<div id="root"></div>
```

```html
<!-- SSR -->
<div id="root">
  <h1>List</h1>
  <ul>
    ...
  </ul>
</div>
```

## SPA, MPA, CSR, SSR, SSG 그 경계

이 용어들을 간단하게 짚고 넘어가겠습니다.

### SPA ( Single Page Application )

SPA는 서버로부터 첫 페이지만 받아오고,

이후는 동적으로 페이지를 구성하는 웹 어플리케이션입니다.

따라서 데이터를 조회하고 수정하는데 페이지가 새로고침 되지 않습니다.

### MPA ( Multi Page Application )

MPA는 서버로부터 완전한 페이지를 받아오고,

이후에 데이터를 조회하고 수정하는데 완전히 다른 페이지로 넘어갑니다.

### CSR ( Client Side Rendering )

데이터가 없는 빈 HTML을 받아오고,

데이터는 정적인 파일이 로드된 후에 서버에 요청해서 받아오는 렌더링 방식입니다.

### SSR ( Server Side Rendering )

CSR과 달리 데이터까지 완전히 삽입된 HTML을 받아옵니다.

단, ‘**매 요청마다’** 서버에서 정적인 페이지를 렌더링합니다.

### SSG ( Static Site Generation )

CSR과 달리 데이터까지 완전히 삽입된 HTML을 받아옵니다.

단 빌드 시간에 미리 HTML을 렌더링해두고, 요청이 들어오면 렌더링된 HTML을 넘겨줍니다.

### SPA는 CSR과 뭔 차이죠?

얼핏보면 같아보이는데,

애초에 이 둘을 같은 선상에 두고 비교할 수 없습니다.

SPA와 MPA은 ‘페이지를 하나만 쓸까?’ 가 주 토픽이고,

CSR과 SSR은 ‘렌더링을 어디서?’ 가 주 토픽입니다.

따라서 SPA를 구현하기 위해 보통 CSR를 채택한다고 보는게 맞고,

MPA를 구현하기 위해 보통 SSR를 채택한다고 보는게 맞습니다.

### 그러면 ‘SSR만으로’ SPA를 구현할 수 있나요?

안됩니다.

SPA는 동적으로 콘텐츠를 렌더링해서 새로고침을 최소화하는 웹 어플리케이션인데

SSR만으로는 동적으로 새로고침 없이 콘텐츠를 바꿀 수 있는 방법이 없습니다.

그 반대인 ‘CSR만으로 MPA를 구현할 수 있나요?’ 도 동일하게 불가능합니다.

하지만 처음에 SSR를 사용하고, 그 이후에 적절히 CSR를 하면 SPA를 유지할 수 있겠죠.

이 방식을 Next.js에서 채택하고, SSR를 적용한 대부분의 SPA가 이 방식을 고수합니다.

## SSG와 SSR ??

뜬금 없지만 은근 중요한 내용인

SSG와 SSR에 대해 좀 더 자세하게 알아보겠습니다.

### Pre-Rendering

[Next.js 공식 문서의 렌더링 섹션](https://nextjs.org/docs/pages/building-your-application/rendering)에서는 `pre-rendering` 이라는 방법에 대해 설명합니다.

간단하게 설명하자면, 각 페이지들을 사전에 미리 HTML 문서로 생성하여 가지고 있다는 소리입니다.

![Untitled](04%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3%20%E1%84%85%E1%85%A6%E1%86%AB%E1%84%83%E1%85%A5%E1%84%85%E1%85%B5%E1%86%BC%20985e340b9bc4414e8943735a21b5ee1a/Untitled.png)

![Untitled](04%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3%20%E1%84%85%E1%85%A6%E1%86%AB%E1%84%83%E1%85%A5%E1%84%85%E1%85%B5%E1%86%BC%20985e340b9bc4414e8943735a21b5ee1a/Untitled%201.png)

근데 여기서 이 pre-rendering 을 하는 방법을 두 가지를 소개합니다.

- Static Site Generation (Site Generation)
  - ⇒ Next.js 에서는 이 방식을 추천하고, 기본 방식으로 진행합니다.
- Server Side Rendering

### Static Site Generation

![Untitled](04%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3%20%E1%84%85%E1%85%A6%E1%86%AB%E1%84%83%E1%85%A5%E1%84%85%E1%85%B5%E1%86%BC%20985e340b9bc4414e8943735a21b5ee1a/Untitled%202.png)

빌드 타임에 미리 HTML을 만들어 놓고, 요청시마다 그걸 줍니다.

### Server Side Rendering

![Untitled](04%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3%20%E1%84%85%E1%85%A6%E1%86%AB%E1%84%83%E1%85%A5%E1%84%85%E1%85%B5%E1%86%BC%20985e340b9bc4414e8943735a21b5ee1a/Untitled%203.png)

요청시마다 HTML을 렌더링하고, 렌더링된 HTML을 줍니다.

### 언제 뭘 써야할까?

당연히 성능을 위해서는 대부분의 상황에 SSG를 사용하면 좋을 것 같습니다.

하지만 빌드 타임에 HTML을 만들기 때문에, 데이터가 최신이라는 보장을 할 수 없겠죠.

따라서 Next.js에서는 다음과 같은 경우에 SSG를 사용하라고 권고하고 있습니다.

1. 퍼포먼스가 중요할 때
2. 블로그, 고정된 제품의 목록, 포폴(마케팅) 등 각 요청에 대해 동일한 문서를 반환하면 될 때

그리고 다음과 같은 경우에 SSR를 사용하라고 권고하고 있습니다.

1. 데이터가 항상 최신을 유지해야 할 때
2. SNS 처럼 각 요청에 대해 다른 문서를 반환해야할 때

앞으로 말하는 SSR에는 SSG에 대한 내용 또한 포함된다고 생각하면 됩니다.

## SPA에 SSR(+ SSG)를 껴넣으면 장점?

즉, 최초 렌더링을 ‘서버’에서 수행하면 어떤 장점이 있을까에 대한 내용입니다.

### 1. 최초 페이지 진입이 보통 빠르다.

웹 페이지의 성능을 측정하는 항목 중

중요하게 여겨지는 항목에 `FCP`( First Contentful Paint ) 가 있습니다.

이는 브라우저가 다음 항목들을 처음 렌더링한 시점까지의 시간을 나타낸 것입니다.

- 텍스트 ( 웹 폰트 로딩과 크게 상관없음 )
- 이미지 ( 배경 이미지 포함 )
- 비디오
- 뭔가가 그려진 Canvas
- 뭔가가 그려진 SVG
- 단, iframe의 콘텐츠는 제외

이런 콘텐츠들이 서버에 렌더링되어있다면

클라이언트 측에서는 필요한 리소스만 받아오면 되므로 FCP가 굉장히 단축될 수 있겠죠.

![SSR 방식을 적용한 페이지는 FCP가 굉장히 빠릅니다](04%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3%20%E1%84%85%E1%85%A6%E1%86%AB%E1%84%83%E1%85%A5%E1%84%85%E1%85%B5%E1%86%BC%20985e340b9bc4414e8943735a21b5ee1a/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-19_03.28.47.png)

SSR 방식을 적용한 페이지는 FCP가 굉장히 빠릅니다

요즘 와서는 대부분의 경우에는 포함이 안되지만,

서버에 할당된 리소스가 적은 경우, 오히려 느려질 수 있다는 단점이 있긴 합니다.

### 2. 검색 엔진 최적화가 쉽다.

검색 엔진 크롤러는 JS코드를 실행하지 않습니다.

따라서 정적인 콘텐츠만 분석하는데요.

그렇기에 CSR만으로 구현된 SPA는 검색 엔진 최적화가 어렵다는 특징이 있습니다.

하지만 SSR를 이용하면 정적인 콘텐츠를 서버로부터 받아올 수 있으니 훨씬 유리합니다.

### 3. 보안에 좀 더 유리할 수도 있다는 설

> SSR를 사용하면 사용자에 대한 정보를 세션으로 관리하지만

CSR에서는 브라우저 쿠키나, 로컬 스토리지에 정보를 저장합니다.

따라서 XSS 해킹이나 여타 다른 공격에 대한 취약점을 드러낼 가능성이 적습니다.

>

이런 얘기인데,

솔직히 이거는 웹앱의 보안을 신경쓰지 않고 구현하지 않았을 경우의 얘기라서

SSR의 장점이라고 보기는 힘들어 보이네요.

## React로 SSR에 도달하기

결국 SSR의 궁극적인 목적은 어떤 라이브러리/프레임워크를 쓰건 간에

서버 측에서 정적인 HTML 파일을 pre-rendering 할 수 있으면 된다는 것 입니다.

React의 `react-dom/server` 에서는 작성한 컴포넌트들을 렌더링하고

이를 HTML 문자열로 반환하는 함수들을 제공했습니다.

- renderToString
- renderToStaticMarkup
- renderToNodeStream
- renderToStaticNodeStream

### 정적 문자열로 렌더링하는 방법

`renderToString`과 `renderToStaticMarkup`은

컴포넌트를 렌더링한 결과를 문자열로 리턴해줍니다.

```tsx
import { renderToString, renderToStaticMarkup } from "react-dom/server";

const html = renderToString(<App />);
const staticHTML = renderToStaticMarkup(<App />);
```

```tsx
// renderToString 결과
<div data-reactroot>
	<div>hello</div>
</div>

// renderToStaticMarkup 결과
// 리액트 전용 DOM 속성이 없음
<div>
	<div>hello</div>
</div>
```

### 스트리밍 방식으로 렌더링하는 방법

`renderToNodeStream` 과 `renderToStaticNodeStream` 은

컴포넌트를 렌더링한 결과를 Node.js `Readable Stream` 으로 반환합니다.

- Stream?
  간단하게 말하면 HTML 크기가 정말 클 때 그걸 한 번에 다 가져올려면 쉽지 않다.
  그래서 그거를 chunk 단위로 쪼개서 받는 흐름을 스트림이라고 함. ⇒ 메모리 이득
  Readable Stream은 그냥 단순히 읽는 쪽에서의 스트림을 말함.
  보내는 쪽에서의 스트림은 Writable Stream 이라고 함.

```tsx
// 서버 측 코드

import { renderToNodeStream, renderToStaticNodeStream } from "react-dom/server";

app.use("/", (request, response) => {
  const stream = renderToNodeStream(<Page />);
  stream.pipe(response);
});

app.use("/", (request, response) => {
  const staticStream = renderToStaticNodeStream(<Page />);
  staticStream.pipe(response);
});
```

```tsx
// 클라이언트 측 코드

// 1. 서버에 요청을 보낸다.
const res = await fetch("http://localhost:3000");

// 2. Readable Stream을 받아서 chunk 단위로 출력한다.
for await (const chunk of response.body) {
  const htmlChunk = Buffer.from(chunk).toString();
  console.log(htmlChunk);
}
```

```tsx
// 응답을 chunk 단위로 분리함.

// renderToNodeStream 결과
chunk1 -----------------------
<div data-reactroot>
	<div>hello</div>
	<ul>
------------------------------

chunk2 -----------------------
		<li>data1</li>
		<li>data2</li>
------------------------------

chunk3 -----------------------
	</ul>
</div>
------------------------------

// renderToStaticNodeStream 결과
// 동일하나, 리액트 전용 DOM 속성이 없음
chunk1 -----------------------
<div data-reactroot>
	<div>hello</div>
	<ul>
------------------------------

chunk2 -----------------------
		<li>data1</li>
		<li>data2</li>
------------------------------

chunk3 -----------------------
	</ul>
</div>
------------------------------
```

### 근데 Suspense를 완벽히 지원하지 않습니다.

하지만 이들은 현재 React 버전에서 사용하기에 좀 문제가 많습니다.

|                      | 문제               | hydration 가능?              |
| -------------------- | ------------------ | ---------------------------- |
| renderToString       | Suspense 지원 안함 | hydrateRoot()랑 같이 써야 함 |
| renderToStaticMarkup | Suspense 지원 안함 | x                            |
| renderToNodeStream   | - deprecated 됨    |

- 모든 Suspense가 완료될 때 까지 대기
- 출력이 버퍼링 됨 | hydrateRoot()랑 같이 써야 함 |
  | renderToStaticNodeStream | - 모든 Suspense가 완료될 때 까지 대기
- 출력이 버퍼링 됨 | x |

여기서 ‘출력이 버퍼링 된다’ 는 의미는,

받아 올 데이터를 잘개 쪼개서 가져오는 스트리밍의 이점을 살리지 못하고,

출력을 모아서(버퍼링해서) 한번에 가져온다는 의미입니다.

### 이걸 사용하면 됩니다.

따라서, 최신 React에서는 아래와 같은 방법을 강력하게 권장합니다.

아래 방법들은 Suspense도 지원하며, 출력이 버퍼링되지 않고 스트리밍됩니다.

- renderToReadableStream: `Readable Stream`이 resolved 된 `Promise`를 리턴해줌
  - 대신 이게 Web Stream 에 의존하기 때문에 Node.js 에서는 사용하기 힘듬
- renderToPipeableStream: Node.js 파이프라인으로 리턴해 줌
  - 따라서, 클라이언트 측에서 사용이 불가능함.
  - Node.js에서 필요할 떄 이걸 사용하면 됨.

결국 “뭘 써야하나?” 를 요약하면 아래와 같습니다.

- Suspense 필요없다 (코드가 크지도 않다) ⇒ renderToString
- Suspense 또는 스트리밍이 필요하다
  - 대부분의 경우 ⇒ renderToReadableStream
  - html 코드를 Node 환경에서 써야한다 ⇒ renderToPipeableStream

### Hydration 적용하기

![Untitled](04%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%A5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3%20%E1%84%85%E1%85%A6%E1%86%AB%E1%84%83%E1%85%A5%E1%84%85%E1%85%B5%E1%86%BC%20985e340b9bc4414e8943735a21b5ee1a/Untitled%201.png)

hydration은 렌더링된 컴포넌트들에 로딩된 JS를 적용하는 과정을 말합니다.

이 과정을 수행하지 않으면 `useState`, `useEffect` 등의 코드는 작동하지 않겠죠.

### CSR에서의 hydration

보통 CSR 방식으로 구현된 리액트 프로젝트 내의 `main.jsx` 혹은 `index.jsx` 는

다음과 같이 구현되어 있습니다.

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { App } from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(<App />);
```

위 코드의 `render` 함수를 호출하면, 렌더링과 hydration도 같이 수행됩니다.

### SSR에서의 hydration

이를 간단하게 SSR로 구현하게 된다면

서버측에서는 앞서 소개한 함수들을 이용하여 렌더링만 수행하고,

클라이언트 측에서는 hydration을 진행해주면 됩니다.

```tsx
// 서버 측 코드

app.use("/", (req, res) => {
  const html = renderToString(<App />);
  response.send(html);
});
```

```tsx
// 클라이언트 측 코드

import { hydrateRoot } from "react-dom/client";

hydrateRoot(document.getElementById("root"), <App />);
```

### SSR + Hydration의 문제점

Hydration을 통하여 웹 앱이 사용자와 인터랙션하기 위해서는 두 가지 과정이 필요합니다.

1. 모든 것을 다 불러와야 hydration이 가능합니다.
2. 모든 항목의 hydration이 끝나야 비로소 상호작용이 가능합니다.

그런데 앞서 SSR를 위해 Readable Stream을 사용하게 되면

코드를 chunk 단위로 불러온다고 했는데,

React는 HTML 코드의 일부분만으로는 무엇을 해야 할지 알 수 없어

hydration에서 해당 코드를 삭제하게 됩니다.

그래서 이전 React에서는 모든 코드가 전부 불러와진 이후,

hydration을 진행했습니다.

### Selective Hydration

React 18 버전에서는 `lazy` 를 활용한 코드 스플리팅과 `Suspense`를 이용하여

hydration을 수행할 부분을 나눌 수 있게 되었습니다.

```tsx
import { lazy } from "react";

const Posts = lazy(() => import("./Posts.jsx"));

<Suspense fallback={<Loader />}>
  <Posts />
</Suspense>;
```

`lazy` 를 사용하여 특정 코드 부분을 메인 번들러에서 분리시키고,

번들러가 이를 별도의 script 태그로 만들어줍니다.

즉, `<Posts />` 에 대한 UI가 보이지 않더라도 다른 부분의 이벤트를 사용할 수 있게 됩니다.

또한 selective hydration은 `Suspense` 에 우선순위를 동적으로 부여합니다.

```tsx
<div>
  <Post />

  <Suspense fallback={<Loader />}>
    <Likes />
  </Suspense>

  <Suspense fallback={<Loader />}>
    <Comments />
  </Suspense>
</div>
```

위 코드에서 `<Post />` 는 hydration까지 완료된 상태라고 하고,

`<Likes />` 와 `<Comments />` 는 화면에 보이지만 스트리밍만 완료된 상태라고 해봅시다.

그러면 당연하게도 tree 상에서 먼저 발견되는 `<Likes />` 가 먼저 hydration이 될겁니다.

그런데 사용자가 이 시점에 `<Comments />` 컴포넌트를 클릭하면

리액트는 `<Likes />` 에 대한 hydration을 중지하고,

`<Comments />` 에 대한 hydration을 우선적으로 진행합니다.

⇒ 즉, 최대한 빠르게 hydration을 완료하면서도 화면 상 가장 급한 부분에 우선순위를 부여합니다.

## Next.js 14

(시간 되면)

책에 있는 버전은 12 혹은 그 이하 버전이라 그나마 최신 버전인 14로 짧게 살펴보겠습니다.

- app routing

  - default routing
  - dynamic routing (slugs)
    - /videos/12121212 같은 경우
  - not-found.tsx
  - <Link />
  - api routes

    ```tsx
    // app/api/test/route.ts

    import { NextResponse } from "next/server";

    export async function GET(request: Request) {
      return NextResponse.json({ data: "hello" });
    }
    ```

- server/client component
- data fetching
  - Client-Side fetching (useEffect + fetch)
  - SSR 방식: `force-cache`
  - SSG 방식: `no-store`
  - SSR or SSG can works in client component?
    - 보다 자세한 [참고](https://github.com/acdlite/rfcs/blob/first-class-promises/text/0000-first-class-support-for-promises.md#why-cant-client-components-be-async-functions)
- [Font Optimization](https://nextjs.org/docs/pages/building-your-application/optimizing/fonts)
- [Image Optimization](https://nextjs.org/docs/pages/building-your-application/optimizing/images)
