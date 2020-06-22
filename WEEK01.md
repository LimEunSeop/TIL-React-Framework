# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------

## 1주차 질문
- Q. 꼭 ```await``` 키워드는 ```resolved``` 상태인 Promise만  받을 수 있는 것일까요? (해결완료)
  ```
    A.rejected Promise 를 await 한다면 함수를 종단하고 rejected Promise 를 그대로 리턴합니다. resolved Promise 를 await 한다면 PromiseValue를 받을 것이고, return 키워드로 리턴시 resolved Promise에 PromiseValue는 return 값이 될것입니다.
  ```

- Q. 의존모듈 로딩은 type="module" 을 적용한 모듈스크립트가 실행되는 시점인가요? 의존모듈은 비동기로 로딩되나요? 의존모듈 로딩이 끋나야 비로소 모듈스크립트가 실행되나요? (질문이 이해가 잘 안가시면 맨 하단의 '비동기 로딩 속성' 정리 부분 봐주시면 감사하겠습니다.)
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

## 강의 시작에 앞서

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

이런식으로 모듈프로그래밍을 하면 DOMContentLoaded 시간이 눈에띄게 줄어드는 것을 볼 수 있습니다. 원래는 모든 js 파일이 로딩되는시간이 DOMContentLoaded 였는데 말이죠. 그 이유는 다음과 같습니다.

- HTML에 ```script``` 태그로 선언된 것은 main.js 단 하나밖에 없기 때문입니다. DOM 파싱 시 ```script``` 태그엔 main.js 밖에 없으니까요
- main.js 가 로딩완료된 시점에서 DOMContentLoaded 는 이미 끝난겁니다. 이제는 실행의 문제입니다. 앞으로 나오는 의존모듈은 main.js 의 실행과 연관돼있는 것입니다.

> 저는, 모듈프로그래밍 에서 script 모듈을 로딩하고 실행하는 시점을 모든 의존모듈 로딩후 라고 생각하고 있습니다.



이 시점에서 ```<script>``` 태그의 로딩 & 실행 메카니즘을 포함하여 다시 정립해보도록 합시다.

**비동기 로딩 속성**
- **async 속성** : 비동기 로딩 -> DOM 파싱 일시정지후 실행 (script 사이즈가 클 경우에 따라서는 DOMContentLoaded 이후 실행 가능)
- **defer 속성** : 비동기 로딩 -> 실행을 무조건 DOMContentLoaded 이후로 미룸

**스크립트 실행 방식** : 스크립트 로딩이 끝난 후 실행할 때의 이야기 입니다.
- 일반적으로 코드의 흐름을 읽어 차례대로 실행합니다.
- type="module" 로 정의된 모듈방식의 스크립트 파일은 실행 전 또다시 의존모듈을 불러들이는 작업을 합니다. 이전의 단순 스크립트와는 달리 **```의존모듈로딩 -> 스크립트 실행```** 으로 이루어집니다. 의존모듈로딩이 동기인지 비동기인지는 잘 모르겠습니다만(아마 DOM이 길어질 경우 parsing 에 방해될 수 있으니 비동기로 했을겁니다), 종전과 같이 단순 스크립트를 실행하는것이 아닌, 의존모듈을 로딩하는 과정까지 추가된다는 점에서 모듈방식의 스크립트는 특별하다고 볼 수 있습니다.

</div>
</details>

---------------------------------------

<details open>
<summary>2일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>
