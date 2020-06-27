# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------

## 1주차 질문
- Q. 꼭 ```await``` 키워드는 ```resolved``` 상태인 Promise만  받을 수 있는 것일까요? (해결완료)
  ```
    A.rejected Promise 를 await 한다면 함수를 종단하고 rejected Promise 를 그대로 리턴합니다.
    resolved Promise 를 await 한다면 PromiseValue를 받을 것이고, return 키워드로 리턴시
    resolved Promise에 PromiseValue는 return 값이 될것입니다.
  ```

- Q. 의존모듈 로딩은 type="module" 을 적용한 모듈스크립트가 실행되는 시점인가요? 의존모듈은 비동기로 로딩되나요? 의존모듈 로딩이 끋나야 비로소 모듈스크립트가 실행되나요? (질문이 이해가 잘 안가시면 맨 하단의 '비동기 로딩 속성' 정리 부분 봐주시면 감사하겠습니다.)
  ```
    A.하단의 그림을 참조하면, type="module" 속성을 띄는 네이티브 모듈은 기본적으로
    defer 방식을 취하는 것을 볼 수 있습니다. fetch시 의존모듈까지 모두 불러오며,
    따라서 실행시점에 의존모듈을 fetch 된다는 사실은 틀렸습니다. fetch 단계에서든
    스크립트의 import 문을 미리 다 읽어 의존모듈까지 같이 모두 불러옵니다.
  ```
![스크립트 로딩방식 종결](https://v8.dev/_img/modules/async-defer.svg)

- Q. React 프로젝트에서 Node의 존재의미란?
  ```
    A.차세대 웹 패러다임 기술을 미리 사용하도록 하기 위해 Node 패키지의 도움을 잠깐 받는것일 뿐.
  ```

## Front-End 프레임워크 소개, React 사용법, ECMAScript 2015

<details open>
<summary>1일차 학습 - React 소개</summary>
<div markdown="1">

### React 학습에 앞서 공부해야 할 것들

-   웹 표준(Standards) 및 접근성(A11Y) 이해
-   구조 디자인 (HTML5 마크업)
-   표현 디자인 (CSS3 스타일링 + 레이아웃)
-   반응형 디자인 (RWD)
-   인터랙션 디자인 (JavaScript +  DOM  API)
-   비동기 프로그래밍(AJAX) 및 보안 이슈(SOP/CORS), 우회 방법(JSONP)
-   RESTful 웹 서비스 (CRUD  + REST API)
-   모던 자바스크립트 (ES  6+)
-   Git 버전 관리 & GitHub 서비스

> Q) '웹  표준 및 접근성 이해' 란, 웹 표준을 준수하는 코딩 및, 키보드 접근성, 시각장애인 접근성 정도로 생각하면 될까?

