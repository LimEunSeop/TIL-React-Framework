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
2. `<Route render>` : props 를 받는 함수를 받아 래핑 렌더링을 수행합니다. 커스텀 속성 전달할 때 이것을 이용하면 됩니다. 이 때, props 파라미터를 전개하여 같이 전달해주는 것도 중요합니다.
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
<summary>2일차 학습 - React Router</summary>
<div markdown="1">

#### Switch 컴포넌트
위에서부터 차례대로 검사하여 path속성이 매치되는 Route 객체를 하나라도 만나면 break 합니다.
```jsx
<Switch> 
  <Route path="/about" component={About} />
  <Route path="/:user" component={User} />
  <Route path="*" component={NoMatch} />
</Switch>
```

#### Link 컴포넌트
Redirection 없이 현재 Path와 History 객체의 조작만으로 애플리케이션의 탐색이 가능하도록 해줍니다.
>title, id, className 과 같은 속성을 정의하면 내부의 a 요소에 그대로 전달됩니다. styled-component 했던때 처럼요.

##### 사용 예시
<details close>
<summary>펼치기</summary>
<div markdown="1">

```jsx
// to 경로 문자열(string)
<Link to="/lectures">강의</Link>

// to 패스 + 검색(?) 문자열
<Link to="/lectures?sort=createdAt">강의</Link>

// to 객체(object) 설정
// pathname : 링크 경로 문자열
// search : 쿼리 매개변수 문자열
// hash : URL 해시(#) 문자열
// state: 상태 설정 (location으로 접근. 예: { fromDashboard: true })
<Link to={{ 
  pathname: '/lectures', 
  search: '?sort=createdAt' 
}}>강의</Link>

// to 함수(function) 설정
// 현재 위치(location)를 인자로 전달 받는 함수를 사용해 객체 또는 문자열 반환
<Link to={
  (location) => ({ ...location, pathname: '/lectures'})
}>강의</Link>

<Link to={
  (location) => `${location.pathname}?sort=name`
}>강의</Link>

// replace 불리언(boolean) 설정
// 속성을 설정할 경우, 링크 클릭 시 History에 새로운 항목을 추가하는 대신 현재 항목으로 교체
<Link 
  to="/lectures" 
  replace
>
  강의
</Link>
```

</div>
</details>

##### innerRef 설정
function 형태와 RefObject 형태를 받을 수 있습니다. React 16, React Router 5.1 이상이면 그냥 ref 속성 사용해도 됩니다.

###### function 형태
```jsx
<Link
  to="/"
  innerRef={node => {
    // `node`는 마운트 된(mounted) DOM 요소를 참조하고,
    // 마운트가 해제 된 경우(unmount)는 null 입니다.
  }}
/>
```

###### RefObject 형태
```jsx
class LinkRefDemo extends React.Component {
  
  anchorRef = React.createRef()

  render() {
    return (
      <Link to="/" innerRef={anchorRef} />
    )
  }

}
```

#### NavLink 컴포넌트
Navigation 에 맞게 Link 컴포넌트를 개조한 컴포넌트입니다.
```jsx
<nav className="global-nav">
  <ul>
    <li><NavLink to="/" exact>홈</NavLink></li>
    <li><NavLink to="/lecture">강의</NavLink></li>
    <li><NavLink to="/lab">연구</NavLink></li>
  </ul>
</nav>
```
- 일단 현재 Path 와 to속성의 rule 을 쫙 비교합니다. rule 에 맞으면 active class 를 붙여줍니다. 사용자는 그 active 클래스에 스타일을 부여하여 활성화됨을 나타내는 것입니다.
- 링크를 클릭하면 to 속성의 Path 로 링크를 이동시켜줍니다.

##### exact, strict 속성
NavLink 컴포넌트는 active 클래스 붙일까말까 에 이 속성을 쓰는 것입니다.
> Route 컴포넌트는 자식 컴포넌트를 렌더링 할까말까 rule 비교 시 이 속성을 썼습니다.

##### activeClassName, activeStyle 속성
activeClassName 은 active 상태시 클래스이름을 커스터마이징하고, activeStyle 은 active 클래스이름이 무엇이건 to rule 에 맞아 active 조건 충족 했을 경우 정의한 인라인스타일을 먹입니다.

