# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------
## 1주차 질문
- Q. 꼭 ```await``` 키워드는 ```resolved``` 상태인 Promise만  받을 수 있는 것일까요?
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

## 1주차 대 주제를 작성합니다.

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
```async``` 함수가 Promise 객체를 받아 thenable job 을 수행하기 위한 키워드로, 이를 적용하면 ```then```, ```catch``` 등의 Promise 체이닝을 하지 않아도 되는 장점이 있습니다. 주의해야할 점은 **반드시 Async 함수 안에** 있어야 합니다. 암묵적으로 Promise 체이닝을 수행해야 하기 때문이죠.
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

#### Async / Await 응용 테크닉
**promise**
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
