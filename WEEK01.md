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

## Front-End 프레임워크 소개, React 사용법, ECMAScript 2015

<details open>
<summary>1일차 학습</summary>
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
```async``` 함수가 Promise 객체를 받아 thenable job 을 수행하기 위한 키워드로, 이를 적용하면 ```then```, ```catch``` 등의 Promise 체이닝을 하지 않아도 되는 장점이 있습니다. 주의해야할 점은 **반드시 Async 함수 안에** 있어야 합니다. 암묵적으로 Promise 를 리턴하여 Promise 체이닝을 수행해야 하기 때문이죠.
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
<summary>2일차 학습</summary>
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

</div>
</details>

---------------------------------------

<details open>
<summary>3일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>