#### Redirect 컴포넌트
이 컴포넌트를 만나면 특정 URL 로 Redirection 시킵니다.
from 속성이 없으면 만날시 무조건 to 로 Redirection 됩니다. to 에 오는 값은 Link 처럼 그냥 String 이 오거나 Location 에 들어갈 객체가 온다는 점이 똑같네요.
```jsx
// to : 문자열 설정
<Redirect to="/dashboard"/>

// to : 객체 설정
<Redirect to={{
  pathname: '/login',
  search: '?utm=your+file',
  state: {
    referrer: currentLocation
  }
}}/>
```
from 이 오는순간 rule 을 적용하여 현재 path 와 비교합니다. exact strict sensitive 오는것 다 똑같네요.
```jsx
// from : 문자열 설정
<Redirect from="/admin" to="/dashboard" />
<Redirect from="/users/:id" to="/users/profile/:id" />

// exact : 정확한 URL 매칭 설정
<Redirect exact from="/" to="/home" />

// strict : 엄격한 URL 매칭 설정
<Redirect strict from="/one/" to="/home" />

// sensitive : 대소문자  URL 매칭 설정
<Redirect sesitive from="/One/" to="/one" />
```
음.. Redirect 에는 push 속성만 있네요. 공식문서 보니까 replace 같은건 없었습니다. 제 예상으로는 Link와 Redirect 의 기본동작이 다르기 때문이라고 생각드는데요, Link는 Push 가 기본 동작이어서 replace 라는 옵션이 있었던 것이고 Redirect는 replace 가 기본동작이어서 push 라는 옵션이 있었던 것인가 봅니다. 참으로 흥미롭습니다. 어느 사이트를 가면 redirect 가 대충되어 뒤로가기 하면 다시 redirect 되는 문제가 있었는데 React Router 에서는 Redirect 에서 이것을 기본적으로 해결해 주네요. 
```jsx
// push : 현재 history 항목을 바꾸지 않고, 새 항목 추가
<Redirect to="/dashboard" push />
```

이 Redirect 는 올바르지 않은 주소 입력시 page not found 로 리다이렉팅 해줄때 유용히 쓰입니다.

</div>
</details>

---------------------------------------

<details open>
<summary>3일차 학습 - Route Props, 중첩,보호 라우팅, Redux 통합</summary>
<div markdown="1">

### Route 자식 컴포넌트 Props
Route 자식 컴포넌트는 match, location, history 3가지 props 를 받게 되어 있습니다.

**match** : path 의 매칭관련 정보를 담습니다.
- path : Route 객체의 path 속성값
- url : 브라우저의 URL에 적힌 path 를 그대로 가져옵니다.
- params : 매개변수를 통해 넘어온 값들의 쌍입니다. 뒤에 설명합니다.
- isExact : url 이 path 에 정확히 매칭되었는지의 여부 (ex. exact 속성이 없는 Home Link 는 다른페이지 탐색시 IsExact 가 false 입니다. 얻어걸린 match 는 false 가 된다는 뜻입니다.)

**location** : 현재 URL 위치정보와 필요한 상태값을 담습니다.
- hash : 현재 url 에 hash 가 있으면 여기에 담깁니다.
- pathname : 현재 주소창의 path 를 그대로 담습니다.
- search : 현재 URL 의 queryString 을 담습니다.
- state : 현재 state 를 담습니다(이전 링크로부터 전달된 값)
> Link 나 Redirect 컴포넌트의 to 속성에서 location 에 들어갈 속성을 객체로 구성하여 전달할 수 있는데요, Route 자식 컴포넌트의 props.location 에 세팅되게 됩니다. pathname 당연히 세팅해줘야하고 hash, search, state 는 옵션입니다. pathname 만 쓸거면 그냥 to속성을 문자열로 세팅하죠. 객체를 던진다는건 나머지 속성을 세팅해준다는 목적으로 던지는 것입니다.

**history** : history 관련한 함수 (replace, push, goForward, goBack) 이 있다. push나 replace 로 프로그래밍방식으로 네비게이션이 가능하지만, location 객체(state 등등) 설정하는 것이 불분명하여 당분간은 Link 를 사용하는것이 좋지 않을까 싶음.