### Front-End 개발 학습 로드맵
[https://roadmap.sh/frontend](https://roadmap.sh/frontend)
쭉 살펴보고 공부해야될 목표를 설정해봅시다. 일단은 React 에 집중! (향후 Sass, Jest, Cypress, TypeScript 등을 해보고 싶습니다.)

### ES 6+
미처 정리하지 못한 파트를 여기다 간단히 정리하겠습니다.

#### Async 함수
암묵적으로 Promise 를 리턴해주는 함수입니다. return 키워드로 값 리턴시 PromiseStatus는 resolved 가 되고, throw 키워드로 에러 발생시에는 rejected 가 되고, return 및 throw 뒤의 값이 PromiseValue 로 옵니다.
```javascript
async function asyncFn() {
  if ( Math.random() > 0.5 ) {
    return '0.5보다 큽니다.';
  } else {
    throw new Error('0.5보다 작습니다!!');
  }
}

asyncFn()
  .then(response => console.log(response))
  .catch(error => console.error(error.message));
```
#### Await 키워드
```async``` 함수가 Promise 객체의 PromiseValue 를 받기위한 키워드로, 이를 적용하면 ```then```, ```catch``` 등의 Promise 체이닝을 하지 않아도 되는 장점이 있습니다. 주의해야할 점은 **반드시 Async 함수 안에** 있어야 합니다. 암묵적으로 Promise 를 리턴하여 Promise 체이닝을 수행해야 하기 때문이죠.
```javascript
const getData = () => {
  let timeout = Math.floor(Math.random() * 2000);
  return new Promise(resolve => {
    window.setTimeout(() => resolve(['지연', '된', '데이', '터', '전송']), timeout);
  });
};

async function asyncFn() {
  const data = await getData(); // 데이터 응답까지 대기
  console.log(data); // 응답 받은 후 데이터 출력
}
```
> ```then```, ```catch``` 메서드가 Promise 를 반환하여 Promise 체이닝이 가능했던 것처럼 ```then``` 과 비슷한 역할을 수행시켜주는 ```await``` 키워드가 들어간 함수는 async 함수여야 합니다.
>
> 여기서 질문. 꼭 ```await``` 키워드는 ```resolved``` 상태인 Promise만  받을 수 있는 것일까요? 선생님께 질문드려야겠습니다.
> => 답 알아냈습니다. rejected Promise 를 await 한다면 함수를 종단하고 rejected Promise 를 그대로 리턴합니다. resolved Promise 를 await 한다면 PromiseValue를 받을 것이고, return 키워드로 리턴시 resolved Promise에 PromiseValue는 return 값이 될것입니다.

#### Async / Await 응용 테크닉
일반 Promise 사용구문과 Async/Await 구문을 비교해보고, Promise.all(), 구조분해 할당으로 응용해보겠습니다.

**Promise**
```javascript
const api = 'https://jsonplaceholder.typicode.com';

function asyncCallDatas() {
  let todo, photo;
  Promise.all([
    fetch(`${api}/todos/9`)
      .then(response => response.json())
      .then(data => todo = data),
    fetch(`${api}/photos/7`)
      .then(response => response.json())
      .then(data => photo = data)
  ])
  .then(results => console.log(todo, photo));
}

asyncCallDatas();
```

**Async / Await**
```javascript
const api = 'https://jsonplaceholder.typicode.com';

async function asyncCallDatas() {
  let todo = await (await fetch(`${api}/todos/9`)).json();
  let photo = await (await fetch(`${api}/photos/7`)).json();
  console.log(todo, photo);
}

asyncCallDatas();
```
await 를 꼭 해줘야 PromiseValue 를 얻을 수 있습니다. 그렇지않으면 Promise가 리턴됩니다.

**Promise.all(), 구조 분해 할당 적용**
```javascript
async function asyncCallDatas() {
  let [ todo, photo ] = await Promise.all([
    (await fetch(`${api}/todos/9`)).json(),
    (await fetch(`${api}/photos/7`)).json()
  ]);
  console.log(todo, photo);
}
```
- Promise.all 은 Promise 객체를 받아야 하기 때문에 앞에 최종적으로 await 를 붙이지 않았습니다.
- Promise.all 은 PromiseStatus가 resolved이고 PromiseValue가 배열인 Promise를 반환합니다. 이에 await를 하면 비로소 온전한 배열객체를 받기 때문에 비구조화 할당이 가능해지는 것입니다.
- Promise.all 중에 하나가 rejected 된다면 rejected 된 Promise만 반환합니다. 위 테크닉을 적용 시 이에대한 변수를 생각하여 코딩해야 할것 같습니다.

### 우리가 Framework 를 사용하는 이유
차세대 웹 표준에 사용될 개발 패러다임을 브라우저 호환 고민 없이 오늘날 바로 사용할 수 있기 때문입니다. 대표적인 차세대 개발 패러다임은 모듈프로그래밍, 컴포넌트 시스템 2가지가 있습니다.
> 그 중 모듈프로그래밍을 좀 정리하고싶어 개인적인 정리용으로 장황히 적었으니 참고용으로 간단히 보시기 바랍니다.

#### 모듈프로그래밍
![main.js 내부에서 의존모듈을 정의하는 모습](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-1-1.png?raw=true)
- 다음과 같이 module 을 import 하면 html에 ```script``` 태그를 일일히 선언하지 않고도 한 파일 내에서 모듈 의존관계가 명확히 정의됩니다.
- 의존모듈에서도 꼬리에 꼬리를 물어 의존모듈을 로딩합니다. 이 때, 모듈 로딩은 비동기로 이루어집니다.

![DOMContentLoaded 이후에 의존모듈이 로딩되는 모습](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-1-2.png?raw=true)

이런식으로 모듈프로그래밍을 하면 DOMContentLoaded 시간이 눈에띄게 줄어드는 것을 볼 수 있습니다. 원래는 모든 js 파일이 로딩되는시간이 DOMContentLoaded 였는데 말이죠. 그 이유는 main.js 하나에서 시작하여 의존모듈의 순서가 결정되고 fetch 되는데 이 의존모듈 전부 비동기 방식으로 불러오기 때문입니다.


이 시점에서 ```<script>``` 태그의 로딩 & 실행 메카니즘을 포함하여 다시 정립해보도록 합시다.

![스크립트 로딩방식 종결](https://v8.dev/_img/modules/async-defer.svg)

</div>
</details>

---------------------------------------

<details open>
<summary>2일차 학습 - React 시작하기</summary>
<div markdown="1">


### React 특징
React 프로그래밍 특징은 **선언형, 컴포넌트 시스템, 확장성** 입니다.
-  [**선언형(Declarative) 프로그래밍**](https://ko.wikipedia.org/wiki/%EC%84%A0%EC%96%B8%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)
 : 전달 속성 ```props```, 상태 ```state``` 변경 감지 → UI 업데이트(Rendering)
- 컴포넌트(Component) 기반 프로그래밍
: 캡슐화 된 컴포넌트 로직(Logic)은 템플릿(Template)이 아닌, **JSX**(Javascript+XML)로!
- 한 번 배워 어디서나 사용 가능(Learn Once, Write Anywhere)
: 웹 앱(Web App) 또는 네이티브 앱(Native App)

### 필요 라이브러리
- react
- react-dom

### React 의 필수개념
React Component, React Element, ReactDOM - rendering 에 관해 먼저 알아야 한다.
```jsx
// React Component (Functional)
function App() {
  return <div>React Element</div>
}

// React Element (JSX)
var app = <App/>

// ReactDOM - rendering
ReactDOM.render(app, document.querySelector('#app'))
```
- **React Component** : React Element 의 공통 설계도면과 같은 역할
- **React Element** : React Component 에 의해 만들어진 객체. HTML 태그처럼 사용가능
- **ReactDOM - rendering** : React Element 를 실제 DOM 에 붙이는 작업

### React Quick Overview 실습
[https://codepen.io/dmstjq12/pen/WNrjGwG?editors=1010](https://codepen.io/dmstjq12/pen/WNrjGwG?editors=1010)
의 실습내용을 통해 React Component, React Element 를 이용하여 복잡한 내용의 네이티브 컨트롤을 마치 HTML 사용하듯 손쉽게 사용할 수 있음을 느낍시다. (+ props 동작방식도 가볍게 살펴보아요)

### React 실무 개발환경
위에서 다뤘던 실습은 단순히 Javascript 내에서 React 라이브러리를 불러서 해결했던 것입니다만, 실무에서는 저런 방법을 사용하지 않습니다. 프레임워크 라는걸 구성합니다. 대표적인 이유로는 1일차 강의에서도 알 수 있듯이 최신 웹 개발 패러다임을 미리 쓰기 위함입니다.
![React Framework](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-2-1.png?raw=true)
아래와 같은 워크플로우를 실현하기 위해, Node 의 도움을 받습니다. 이렇게 Node 의 도움을 받아 빌드 후 배포하여 웹사이트에 로딩된 React 는 브라우저에서 실행되는 것입니다.

그림 해석, bundling, building 차이 정리. 질문사항 정리

### React 프로젝트 생성
```npx create-react-app \<ProjectName\>```
npx 는 최신 node 패키지를 설치하여 즉석으로 실행시켜주는 명령어 입니다. create-react-app 은 항상 최신버전이 사용되길 권장하므로 꼭 **npx** 를 사용하도록 합시다.

### React 프로젝트 디렉터리 구조
Create React App 으로 생성한 프로젝트 디렉터리 구조입니다. 엄청 중요합니다. 가볍게 넘어가지말고 통암기하여 각각의 역할을 확실히 해둡시다!!
```
.
├── README.md
├── node_modules/ # 개발 의존 모듈 집합 디렉토리
├── package.json
├── public/ # 정적 리소스 디렉토리
│   ├── favicon.ico
│   ├── index.html # 애플리케이션 기본 템플릿
│   └── manifest.json
├── src/ # React 애플리케이션 개발 디렉토리
│   ├── App.css
│   ├── App.js # 애플리케이션 파일
│   ├── App.test.js
│   ├── index.css
│   ├── index.js # 엔트리 파일
│   ├── logo.svg
│   └── serviceWorker.js
└── yarn.lock
```
#### node_modules
프로젝트에 필요한 개발 의존모듈 집합 디렉토리. react, react-dom, react-scripts 등과 그 모듈이 의존하는 또 다른 모듈이 여기에 설치되어 있습니다.

#### package.json
- **의존하는 모듈에 대한 정보를 관리합니다.** dependencies 속성에서 의존모듈 및 정보를 확인합니다.
- **실행 가능한 스크립트를 관리합니다.**

#### public
정적 리소스 디렉터리. HTML, CSS, Javascript, Image, Media 파일들이 있습니다. public 안에 있는 리소스는 HTML 에서 참조가 가능합니다. **기본 템플릿 파일인 index.html**이 들어있습니다. 아래는 index.html 에 대한 코드 설명입니다.
```html
<!DOCTYPE html>
<!-- en 값을 ko-KR로 변경 -->
<html lang="ko-KR">
  <head>
    <meta charset="utf-8" />
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <!--
      manifest.json은 웹 앱을 사용자의 모바일 장치 또는
      데스크톱에 설치할 때 사용되는 메타 데이터를 제공합니다.
      참고: https://developers.google.com/web/fundamentals/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <!--
      위의 태그에서 %PUBLIC_URL%을 사용합니다.
      %PUBLIC_URL%은 빌드하는 동안`public` 폴더의 URL로 대체 됩니다.
      public 폴더 안에 있는 파일들만 HTML에서 참조 할 수 있습니다.

      "/favicon.ico"또는 "favicon.ico"과 달리, "%PUBLIC_URL%/favicon.ico"는
      클라이언트 사이드 라우팅(CSR)과 non-root public URL에서 문제없이 작동합니다. %PUBLIC_URL% 을 해줘야 빌드 시 문제없이 작동합니다.
      `yarn build` 명령을 실행하면 non-root public URL에 대해 알 수 있을 겁니다.
    -->
    <title>React 앱</title>
  </head>
  <body>
    <noscript>이 앱을 실행하려면 JavaScript가 사용가능한 환경이어야 합니다.</noscript>
    <div id="root"></div>
    <!--
      이 HTML 파일은 템플릿입니다.
      브라우저에서 직접 열면 빈 페이지가 나타납니다.

      이 파일에 웹 폰트, 메타 태그 또는 Google 애널리스틱(통계, 분석)을 추가 할 수 있습니다.
      빌드 단계에서 번들 된 스크립트는 <body> 태그 내부에 삽입됩니다.

      개발을 시작하려면`npm start` 또는 `yarn start`를 실행하세요.
      배포를 위한 번들 파일을 생성하려면 `npm run build` 또는 `yarn build`를 실행합니다.
    -->
  </body>
</html>
```

#### src
실제 React 애플리케이션 개발 디렉토리. 이 안에서 모든 개발이 이루어집니다. 여기서 만들어진 가상 DOM 은 index.html 의 실제 native DOM 에 붙여지게 됩니다.

##### index.js 
핵심이 되는 엔트리(진입) 파일. 애플리케이션을 구성하는 설정이 포함되어 있습니다. App.js 파일을 불러오고 native DOM 에 렌더링 합니다. 
```javascript
// React 모듈 로드
import React from 'react'
// ReactDOM 모듈 로드
import ReactDOM from 'react-dom'
// 메인(인덱스) 스타일 로드
import './index.css'
// 앱 컴포넌트 로드
import App from './App'
// 서비스 워커 로드
import * as serviceWorker from './serviceWorker'

// ReactDOM 모듈의 렌더 함수를 사용해 #root (src/index.html) 요소
// 내부에 동적으로 App 컴포넌트(React Element)를 렌더링 합니다.
ReactDOM.render(<App />, document.getElementById('root'))

// 앱을 오프라인에서 작동시키고 보다 빠르게 로드 하려면 아래 코드의 unregister()를
// register()로 변경합니다. [노트: 이 방법은 몇 가지 문제를 수반하니 주의하세요.]
// 서비스 작업자에 대해 자세히 알아보기: https://bit.ly/CRA-PWA
serviceWorker.unregister()
```
##### App.js
애플리케이션 파일. 실질적인 React 앱을 구성하기 시작하는 파일입니다.


```javascript
// React 모듈 로드
import React from 'react'
// 로고 이미지 로드
import logo from './logo.svg'
// App 스타일 로드
import './App.css'

// 함수형 컴포넌트(Functional Component)
function App() {
  // JSX(JavaScript 문법 확장) 반환
  return (
    <div className="App">
      // class 속성 대신 className을 사용
      <header className="App-header">
        // 데이터(상태, state)를 템플릿에 바인딩 할 때는 {}를 사용 // Empty Element의 경우 반드시
        태그를 닫아야 함 (예: <br />)
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          <code>src/App.js</code> 파일을 수정하고, 저장하면 리로드 됩니다.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer">
          React 배우기
        </a>
      </header>
    </div>
  )
}

// App 컴포넌트 모듈 내보내기
export default App
```


### 아직 풀리지 않은 의문
이미지, css 임포트 하는것은 처음본다. 내일 복습하면서 좀 익숙해지자. import, export 규칙도 다시 복습하자

</div>
</details>

---------------------------------------

<details open>
<summary>3일차 학습 - 개발도구 확장</summary>
<div markdown="1">

### Pretier - Code formatter
설정된 옵션에 따라 자동으로 코드 스타일을 정리해주는 도구입니다.

#### 초기 설정
처음에 설정해야될 곳이 있습니다. 이 과정을 거치면 자동스타일링이 가능해집니다.
1. Settings 패널을 열어 'format javascript' 검색 후 사용하지 않도록 설정합니다.
2.  'format on save' 검색 후 사용 하도록 설정합니다.

이후 'prettier' 검색 후 사용할 Prettier 규칙을 설정합니다.
[https://prettier.io/playground](https://prettier.io/playground) 서비스를 이용하면 규칙적용을 쉽게 테스트하고 설정 json 코드를 얻을 수 있습니다.

### Formatting Toggle
prettier 의 포매팅 도구를 사용하고 싶지 않을 때 패키지를 disable 할 필요 없이 클릭 한 번으로 포매팅을 켜고 끌 수 있도록 하는 도구입니다.
![how to use formatting toggle](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-3-1.png?raw=true)

### React Snippets
패키지명 : ES7 React/Redux/GraphQL/React-Native snippets
► 기본 코드조각뿐만 아니라 React 전용 코드조각도 제공합니다. MarketPlace 의 명령어 리스트를 참고하여 자주 사용하는 명령어의 snipet 을 공부해봅시다.!

### React Pure To Class
React 함수형 컴포넌트를 클래스 컴포넌트로 손쉽게 변경하려면 **React Pure To Class** 확장을 설치합니다. 함수형 컴포넌트를 블록지정하고 ```SHIFT+CMD+P``` 로 명령을 찾아 실행시키면 되나, 불편하면 단축키를 지정할 수 있습니다. 저는 ```SHIFT+CTRL+OPT+P,C``` 로 하겠습니다. Pure To Class 패키지의 약자로 2자를 추린 것입니다.

### Auto Import
![Auto Import Package Info](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-3-2.png?raw=true)
필요한 모듈을 자동으로 불러 들입니다. Component js 파일 하나 만들고 다른 곳에서 React Element 태그 작성중 아래 스크린샷과 같이 Auto Import 해주겠다는 자동완성을 만나면 엔터를 눌러 Auto Import 해줍니다.
![Auto Import](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-3-3.png?raw=true)

### Import Cost
Import 한 모듈 코드 옆에 시각적으로 파일의 사이즈를 보여줍니다. 파일 용량이 크다 생각될 경우 좀더 하위경로의 필요한 모듈만을 가져와 최적화를 꾀할 수 있습니다.

### Path Autocomplete
모듈 경로를 작성할 때 불러올 모듈 파일 경로를 자동 완성해줍니다.

### Auto Complete Tag
태그 자동 닫기(Auto Close Tag), 태그 시작과 끝 자동 이름 변경(Auto Rename Tag) 기능을 재공합니다.

### Bracket Pair Colorizer2
코드 블록의 색상을 커스터마이징 합니다. 설정이 제대로 먹히지 않는 감이 있긴 하지만 나중에 뭔가 필요하다 싶으면 설정을 다시 살펴봐 변경해주도록 하겠습니다.

### Color Highlight & Manager
![Color Highlighter 존재이유](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-3-4.png?raw=true)
CSS 에선 다음과 같이 기본적으로 Color Hightlighing & Managing 기능이 제공되는데요, React JS 화면에서는 그렇지 못합니다. JS 코드 내에서도 이를 가능하게 해주는 툴이라 보시면 됩니다.

### Image Preview
에디터 상에서 svg 같은 이미지를 미리 볼 수 있게 하는 패키지 입니다.

### Translator
클래스, 변수 및 함수 이름을 작성할 때 한글 ☞ 영어 번역 기능을 제공합니다. ```SHIFT+CMD+T``` 단축키를 통해 사용합니다.

### React Developer Tools
https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi
크롬 extension 으로서 리엑트로 만들어진 페이지에 대한 정보를 좀 더 확인할 수 있도록 도와줍니다.

</div>
</details>

---------------------------------------

<details open>
<summary>4일차 학습 - JSX ➪ React 요소</summary>
<div markdown="1">

### React 렌더링 시스템 - Virtual DOM
React 는 가상 DOM 이라는 렌더링 시스템을 사용하고 있습니다. 실제 DOM 과는 구분되는 개념인데요, 결론 먼저 말씀드리자면 UI 속도를 최적화 하기 위해 도입되었습니다.

기존의 방식대로 실제 DOM 을 Javascript 로 직접 조작하면, 조작당한 Element 의 하위 Element 까지 모두 다시 렌더링 하게 되는데 이 때 렌더링 비용이 많이 들어 속도가 저하되는 결과를 낳습니다.

이러한 문제가 개선되어 나온 것이 Virtual DOM 입니다. Virtual DOM 은 Javascript 상에서 가상 DOM 이라는 것을 따로 관리하여 가상 DOM 의 변화가 있을 경우 변화가 있는 지점만 추려내 그 부분만 실제 DOM 으로 반영시켜주는 시스템 입니다.

#### Virtual DOM 동작 방식
Virtual DOM 은 크게 ```1.Virtual DOM tree 구성``` → ```2.기존 DOM 과의 차이점 비교``` ( **diffing** ) → ```3.변경된 부분을 실제 DOM 에 반영``` ( **patching** ) 3가지의 단계로 수정을 거듭하는데, 각 단계가 다음과 같이 h.js, createElement.js, diff.js, patch.js 4가지 모듈이 협업하여 이루어지게 됩니다.

![Virtual Dom Protocol](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/1-4-1.png?raw=true)

1. ##### Virtual DOM 생성
: **```h.js```** 가 모델(데이터)의 상태를 읽고 Virtual DOM tree를 구성합니다. 보통 최초에 1회 구성한 후 모델의 변화가 발생하면 그 변화에 따라 diffing 을 위해 새로운 Virtual DOM tree를 생성하게 되죠.

2. ##### diffing
: 모델의 변화로 생성된 새로운 Virtual DOM tree 를  **```diff.js```** 가 비교합니다. 이를 diffing 이라고 합니다. 비교하여 변화된 결과를 참고하여 patching 이 이루어집니다.

3. ##### patching
: 변화된 결과를 **```patch.js```** 가 전달받아 실제 DOM 에 반영합니다.  이를 patching 이라고 하는데, 상처를 메꿀 때 등 뗌빵을 메꾼다는 의미로 영어에선 patch 라는 단어를 쓰는데 그 단어의 이미지를 생각하면 쉽습니다. 단어의 의미답게 다른곳 반영없이 '그 부분만을 반영한다' 는 경향이 짙습니다. **patching 직전에는 실제 반영을 위해 실제 element 가 필요**한데,  그 실제 element 를 얻는 작업을 **```createElement.js```**가 수행합니다.
> Patch 라는 단어를 곱씹고 또 곱씹읍시다. 이 단어, 이 단계에서 Virtual DOM 의 핵심 로직이 존재합니다. 바로, 변경되는 부분만 반영되고 **하위 Element 가 리프레쉬 되지않아 비용이 절약되는 것**이죠. 처음에는 의문점이 있었습니다. 반영은 곧 실제 DOM을 조작하는 것이니 이전과 똑같이 하위 Element 도 리프레쉬 되지 않을까 싶었죠. 그치만 patch 라는 개념을 괜히 만들었겠습니까, 내부적으로 실제 DOM 을 조작을 하되 하위 Element 까지 리프레쉬 되는 이벤트를 막는 작업을 했겠죠. 그게 뭐라고요? 우리는 알 필요 없습니다.

#### Virtual DOM 예제
아이템을 추가하고 삭제하는 간단한 예시인데요, 아까 말씀드린 4가지 모듈 각각을 실제로 함수 형태로 가져와서 Virtual DOM 을 컨트롤하고 반영하는 것을 확인할 수 있습니다. 

```javascript
var h = require('virtual-dom/h')
var createElement = require('virtual-dom/create-element')
var diff = require('virtual-dom/diff')
var patch = require('virtual-dom/patch')

// Actual DOM
// var actual_dom = document.createElement('p')
// actual_dom.innerHTML = 'Actual DOM'
// document.body.appendChild(actual_dom)

// var count = 0
var data = ['vue.js', 'angular', "react"]

// Virtual DOM
function render(data) {
  var lists = data.map(function (item, index) {
    return h('li', [
      item,
      h('button', {
        type: 'button',
        onclick: function(e) {
          // 모델 데이터 변경
          data.splice(index, 1)
          //  화면 뷰 업데이트
          update();
        }
      }, 'delete')
    ] )
  })
  var list =  h('ul', lists)
  var input = h('input.add-content',{
    type: 'text',
    placeholder: 'Add Favorite Framework'
  })
  var add_btn = h('button.add-btn', {
    type: 'button',
    onclick: function(e) {
      var input = document.querySelector('.add-content');
      // 모델 데이버 업데이트
      data.push(input.value);
      // 화면 뷰 업데이트
      update();
      // 인풋 초기화
      input.value = '';
    }
  }, 'Add')
  var container = h('div.container', [
    input,
    add_btn,
    list
  ])

  return container
  // return h('p', 'virtual dom' + data)
}

function update() {
  // 새로운 가상 트리를 생인
  // var newTree = render(++count)
  var newTree = render(data)
  // 기존 가상 트리, 새로운 가상 트리하고 변경점이 있는지 확인
  var patches = diff(tree, newTree)
  // 변경사항이 발생하면 rootNode에 패치(붙인다)
  patch(rootNode, patches)
  tree = newTree
}

// var tree = render(count)
var tree = render(data)
var rootNode = createElement(tree)
document.body.appendChild(rootNode)
```
이 소스 중에서 흥미로운 점은 다음과 같습니다.

- **초기화 작업과 이후 작업이 다르다는것**. 비단 이 소스에만 존재하는 방식이 아니고 프로그래밍 전반에 걸쳐 존재하는 방식입니다. 최초 초기화 작업은 코드가 다를 수밖에 없습니다. 일관성있는 반복을 받아들이기 위해 만반의 준비를 하는 때 이니까요(이래서 생성자가 있는가 봅니다). 보시면 diff, patch 모듈이 빠지고 createElement 모듈, DOM API 가 쓰인것을 볼 수 있습니다. 최초생성이기 때문에 diff 할 대상이 없었고 따라서 변경사항 반영이 아닌 actual DOM 에 반영하는 native DOM API 가 쓰였던 것입니다.
- **patch 함수에 createElement 모듈이 안 보이는것**. patch 함수가 실제 DOM 에 반영하기 전에 실제 element 를 얻기 위해 내부적으로 createElement 작업을 같이 하기 때문입니다.
- **render() 와 update() 함수를 나눈것**. 마크업을 마치 컴포넌트화 하여 일관된 DOM 비교를 가능케하는 발판을 만든듯이 render() 함수가 그 역할을 톡톡히 수행하고 있습니다. 그 함수 속에서 Virtual DOM 의 구조를 정의할 때 어떤 이벤트가 발생하면 update() 함수를 호출할지. 개발자가 일련의 Virtual DOM 비교하는 작업을 트리거 하는 시점을 정할 수 있다는게 흥미로웠습니다. update() 함수는 우리가 배웠던 virtual DOM 관련 로직을 충실히 따르고 있고요 맨 처음에 그냥 React 만 배웠다면 이런 내부구조 절대 체험하지 못할 것입니다.

### React 에서의 Virtual DOM
앞서 공부한 내용을 바탕으로, React 에서의 Virtual DOM 은 어떤 규칙을 따르는지 추론해보는 시간을 가집시다.
```jsx
class App extends React.Component {
  constructor(props) {
    super(props)
  }

  render() {
    return (
      <div className="App">
        <AppHeader />
      </div>
    )
  }
}

ReactDOM.render(<App />, document.getElementById('root'))
```
React 에서는 App 클래스와 같이 컴포넌트 기반으로 마크업을 정의해 놓습니다. render() 함수에 실질적인 마크업이 정의돼 있고  이것이 Virtual DOM 비교의 발판을 마련할 것입니다.

저 render  메서드가 호출되는 시점은 초기화되는 시점과 update가 이루어지는 시점 두 곳이 되겠죠. 초기화부분은 저기 보이네요. ReactDOM.render 가 초기화 작업을 수행합니다. Virtual DOM을 실제 DOM 으로 붙이는 모습이 딱 봐도 보이네요. 아까 고찰했던것 처럼 실제 DOM 에 붙여져 초기화만 이루어질 뿐 diffing 이나 patching 작업은 없겠죠.

update 가 트리거 되는것은요? React 가 정해놓은 규칙을 따를 것입니다 (preview. 상태변화)

### JSX
마지막으로 다루지 못한 부분이 있습니다. 바로 XML 비스무리한 javascript 문법에 맞지 않은 정체불명의 코드. 바로 JSX(**JavaScript Syntax eXtension**) 라는 것입니다. 

>JSX의 X 가 XML 을 뜻하는 것인줄 알았는데 그건 아니네요. 자바스크립트 문법을 확장했다?? 저는 처음에 조금 의문점이 생겼습니다. 저 XML 같은게 중간에 interpolation 을 위에 JS가 끼워지는 형태라 XML 으로부터 파생하여 JS 를 쓰게 한다 해서 XMLJS 라고 해야지 왜 단어가 JS 가 먼저 붙어 JS 같이 받아들이게 할까... 지금 생각해보면 일단 약자에서 알 수 있듯이 JS 익스텐션이고, 그 익스텐션이란 JS 에서 원래 저런문법 안되는데 XML 을 추가할 수 있게 '허용'을 한다, 덤으로 기존 js 문법 활용하여 interpolation 지원해줄게 라는 점에서 JSX 라고 이름을 지은것이 아닌가 예상해봅니다.

저 문법이 바로 Virtual DOM tree 를 생성하게 해줍니다. 이전의 Virtual DOM 예제에서 h.js 모듈로 Virtual DOM tree 가 복잡하게 구성돼있는 것을 볼 수 있었습니다. React 에서는 **```JSX```** 라는 것을 지원하여 복잡한 구조를 시각적으로 간소화 시켜주고 이를 내부적으로 React.createElement() 라는 함수를 사용하여 Virtual DOM tree로 구성해 줍니다.

JSX 는 미래 웹 패러다임중 하나인 컴포넌트 프로그래밍을 미리 경험시켜주기 위하여 React 에서만 특별히 제공되는 문법입니다. 실제 웹브라우저 에서는 절대 읽히지 않을 소스라는 것이죠. 브라우저가 해석가능한 소스는 React 에서 제공하는 Virtual DOM node 인 React.createElement() 로 생성되는 React Element 입니다. 따라서 이것으로 변환작업을 해줘야 하는데 그것을 React 프레임워크에 포함돼있는 Babel 이라는 Node 프로그램이 빌드시 수행합니다.

아래의 JSX 가 Babel 을 거치면 다음과 같이 변합니다.

#### 컴파일 이전
```jsx
const iframeWrapper = (
  <div className="iframe-wrapper">
    <iframe src="https://yamoo9.github.io/react-master" />
  </div>
)

```

#### 컴파일 이후
```javascript
var iframeWrapper = React.createElement(
  // .iframe-wrapper
  'div',
  { className: 'iframe-wrapper' },
  // .iframe-wrapper > iframe
  React.createElement('iframe', { src: 'https://yamoo9.github.io/react-master' })
)
```

</div>
</details>

---------------------------------------

<details open>
<summary>5일차 학습 - JSX 활용</summary>
<div markdown="1">

### JSX 
React Element를 사용자가 보기좋게 쉽게 작성하기위해 제공되는 React 만의 JS 확장문법입니다.

#### JSX 삽입 위치
JSX 구문은 식(expression) 입니다.
1. expression 이 들어갈 수 있는 곳이라면 어디든 들어갈 수 있습니다.
2. JSX 식 자체에 데이터를 바인딩 하기 위해 원하는 위치에 중괄호로 묶어 그 안에 Javascript expression 을 채웁니다. 중괄호 안에는 무조건 Javascript의 expression 이 와야 합니다. 함수의 호출이던 3항연산자던 무엇이든 올 수 있습니다.
3. 바인딩할 Javascript expression 에도 expression이 또 올 수  있기 때문에 JSX 를 넣을 수 있습니다.

**이렇게 1~3의 과정이 무한 반복될 수가 있습니다. 다음과 같이 구성도 가능한 것이죠.**
```jsx
const app = (
  <div className="app">
    {
      <li key={2}>b</li>
    }
  </div>
)
```
다만 JSX 를 작성하는 데에는 규칙이 있으므로 자세히 살펴봐야 합니다.

#### JSX 데이터 바인딩 규칙
데이터가 바인딩 될때는 특정 위치 특정 조건에서 예외적인 내부처리가 있기 때문에 일관성을 따지기 보단 특정 예외상황을 상식이다 생각하고 암기해야할 필요가 있습니다.

---
- **속성에 데이터를 바인딩 할 때는 숫자던 문자던 쌍따옴표가 필요 없습니다.** Babel 이 React Element 로 변환시켜주고 실제 DOM 에 붙여질 때 알아서 쌍따옴표가 생길 것입니다.
```jsx
<span class={number % 4}>test</span>
```
---
- **속성에 null이 바인딩 되면 실제 DOM 에서는 그 속성이 안보일 것입니다.**
```jsx
<abbr title={abbrs.jsx ? abbrs.jsx : null}>{headline}</abbr>
```
---
- **인라인 스타일 등 객체 비스무리한걸 받는 속성에는 객체를 바인딩 합니다.** style이 아닌 다른속성에 바인딩 하면 적용 안됩니다. 누누히 말씀드리지만 특정한 파싱 규칙이 분명히 적용돼 있습니다.
```jsx
const figure = <figure style={{ marginTop: '1rem', marginBottom: '0.8rem' }} />
```
---
- **JSX 에서 주석을 작성할 때는 JS 바인딩을 하여 JS를 이용할 수밖에 없는데 ```/* */ ``` 방식을 사용할 것을 권장합니다.** ```//``` 을 사용해도 되나, 닫은 중괄호까지 주석처리 되므로 닫는 중괄호가 개행이 돼있어야만 합니다.
```jsx
const app = (
  <React.Fragment>
    {/* JSX 안에서의 주석은 이렇게 하는것을 권장합니다. */}
    {[ // 이렇게는 됨. 그러나 뒤가 모두 주석처리되어 한 줄 구성이 불가능해짐
      <p>a</p>,
      <p>b</p>
    ]}
  </React.Fragment>
)
```
---
- **JSX Whitespace는 empty line 포함 각 줄의 처음과 끝에 존재하는 whitespace 는 전부 제거됩니다.** Whitespace 의 연속이 1개의 공백으로 치부되는 HTML 과는 상반되죠.
-----
- **리스트 랜더링 별거 아닙니다. 그냥 JSX의 list 배열 적어주면 됩니다.** 그러나 list 는 주로 동적 반복 렌더링이기 때문에. 비슷한 모양의 element일 테니까 주로 map 메서드를 사용하여 배열을 반환하는 형태가 될 것입니다.
```jsx
const reactFamily = [
  { id: 'goqhwkay1', name: 'React' },
  { id: 'goqhwkkc8', name: 'Redux' },
  { id: 'goqhwkzr4', name: 'React Router' },
]

const ReactFamilyJSX =
  // 조건 식
  reactFamily.length > 0 ? 
    (
      // 조건이 참인 경우
      <ul className="react-family">
        {reactFamily.map(member => (
          <li className="react-family__member">{member.name}</li>
        ))}
      </ul>
    ) : 
    (
      // 조건이 거짓인 경우
      <p>공부 할 React 패밀리가 없습니다.</p>
    )
```
---
- **li item 에 key 속성 설정 안해주면 경고뜹니다.** html 확인했더니 속성이 보이지는 않네요, 이 의문점 해결해야될듯 싶습니다.
---

#### JSX 작성 규칙
- **속성명은 camelCase 로 표기합니다.** class 속성은 JS 에 이미 예약어가 있기 때문에 className 이라고 대체합니다만 여기서 camelCase 를 지켜야합니다. tabindex 속성 또한 JSX 에서는 tabIndex 라고 표기함으로써 일관성을 살립니다.
```jsx
<div className="container" tabIndex="-1">
  ...
</div>
```
> 애플리케이션 접근성 향상을 위한 표준 기술 [WAI-ARIA](https://www.w3.org/TR/wai-aria-1.1) 속성 및 상태(`aria-*`)는 HTML 표준 속성과 동일한 하이픈 케이스(hypen-case) 표기법을 사용 합니다. ariaLabel 이라 하면 이상하잖아요. aria 는 네임스페이스의 성향이 짙으므로 표준과 동일하게 작성하는 것입니다.
> ```jsx
><div aria-label="키보드 컨트롤 도움말" role="group">
>  ...
></div>
>```
---
- `<img/>`, `<br/>`, `<area/>` 등 **콘텐츠가 없는 요소는 반드시 닫아(`/>`) 줘야 합니다**.
```jsx
<div className="empty-elements">
  <img src={image.src} alt={image.alt} />
  <br />
  <map>
    {mapItems.map(item => (
      <area shape={item.shape} coords={item.coords} href={item.href} title={item.title} />
    ))}
  </map>
</div>
```
---
- **root 요소는 하나만 와야합니다.** 따라서 2개이상 나열하려면 container 아무거나 혹은 <React.Fragment> 로 감싸야 합니다. React.Fragment 요소는 실제DOM 에 렌더링 안 됩니다.
```jsx
const ButtonGroup = (
  <React.Fragment>
    <button type="button" className="button is-save">
      저장
    </button>
    <button type="button" className="button is-cancel">
      취소
    </button>
  </React.Fragment>
)
```

</div>
</details>

---------------------------------------

<details open>
<summary>6일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>
