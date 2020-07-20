# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------
## 5주차 질문
- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

## 싱글 페이지 앱 제작 및 미들웨어, 라이브러리 등을 활용 방법

<details open>
<summary>1일차 학습 - React Router</summary>
<div markdown="1">

### React Router
URL Path 를 읽어 해당되는 컴포넌트를 렌더링 하도록 도와주는 모듈입니다. 단순 SPA를 구현할 시에는 url 이 계속 일정하여 사용자경험 측면에서 한계가 있었는데 컴포넌트가 교체될 때 만큼은 MPA 방식처럼 하여 라우팅 문제를 해결할 수 있도록 해줍니다.

#### 구현방법
```jsx
const App = (props) => (
  <Router>
    <div className="App">
      <Route path="/" render={(props) => <h1>홈 페이지(render 함수 활용)</h1>} exact />
      <Route path="/about-router" component={AboutRouter} />
    </div>
  </Router>
)
```
1. Router 의 휘하로 둘 곳을 정하고 그곳에 BrouserRouter 컴포넌트를 배치합니다 (주로 최상단에 배치. index.js App.js 중 편한곳 선택)
2. 그 후 컴포넌트를 둘 곳에 Route 컴포넌트를 배치하고 path rule 정한 후 컴포넌트를 연결시켜 줍니다.

각 요소에 대해 조금씩 자세히 가봅시다.

#### Router 컴포넌트
React Router 구성시 가장 바깥에 위치하는 컴포넌트입니다. `BrowserRouter`, `HashRouter` 이 두개가 대표적인데, `BrowserRouter` 가 좀 최신이고 구형 브라우져에 안 돌아가고, `HashRouter` 가 좀 구식이고 구형 부라우저에 돌아갑니다. 

>Github Page 에서는 HashRouter 를 써야 돌아간다고 합니다.

#### Route 컴포넌트
path 속성을 읽어 일치하면 컴포넌트 렌더링을 실행하는 컴포넌트입니다. 이 렌더링 유형은 크게 3가지가 있습니다.

##### Route 의 렌더링 유형
1. `<Route component>` : 미리 정의된 컴포넌트를 렌더링 합니다.
2. `<Route render>` : props 를 받는 함수를 받아 래핑 렌더링을 수행합니다.
3. `<Route children>` : 매칭된 경우 자식컴포넌트를 렌더링하는데, 나중에 배운다고 합니다.

##### Route 렌더링 컴포넌트 Props
`<Route>` 에서 렌더링하는 컴포넌트는 `<Route>` 로부터 3가지 Props 를 받습니다.

- match : path가 :categoryname 으로 올 경우 props.match.paramas.categoryName 이 세팅됩니다.
- location : location.search 로 쿼리스트링 받아올 수 있습니다. queryString.parse 모듈로 간단히 객체화 가능합니다.
- history : 네비게이션 수행합니다. 아까부터 생각하건데 js 의 api 그대로 가져오는듯 싶습니다.

##### `<Route>` 컴포넌트 설정
- exact : path 가 정확히 일치해야 렌더링합니다.
- strict : exact 와 같이쓸때 후미슬래쉬도 따집니다.
- sensitive : path 의 대소문자를 구분합니다.



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