자주쓰는 테크닉을 한번 살펴봅시다.

#### 커스텀 props 전달 테크닉
match, location, history 말고도 sortBy 등의 사용자 정의 props 를 전달할 수도 있습니다. 이 때는 render 속성을 통해 래핑렌더를 실시해야 합니다.
```jsx
<Route
  path="/categories"
  render={(props) => <Categories sortBy="createdAt" {...props} />}
/>
```

#### match.params
Route 컴포넌트의 path 속성에 `:` 을 사용해 입릭된 것은 매개변수 취급을 받아 자식컴포넌트의 props.match.params 로 값을 넘겨줍니다.
```jsx
<Route
  path="/category/:categoryName"
  component={Category}
/>
```
```jsx
const Category = ({ match }) => {
  const { categoryname } = match.params
  return <h1>{categoryName} 카테고리</h1>
}
```

#### 쿼리스트링 처리
Route 컴포넌트 자식에 전달되는 props.location 에는 window.location 과 역할이 같아 쿼리스트링까지 내장하고 있습니다. query-string 모듈을 사용하면 이를 효율적으로 다룰수 있습니다.
```jsx
import queryString from 'query-string'

const Category = ({ match, location }) => {
  const { categoryName } = match.params
  const { sortBy } = queryString.parse(location.search)
  return <h1>{categoryName} 카테고리</h1>
}
```

#### 버튼으로 네비게이팅
링크이동은 `a` 태그로 하는것이 맞지만, 뒤로가기 버튼에 한해서 버튼으로 구현하는 것도 가능합니다. 이는 접근성에 크게 위배되지 않아보이는게, 애플리케이션의 인터렉션 버튼의 일종이기 때문이라고 생각합니다.
```jsx
const Category = (props) => {

  function goCategories() {
    props.history.push('/categories') // 히스토리 스택에 push. 즉 일반적인 이동
    // props.history.replace('/categories') // 히스토리 스택의 top Item을 교환. 즉 리다이렉팅과 같은 작용
  }

  return <button type="button" onClick={goCategories}>전체 카테고리로 이동</button>
}
```

### 중첩 라우팅 구성방법
자식 컴포넌트에서 새로운 서브 Navigation 구성하는데, **Nav의 to는 앞에 match.url 을, Route 의 path 에는 match.path 를 배치 시켜야 합니다.** Nav는 주소창의 URL 을 확장해야만 하고, Route 는 상위 컴포넌트에서 설정한 파라미터를 그대로 forwarding 해야할 의무가 있기 때문입니다.

```jsx
{/* 중첩 링크 구성 */}
<NavLink to={`${match.url}/1f423`}>병아리</NavLink>

{/* 중첩 라우트 */}
<Route
  path={`${match.url}/:id`}
  component={FigureIcon}
/>
```

#### 디렉터리 구성
각 페이지를 views 디렉터리에 넣습니다. 중첩라우트가 발생하는 컴포넌트는 디렉터리를 새로 구성하여 index.js 에서 nav 와 route 를 구성하고 서브페이지를 추가해 나가는 것을 반복해 나가면 됩니다.
```
src/
└── views/
    ├── Home.js
    ├── Lab/ # 중첩 라우트 디렉토리
    │   ├── index.js # 중첩 라우팅 기본 컴포넌트
    │   ├── FigureIcon.js # 동적 라우팅 컴포넌트
    │   └── Lab.css
    ├── Lecture.js
    └── styles.js
```

#### 라우팅 정보 추출
match.params 나 location 객세를 읽어서 확인합니다.
```jsx
export default ({ match, location }) => {

  console.log(match.params) // match.params 정보 확인
  console.log(location)     // location 정보 확인

  // match.params.id
  const { id } = match.params

  return (
    <figure
      style={styles(figure, { marginTop: 50, background: iconImage(id) })}
    />
  )
}
```

### 보호된라우팅
컴포넌트 래핑을 통해 인증된 사용자만 라우팅되도록 하는 Route 를 만들 수 있습니다. 긴말필요없이 타이핑으로 예제를 따라치겠습니다.

#### 인증
간단한 인증 테스트를 위해 Auth 클래스를 작성합니다.
```jsx
class Auth {

  constructor() {
    this.isAuth = false
  }

  login(cb) {
    this.isAuth = true
    typeof cb === 'function' && cb()
  }

  logout(cb) {
    this.isAuth = false
    typeof cb === 'function' && cb()
  }

  isAuth() {
    return this.isAuth
  }
}

export default new Auth()
```

#### 보호된 라우트
`ProtextedRoute` 컴포넌트는 이름 그대로 `component` 속성으로 전달 받은 컴포넌트를 래핑하여 보호합니다. `render` 함수를 활용해 감싼 컴포넌트를 렌더링 합니다.
```jsx
import React from 'react'
import { Route, Redirect } from 'react-router-dom'
import auth from './auth'

const ProtectedRoute = ({component: Component, ...rest}) => {
  <Route {...rest} render={
    (props) => auth.isAuth ?
                <Component {...props} /> :
                <Redirect to={{
                  pathname: '/',
                  state: { from: props.location }
                }}/>
  }>
}
```
> 리다이렉트를 로그인페이지로 이동하면 괜찮을듯? 로그인 다되면 state 로 받은 props.location.url 값 참고하여 이전에 이동실패한 페이지로 이동하면 될듯

#### 라우트 설정
보호된 라우트 모듈을 불러와 인증 없이 접근 못하도록 라우팅 설정합니다.
```jsx
import ProtectedRoute from './ProtectedRoute'

<Router>
  <Switch>
    <ProtectedRoute path="/admin" component={AdminDashboard} />
    <Route path="/" component={Landing} />
  </Switch>
</Router>
```

#### 로그인
로그인 처리 할 컴포넌트에 `auth`모듈을 불러온 후, 버튼을 클릭하면 handleLogin 핸들러를 실행해 로그인 되도록 설정합니다.
```jsx
import React from 'react'
import auth from './auth'

class DemoLogin extends React.Component {

  handleLogin = () => {
    const { history } = this.props
    auth.login(() => history.push('/app'))
  }

  render() {
    return (
      <button type="button" onClick={this.handleLogin}>로그인</button>
    )
  }
}

export default DemoLogin
```

#### 로그아웃
로그아웃 처리 할 컴포넌트에 `auth` 모듈을 불러온 후, 버튼을 클릭하면 handleLogout 핸들러를 실행해 로그아웃 되도록 설정합니다.
```jsx
import React from 'react'
import auth from './auth'

class DemoLogout extends React.Component {

  handleLogout = () => {
    const { history } = this.props
    auth.logout(() => history.push('app'))
  }

  render() {
    return (
      <button type="button" onClick={this.handleLogout}>로그아웃</button>
    )
  }
}

export default DemoLogout
```

### Route 컴포넌트 props 할당 규칙
이 시점에서 Route 컴포넌트의 자식의 props 할당규칙을 정리해봅시다.
```jsx
<Route component={Children} />
```
이렇게 렌더링 하면 Children 컴포넌트에 match, location, history 가 잘 들어갑니다. 그러나 아래와 같은 방식은 할당 안됩니다.
```jsx
<Route>
  <Children />
</Route>
```
아래와 같이 라우터와 직접 연결되어있지 않은 컴포넌트도 할당 안됩니다.
```jsx
<Route component={Test}>

function Test(props) {
  return <Children />
}

function Children(props) {
  console.log(props) // match, location, history 없음
}
```
이렇게 되면 Children 정의때 withRouter HOC 를 사용하여 export 하거나 Hook(useHistory, useLocation, useParams, useRouteMatch)을 사용해야합니다.

> withRouter HOC 도 뭔가 Consume 하는 느낌이 납니다. 왜냐, 필요한 뎁스에 가서 사용하니까요. 자식에 대대손손 props 를 안물려줘도 withRouter 하면 Route 컴포넌트 props 가 전달되니까요. 다만 직접적인 자식은 자동으로 받을 수 있다는 것이 차이지만요.

</div>
</details>

---------------------------------------

<details open>
<summary>4일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>