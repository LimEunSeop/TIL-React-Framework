# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------
## 3주차 질문
- Q. 다이얼로그 컴포넌트 작성 시 Data List 를 Props 로 받으면 어떨지?
  ```
    A. Data List 를 관리하는 컴포넌트와 다이얼로그 사이의 depth가 깊으면 props 전달이 힘들 수 있습니다.
    Context를 이용할 것을 염두에 두고 개발하여야 합니다.
  ```

- Q. React.useState Hook 사용 시 state를 한 곳에 관리해도 괜찮은지?
  ```
    A. Hook 에서의 setState는 class Component의 setState 와 동작 방식이 다릅니다.
    Class Component의 setState는 전달된 속성만 교체하는것에 반해
    Hook 에서의 setState 는 전달한 객체로 그대로 교체합니다.
    setState 는 비동기 메서드이기 때문에, Class Component 의 경우는
    연속적인 setState 가 있어도 이 변경사항이 합쳐지지만
    Hook 은 변경사항이 합쳐지지 못합니다.
    따라서 Hook 에서는 상태를 분리하여 관리해야 합니다.
  ```

- Q. React Hook 에서 콜백등의 함수참조는 어떻게 해결하나요?
  ```
    A. useCallback() 이라는 훅을 이용해 함수를 유지합니다.
  ```

- Q. useRef()가 있다면 왜 sideEffect 발생하는 Hook 을 사용하는지?
  ```
    A. useRef() 는 굳이 안써도 됩니다. Hook 내부에서 querySelectorAll() 과 같은 DOM API 로
    Element 가져오면 되니까요. 그런데 Ref 를 쓰게 되면 Component 의 멤버로 다룰수 있으니까 편한겁니다.
    useRef() 는 참조하기 편하게 멤버로 지정해놓기 위함이고, sideEffect 를 이용하는 것은 이를 이용하여
    실제 DOM 처리를 하기 위함입니다. 대립되는 주체가 아니고 상호보완적인 개념이지요.
    그리고 FileInput을 submit 하는 eventhendler 에서도 사이드이펙트를 발생시키는데요,
    이는 DOM이 모두 마운트 되고나서의 User Input 을 핸들링하는 것이니 사이드이펙트가 당연히 발생하는것입니다.
  ```

## React 훅, 폼, 고차 컴포넌트, 스타일 컴포넌트 그리고 미니 프로젝트 {E1}

<details open>
<summary>1일차 학습 - Mini Project {E0}</summary>
<div markdown="1">

### React Mini Project {E0}
배열(Array) 메서드를 활용하여 동작을 정의한 기초적인 리엑트 프로젝트 입니다. [(프로젝트주소는 여기에)](https://github.com/LimEunSeop/React-Array-Methods-App)

![E0 Project](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/3-1-1.png?raw=true)

#### 사용 기술
- React Testing Library + Jest
- Context API

#### 프로젝트 수행 순서
모든 개발은 ```App.test.js``` 내에서 TDD 방식으로 먼저 진행되었습니다.
1. ```create-react-app``` 으로 프로젝트 생성
2. ```public``` 디렉터리 내의 기본적인 정적 리소스 재구성
3. App.js 기본적인 템플릿 작성
4. ```/src/utils``` 디렉터리 내에 필요한 유틸리티함수 미리 작성
5. 유틸리티 함수 이용하여 앱 초기 렌더링(list 출력) + 이벤트 핸들러 연결
6. ```/src/components``` 로 컴포넌트 분리
7. ```/src/contexts``` 에 Context 생성후 적용

#### 정적리소스
기본 정적 리소스에 나만의 리소스를 교체하고, manifest.json 을 수정해줘야 하며, 1번째 뎁스에 있는 파일들은 현재의 규칙을 벗어나면 안 됩니다.
> css, images 등 새로 폴더를 구성하여 페이지에 새로 추가할 리소스를 커스텀으로 관리할 수 있습니다.
```sh
public/
├── css/
│   ├── font.css
│   └── style.css
├── images/ # 이미지 폴더 복사/붙여넣기
│   └── oriental-flower.png
├── favicon.png # 교체(덮어쓰기)
├── index.html
├── logo192.png # 교체(덮어쓰기)
├── logo512.png # 교체(덮어쓰기)
├── manifest.json
└── robots.txt
```

##### css
공통스타일(common.css), 접근성스타일(a11y.css), 웹폰트 등 변경없이 앱에서 그대로 사용될 수 있는 css 는 정적리소스로 분류합니다. 이외의 css 는 React 컴포넌트의 동적리소스로 취급되므로 꼭 잊지 않도록 합니다!!

##### images
마찬가지로 변경없이 앱에서 그대로 사용되는 이미지들은 정적리소스로 관리됩니다. 하지만 svg 파일은 컴포넌트로 다루면 이득(애니메이션, 접근성)이 많기 때문에 일반적으로 public 정적리소스 폴더에 들어가지 않습니다.

##### favicon.png
즐겨찾기 및 탭에 보여지는 아이콘입니다. 원래는 .ico 확장자를 썼으나 최근에는 png 파일도 가능해졌다고 합니다.

##### index.html
웹 페이지가 처음으로 실행되는 곳으로, head 에서 여러 메타데이터와 정적리소스를 설정합니다. bundle 된 리엑트 스크립트가 배치되어 이곳의 actual DOM 에 렌더링됩니다. 또한 구글 analytics 도 추가할 수 있습니다. 어렵게 생각하지 않고, 그냥 하나의 html 에서는 무엇을 해야하는지 곰곰히 생각하면 될것 같습니다.
다음과 같은 head 정보는 알아둡시다.
```html
<html lang="ko-KR">
  <head>
    <meta charset="utf-8" />
    <title>배열(Array) 메서드를 활용한 React 앱</title>
    <meta http-equiv="X-UA-Compatible" content="ie=edge"> <!-- IE 대응 -->
    <meta name="viewport" content="width=device-width, initial-scale=1" />  <!-- 애플기기 뷰포트-->
    <meta name="description" content="자주 사용되는 배열(Array) 메서드를 React 앱에 녹여내는 학습을 진행합니다.">
    <meta name="theme-color" content="#FFB74D" /> <!-- 안드로이드 chrom 브라우저에서 상단바 색상변경 -->

    <!-- 정적 리소스 설정 -->
    <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" /> <!-- IOS 홈화면에 추가 -->
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.png" />
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" /> <!-- pwa 이 됐을때의 설정 manifest 위치 See https://developers.google.com/web/fundamentals/web-app-manifest/ -->
    <link rel="stylesheet" href="%PUBLIC_URL%/css/font.css">
    <link rel="stylesheet" href="%PUBLIC_URL%/css/style.css">
```

##### logo***.png
manifest.json 에서 읽히는 아이콘으로, PWA(Progressive Web App) 이 됐을 때 네이티브 환경에서 사용할 아이콘 입니다.

##### manifest.json
앱이 네이티브 환경에서 구동되는 PWA 가 될때 제공하는 정보입니다.
```json
{
  "short_name": "Array Methods App",
  "name": "배열(Array) 메서드를 활용한 React 앱",
  "icons": [
    {
      "src": "favicon.png",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    },
    {
      "src": "logo192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "logo512.png",
      "type": "image/png",
      "sizes": "512x512"
    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#FFB74D",
  "background_color": "#ffffff"
}
```
short_name : 사용자 홈 화면에서 아이콘 이름으로 사용  
name : 웹앱 설치 배너에 사용  
icons : 홈 화면에 추가할때 사용할 이미지  
start_url : 웹앱 실행시 시작되는 URL 주소  
display : 디스플레이 유형(fullscreen, standalone, browser 중 설정)  
theme_color : 상단 툴바의 색상  
background_color : 스플래시 화면 배경 색상  
orientation : 특정 방향을 강제로 지정(landscape, portrait 중 설정)

##### robots.txt
크롤링 방지차원 뭔갈 기록하는것 까진 아는데 자세히는 모릅니다. 나중에 차차 학습하기로 합니다.

#### 이번에 새로 알게된 테크닉
update hook 을 위해 setState를 사용하도록 하자. state 객체가 immutable 하니 값으로는 새로운 값을 대입. 그래서 map 이나 spread 연산을 통해 새로운 객체 참조값을 만들어 주는것! 모던js에서 값 추가할때 이렇게 기존배열 spread 하고 뒤에 추가하는것. 암기하자
```jsx
handleAddRandomUser = () => {
  this.setState({
    wealthList: [...this.state.wealthList, createRandomUser()],
    wealthTotal: null
  })
}
```

setState 함수에 인자가 prevState인 콜백을 받아 새롭게 변경할 state 를 리턴하는 코드.. 객체 바로리턴시 소괄호로 감싸는것 기억. 중괄호로 감싸면 함수본체로 인식한다.. 객체도 spread 가능하다..
```jsx
handleDoubleWealth = () => {
  this.setState(({ wealthList }) => ({
    wealthList: wealthList.map((person) => ({
      ...person,
      wealth: person.wealth * 2,
    })),
    wealthTotal: null
  }))
}
```

button 리스트도 이런식으로 state 로 관리합니다. 얘네들도 반복되는 데이터니.. Context 구성시에 핸들러 Provider 전달하겠단 핑계로 아예 다 버튼을 배열로 관리해버리는듯 한데 괜찮네요.
```jsx
buttonList: [
  {
    content: '자산가 추가',
    classes: 'add-user',
    // 컴포넌트의 이벤트 핸들러 메서드 연결은 다음과 같이 작성하면 됩니다.
    // this 참조는 핸들러 내에서 클릭했을 때 확인할 수 있습니다.
    onClick: this.handleAddRandomUser,
  }, {
    content: '자산 x2 증가',
    classes: 'doucle-wealth',
    onClick: this.handleDoubleWealth,
  }, {
    content: '5억원 걸러내기',
    classes: 'filter-500million-won',
    onClick: this.handleFiltering500millionWon,
  }, {
    content: '자산 순별 정렬',
    classes: 'sort-toggle-asc-desc',
    onClick: this.handleSortingToggle,
  }, {
    content: '자산 총액 계산',
    classes: 'calculate-wealth',
    onClick: this.handleCalculateTotalWealth,
  },
]
```

객체가 와야될 속성엔 jsx 중괄호 안에 일반적인 객체 복사 spread 넣을것. 태그안에 객체를 그냥 spread 하면 해당요소의 키-값쌍이 추가됨. props의 구체적 위치 지정 없이 그냥 React 요소에 설정한 속성처럼 속성의 위치변화를 원치 않으면 이런식으로 최상위 태그에 props를 spread 함 (나머지 props 딱히 둘곳없으면 최상위에 짬때리는것)
```jsx
<AppContext.Provider value={{...this.state}}>
------------------------------------------
<div className="container" {...props}>
```

위치 정할 속성은 구체적으로 할당해주고 나머지는 rest 연산자로 props 객체로 받아서 아까처럼 최상위에 짬때릴것
```jsx
render() {
  const { label, ...props } = this.props
```

이런건 걍 애교로 알아두기. 변수 세팅여부에따라 요소렌더링. && 로인한 값사용과 함수호출쓰임, || 로인한 디폴트값세팅 테크닉 js에선 여전히 쓰임. 여기는 파라미터가아니니 필요. 꼭외우자.. if 문이나 3항연산자 뻘짓하지말고.. **if문은 일단 배제하고 생각하기!!!!**
```jsx
{/* 자산 총액 */}
{
  total &&
  (
    <h3 className="subHeadline">
      <strong>자산 총액</strong>
      <strong>{filterCurrencyKR(total)}</strong>
    </h3>
  )
}
```

index.js 엔트리 파일에서 개발속도를 위해 serviceWorker unregister 돼있는데 배포용 & PWA 적용하려면 register 해야함. **우리는 node 환경에서 Web Browser 에 돌아갈 스크립트가 웹에서 잘 돌아가도록 서포트하는 프로그램을 개발하는 셈이라** node에서 쓰는 process.env.NODE_ENV 같은코드 사용 가능. ES10 표준으로 제안된 동적 import 기능으로 상단 import 필요없이 조건문 안에서 비동기로 한큐에 처리 가능.
```javascript
// PWA 웹앱 등록할 때 사용
if (process.env.NODE_ENV === 'production') {
  // ES 표준 제안된 동적 import() 방식 - ES10
  import('./serviceWorker').then((serviceWorker) => serviceWorker.register())
}
```

</div>
</details>

---------------------------------------

<details open>
<summary>2일차 학습 - 고차 컴포넌트(HOC)</summary>
<div markdown="1">

### 고차 컴포넌트 (HOC)
고차컴포넌트란, 컴포넌트를 인자로 전달받아 기능이 향상된 컴포넌트를 반환하는 **함수** 입니다. 함수호출 그 자체로 나오는 반환값, 함수자체를 통틀어 HOC 라고 일걷는 겁니다. 둘중에 뭐가 HOC 인가 생각하지 마세요. 에너지만 빠질 뿐입니다. 공식문서에도 함수라고 나와있고요, 애초에 HOC 는 함수임을 공리(axiom)로 암기해야 될것 같습니다.
```javascript
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

#### 고차컴포넌트의 쓰임
수업에서 한가지 배운 예시로는, Context API 를 간략하게 쓰기위한 contextType이 있는데 이마저 생략하도록 해줄 수 있는 것입니다.

HOC 에서 리턴할 클래스 내부 에서 대신 contextType 를 정의해주고 아래와 같이 context를 넘겨주고 props를 forwarding 해줍니다.
```jsx
import React, { Component } from 'react'
import LectureContext from '../context/LectureContext'

export default (Comp) => {
  return class extends Component {
    static contextType = LectureContext
    render() {
      return <Comp context={this.context} {...this.props} />
    }
  }
}
```
**주의** : HOC 를 사용하게 되면 HOC 에서 리턴하는 컴포넌트가 먼저 props를 받게 되므로, 꼭 render 메서드에서 리턴하는 컴포넌트에 props를 spread 해주어 forwarding 시켜줘야 합니다.

HOC 를 사용하려는 Lecturer 컴포넌트에서는 HOC 에 자기자신 컴포넌트를 전달해주면 됩니다. context 를 읽을 때는 props로 전달받았으므로 props에서부터 읽어줘야 합니다.
```jsx
import React, { Component } from 'react'
import withLecturerContext from '../hoc/withLecturerContext';

class Lecturer extends React.Component {
  render() {
    const { lecturer, context } = this.props
......
export default withLecturerContext(Lecturer)
```


</div>
</details>

---------------------------------------

<details open>
<summary>3일차 학습 - 폼 컨트롤</summary>
<div markdown="1">

### 폼 컨트롤
앞으로 다룰 폼 컨트롤 이라는 것을 다룰건데요, 컨트롤 이라는 용어를 처음 접합니다. 이 뜻을 잠시 짚고 가자면, input 요소와 textarea 같은 폼 엘리먼트가 React 에 결합되어 React 의 통제를 받는 것을 의미합니다. 이 통제를 받는 컴포넌트를 Controlled Component 라고 하지요. 폼 컨트롤 같은 경우에는 React 에 의해 사용자의 입력데이터가 업데이트 되고 유효성이 체크됩니다.

#### Uncontrolled Component VS Controlled Component
폼 엘리먼트에 value 속성의 정의여부에 따라 Uncontrolled Component, Controlled Component 가 구분됩니다. 

input 요소의 value 속성이 정의되지 않으면 이러한 컴포넌트는 **Uncontrolled 컴포넌트** 라고 합니다. 이 컴포넌트는 일반 input 요소처럼 값을 자유롭게 입력하고 submit 할 수 있습니다. 대신 React 의 통제를 받지 못하기 때문에 React 로부터 value 속성에 초기화 해야할 경우 문제가 생기지요.

그 문제를 value 속성 정의로 해결합니다. value 속성을 정의할 경우 이제 React 의 통제를 받기 시작하는데 이러한 컴포넌트를 **Controlled Component** 라고 부르기 시작합니다. value 속성에는 적절한 컨트롤을 위해 폼 엘리먼트 에 상응하는 state 가 들어가야 합니다. 만약 상수를 넣는다면 input 입력이 안되고 그 값으로 고정될 것입니다.

그 이유는, **React 에서의 폼컨트롤의 데이터 바인딩은 React에서 input 요소의 value 속성으로의 단방향 바인딩으로 이루어 지기 때문입니다.** value 속성을 정의했다는 것은, React 로부터 폼 엘리먼트의 값을 정했다는 것을 뜻합니다. 정한 값이 상수면 더이상 값이 변할 여지가 없게 되는데요,

- React 는 선언형 프로그래밍 방식을 채택하며, 데이터 중심이다
- props 는 단지 부모로부터 전달받는 읽기전용 값일 뿐이다
- state 가 컨트롤러와 연관된 실질적인 데이터. 상태를 관리한다

라는 원리에 따라 결국은 state를 바인딩 해야됩니다. 그 후 이벤트핸들러를 연결시켜 setState 로 input 값을 조작하는 것입니다.

#### 폼 컨트롤의 운영 방식
다음과 같이 폼 컨트롤 최상단에 state 와 이벤트 핸들러를 배치시키고, 필요한 경우 Form Element 를 컴포넌트로 따로 분리시킵니다. 다수의 Form Element 가 존재하는 경우는, 각 Element 에 name 속성을 부여하고 하나의 핸들러에서 name, value 속성을 뽑아내어 그것으로 state 를 변경하면 됩니다.
```jsx
import React, { Component } from 'react'

export default class MultiControlInputs extends Component {
  state = {
    // 사용자 등록 정보 객체 (이메일, 패스워드)
    register: {
      email: '',
      password: '',
    },
  }
  // 멀티 <input /> 핸들링 핸들러
  handleChange = (e) => {
    // 이벤트 타겟 객체로부터 name, value 속성 구조 분해 할당
    const { name, value } = e.target
    const { register } = this.state
    
    const register = {
    ...this.state.register,
    [name]: value
    }
    // register 상태 업데이트
    this.setState({ register })
  }
  render() {
    const { register } = this.state
    return (
      // Fragment
      <React.Fragment>
        <input
          type="email"
          name="email"
          aria-label="계정 이메일"
          value={register.email}
          onChange={this.handleChange}
        />
        <input
          type="password"
          name="password"
          aria-label="계정 패스워드"
          value={register.password}
          onChange={this.handleChange}
        />
      </React.Fragment>
    )
  }
}
```
> JSX 에서 label 요소 for 속성은 htmlFor 로 사용됩니다.


#### textarea 컨트롤
HTML textarea 요소는 사용자가 입력한 내용을 자식 텍스트 콘텐츠로 받습니다.
```jsx
<textarea>
  오늘 하루를 정리하는 글을 작성해봅시다.
</textarea>
```
하지만 React는  `value`  속성을 대신 사용합니다. 즉, 한 줄 입력을 사용하는 폼 컨트롤과 비슷하게 작성합니다.
```jsx
<textarea
  value={this.state.value}
  onChange={this.handleChange}
/>
```

#### select 컨트롤
HTML select 요소는 드롭 다운 메뉴를 화면에 렌더링 합니다.
```jsx
<select>
  <option value="react">React</option>
  <option value="redux">Redux</option>
  <option selected value="reactRouter">React Router</option>
</select>
```
HTML 드롭 다운 메뉴는 초기 활성화 값을 `selected` 속성을 사용해 처리하지만, React는 `value` 속성을 대신 사용합니다.
```jsx
<select
  value={this.state.value}
  onChange={this.handleChange}
>
  <option value="react">React</option>
  <option value="redux">Redux</option>
  <option value="reactRouter">React Router</option>
</select>
```

> ##### React 에서 멀티플 셀렉트 메뉴를 구성하려면?
> React 컨트롤 컴포넌트로 구성된 select 요소는 사용자에 의해 복수의 아이템을 선택 받을 경우 다음의 2가지가 요구 됩니다.
> 1. `multiple` 속성 값이 `true`
> 2. `value` 값은 Array
> ```jsx
> <select multiple={true} value={this.state.value} onChange={this.handleChange}>
> ```
> `value` 값을 배열로 업데이트 받아야 하기 때문에 초기 값을 배열로 설정한 후, 핸들러 내부에서는 사용자의 멀티 선택을 배열 데이터로 만드는 공정을 거쳐 새로운 배열을 할당하여 업데이트 합니다.
> ```jsx
> state = {
>   value: []
> }
> handleChange(e) {
>   // select > option 요소 수집 후 배열 데이터로 변경
>   const options = Array.from(e.target.children)
>   // 사용자가 선택한 option 필터링
>   const selectedOptions = options.filter(option => option.selected)
>   // 필터링 된 option.value 값을 아이템으로 하는 새로운 배열 반환
>   const selectedOptionsValue = selectedOptions.map(option => option.value)
>   // 상태 업데이트
>   this.setState({value: selectedOptionsValue})
> }
> ```


#### File 인풋 요소
파일인풋에는 뭔 파일을 넣어야 될지 모르기 때문에 value 속성을 바인딩하지 않으므로 Uncontrolled Component 로 분류된다. 따라서 DOM에 실제로 접근하여 컨트롤해야하는데, 그러기 위해서는 ref 속성을 사용해야 한다.
> 파일을 전송하려면 File API 를 사용해야 하는데 MDN 문서 잘 찾아보고 숙지하도록 하자.

#### Uncontrolled Component 접근법

1. 생성자에서 인스턴스 속성에 Ref 속성을 만들어줍니다. React 를 벗어난 컴포넌트이므로 state 로 관리되지 않습니다. 
```javascript
constructor(props) {
  super(props);
  // Ref를 사용해 DOM 요소 참조
  this.fileInput = React.createRef();
}
```

2.  다음과 같이 요소의 ref 속성에다 어느 ref 속성에 요소를 연결시켜줄지 정의해줘야 합니다.
```jsx
<input type="file" ref={this.fileInput} />
```

3. ref.current 속성을 통해 element 객체에 접근합니다.
```jsx
accessRef = () => {
  const span = this.refEl.current
  span.style.fontSize = '62px'
}
```

#### Dialog 폼컨트롤 개발방법
1. 상위컴포넌트에서 list data, isVisibleDialog, editingData 등의 state 를 관리한다
2. isVisibleDialog 는 Dialog 요소 렌더링 시 visible 여부 전달해줄 데이터다.
3. state를 조작할 removeData, editData(list 데이터조작), showDialog, hideDialog(editingData, isVisibleDialog 조작) 메서드를 미리 정의한다.
4. 데이터가 들어갈 컴포넌트를 렌더링한다. (수정, 삭제버튼을 컨텍스트에 연결된 상위컴포넌트의 메서드에 연결)
5. 다이얼로그를 렌더링한다. (props로 isVisibleDialog 전달.)
-  **static getDerivedStateFromProps() 로 editingData와 _previousData 를 비교하여 다이얼로그 초기값세팅**. state 를 바꾸면서 update 를 트리거하지 않기 때문에 완전 중요!!!!!
- 그 후 멀티인풋 폼컨트롤을 작성하면됨
- 접근성을위해 editingLecturer 가 null 일 경우 '데이터를 수정하고 있지 않다'는 태그를 심으면 좋음

</div>
</details>

---------------------------------------

<details open>
<summary>4일차 학습 - React Hook</summary>
<div markdown="1">

### React Hook
Functional Component 로 Class Component 의 능력을 흉내낼 수 있도록 해주는 기능입니다.

생명주기에서 Hook 을 들어본 적이 있는데요, 생명주기에서 상황을 걸고 넘어진다는 뜻에서 그런 단어가 선택되는 겁니다. Functional Component 에서도 마찬가지입니다. Functional Component는 render 시에 매번 호출됩니다. 매번 호출되어 내부적으로 다양한 동작을 수행하는데 함수라서 딱히 그걸 Hooking 할 수단이 없습니다. 그래서 Hook 이라는 것을 따로 만들어 Functional Component 내부에 Hook 함수를 넣어 특정 기능을 Hooking 하는 것입니다.

원래 리엑트 functional component는 render 시마다 매번 호출되므로 함수 내부에는 주로 props 를 구조분해 하는 상수나 이벤트 핸들러를 참조하는 상수 등의 즉석으로 할당되는 상수를 배치합니다. 매번 실행컨텍스트가 종료되는데, state나, Ref 참조, contextType 참조를 지속적으로 저장한다는 것은 상식적으로 불가능하죠. 게다가 클래스 컴포넌트가 아니라 함수이기 때문에 특정 사이드이펙트가 발생하는 시기를 Hooking 하는것은 절대 불가능 합니다.

React 에서는 이를 각각 useState(), useRef(), useCotext(), useEffect() 라는 Hook 을 통해 해결합니다. 그럼, 하나씩 살펴보도록 합시다.

<details close>
<summary>여기서 질문! (너무길어 흐름을 방해하는것 같아서 접어놨습니다) </summary>
<div markdown="1">

> Q) useState() 는 Functional Component 내에서 생성된 state의 참조를 유지하기 위해 존재한다 치더라도, useRef() 나 useContext() 는 외부 객체의 고정된 참조값을 유지할 뿐입니다. 이렇게 React Hook은 고정된 참조라도 유지하기 위한 노력을 펼치는데 Functional Component 내에 선언된 이벤트 핸들러 함수에 대한 참조 유지 노력은 왜 없나요? Functional Component 호출 시마다 선언되는 Overhead 가 있는것 아닐까요??
> 
> 내가생각하는
> A) 그럼 반대로 생각해 보세요. HOC 도움을 제외하고 생각하여, useRef()와 useContext() 아니면 Functional Component 내에서 Ref 와 Context 는 어떻게 참조할 것이죠? 그래서 저런 Hook 이 존재하는 것입니다. 존재하는 김에 참조를 유지시키는 것이고요.
>
>Q) 그럼 Functional Component 내의 이벤트 핸들러는 어떻게되는 것이죠?? 육안으로 봤을때 암만봐도 Overhead 가 있을것이란 찝찝함이 있는데, Functional Component의 내부적인 기능으로 이벤트 핸들러의 참조를 유지하는 기능이 제공되기라도 할까요??
>
>야무님답변
>A) useCallback을 사용하세요. 다른 Hook과 마찬가지로 초기렌더링 이후 callBack 함수를 쭉 기억합니다. 2번째 인자로 전달되는 의존상태가 변할때만 재정의됩니다.

</div>
</details>


#### useState : state 이용 함수
useState 함수로 state의 초기값을 전해주면 0, 1번째 요소에 각각 state, setState 함수가 들어간 배열이 리턴됩니다. Functional Component 가 계속 호출되도 state에 대한 참조는 유지됩니다. **Hook 에서의 State는 변화하는 단위대로 분리하여 관리해야 합니다.** Hook 에서의 setState 는 Class Component 와는 달리 전체값을 덮으므로 비동기인 setState 함수 여러번 호출시 예상치못한 문제를 낳을 수 있기 때문입니다.
```jsx
const Counter = props => {
  const [count, setCount] = useState(0)
  const [userInfo, updateUserInfo] = useState(props.userInfo || null)
  return (...)
}
```

#### useRef
실제 DOM 노드를 참조할 때 사용합니다. 마찬가지로 Functional Component 가 계속 호출되어도 참조를 계속 유지할 수 있게 됩니다. 

Class Component 에서 사용된 createRef 는 매번 새로운 참조를 생성합니다. 함수형 컴포넌트에서는 절대 사용하면 안되지요. 그리고 객체 특성상 멤버변수의 한 번 참조로 소멸없이 지속적으로 관리되기 때문에 굳이 useRef() 를 쓸 의미는 없어보입니다. 

> Q) Ref 가 실제 DOM 을 참조가능하다면, side effect 란 왜 존재하는 것? Ref 로 처리하면 될텐데.. File Input 과 같이 Controlled Component 로 관리하고 싶었으나 그렇게 되지 못했던 것들을 위해 특별히 따로 관리하기 대안으로 쓰이는 용도인가? 따로 관리 하지 않을 것이라면 side effect 가 발생하는 life cycle hook 에서 side effect 를 가볍게 처리하는 것인가?

```jsx
import React, { useRef } from 'react'

function FileInput(props) {
  // 실제 DOM 노드 참조(Ref.)
  const domFileInputEl = useRef(null)
  const domButtonEl = useRef(null)
  // 이벤트 리스너
  function handleSubmit(e) {
    e.preventDefault()
    console.log(`선택된 파일: ${domFileInputEl.current.files[0].name}`)
    domButtonEl.current.setAttribute('disabled', 'disabled')
    domButtonEl.current.innerText = '전송 됨'
  }
  // 렌더링
  return (
    <form onSubmit={handleSubmit}>
      <label>
        업로드:
        <input type="file" ref={domFileInputEl} />
      </label>
      <br />
      <button type="submit" ref={domButtonEl}>전송</button>
    </form>
  )
}
```

#### useEffect : 사이드이펙트 처리 함수
Hook 에서는 useEffect() 함수의 사용을 통해 마운트후(componentDidMount), 업데이트후(componentDidUpdate), 언마운트전(componentWillUnmount)의 side effect 처리를 흉내낼 수 있습니다. 조금 동작이 다르지만요. 왜 동작이 다를 수밖에 없었는지 납득할 준비를 하면서, 이제 다음 소스를 살펴봅시다.

```jsx
const Counter = (props) => {
  const [count, setCount ] = useState(Number(props.count) || 10)
  // 함수 몸체 내부에서 초기 1회 기억된 값을 사용하기 위한 상태
  const [pristine, setPristine] = useState(true)

  // 컴포넌트 생성 후, 마운트 된 시점 1회 실행됩니다.
  useEffect(() => {
    console.log('%c초기 1회 마운트 이후 시점의 사이드 이펙트 처리', 'font-weight: bold; color: #347edf')
    // 순수 상태 -> 오염 상태로 업데이트
    setPristine(false)
  }, []) // 빈 배열이 인자로 전달되어 최초 1회만 실행되고 맙니다.

  // 초기 렌더링 이후, 실행되는 조건 처리
  useEffect(() => {
    if (!pristine) {
      console.log('%c컴포넌트 업데이트 과정의 사이드 이펙트 처리', 'font-weight: bold; color: #2097a7')
    }

    // 사이드 이펙트 처리 이후 실행됩니다.
    return () => {
      console.log('%c사이드 이펙트 이후 실행되는 처리 과정', 'font-weight: bold; color: #dca050;')
    }
  },
    // count 의존 값이 변경될 때만 useEffect()가 실행됩니다.
    [count]
  )
}
```
useEffect 에 전달되는 콜백은 Functional Component 가 호출될때마다 매번 같이 호출되는데요, 사이드이펙트가 존재하는 시점에 호출됩니다. 콜백 안에서 리턴되는 콜백은 최초에는 호출되지 않습니다. 바깥콜백을 콜백1이라 하고 안에서 리턴되는 콜백을 콜백2라고 합시다. 최초 Functional Component 호출시 콜백1이 호출되고 콜백2는 리턴만 할 뿐이지 호출되진 않습니다. 다음 Functional Component 호출 시 호출되라고 예약하는 차원에서 리턴하는 것입니다. 그럼 다음 Functional Component 호출 시 이전에 리턴되어 예약된 콜백2가 먼저 호출되고 콜백1이 호출됩니다. Functional Component 가 unmount 되면 예약되었던 콜백2가 호출되는 것이고요.

따라서,

**최초 마운트 시**
콜백1->

**업데이트중 (N번 업데이트)**
(콜백2->콜백1->)* N번 호출

**언마운트 시**
콜백2 호출

형태의 호출양상을 보입니다. 위의 소스와 같이 mount와 update시 실행되는 코드는 pristine 이라는 상태로 분리함으로써 componentDidMount 와 componentDidUpdate 훅을 흉내낼 수 있었는데요, componentWillUnmount 가 흉내되어야 할 콜백2는 완전히 흉내내지 못하고 업데이트 중에도 호출되는것을 볼 수 있습니다. 이것은 매번 호출되는 Functional Component 의 한계상 어쩔수 없는 것이고요, Unmount 시에는 Cleanup을 위해 꼭 콜백2가 호출되도록 하기위해 업데이트중에 기약없는 Unmount 를 위해 매번 콜백2 예약하고 다음 사이클에 호출되도록 반복하는 것입니다. 뭐, 클린업 작업이 일어나고 업데이트가 일어나기 때문에 결과적으로 흐름을 끊길만한 요소도 없고 걱정할 필요도 없습니다. 단지 이러한 차이가 있구나~ 이해만 하고 결론적으론 componentDidMount, componentDidUpdate, componentWillUnmount 라고 생각하고 각 코드 섹션을 작성해도 된다는 것입니다.
>아, useEffect() 함수의 두번째 인자의 역할을 까먹었네요, 두번째인자는 변화를 감지할 객체를 정하는 역할을 수행합니다. 생략되면 매번 호출되는 것이고, 빈 배열이면 변화 감지할 객체가 없으니 최초 1회만 수행되는 것이고, 배열에 변화감지할 객체를 넣으면 객체의 참조값이 변하면 수행되는 것입니다. 즉, immutable 한 객체는 속성값이 변하여 새로운 immutable 객체가 교체될때인 경우이지요.

#### Hook 의 작성 규칙
Hook 은 컴포넌트가 렌더링 될 때마다 항상 동일한 순서로 Hook이 호출되는 것을 보장시키기 위해 Hook 작성 시 다음과 같은 규칙을 준수해야 합니다.
1. **무조건 React 함수 컴포넌트의 최상단에서 호출하세요.** 일반적인 Javascript 함수 내에서 호출하면 안됩니다.
2. 만약 **일반적인 Javascript 함수 내에서 호출했다면, 그 함수는 Custom Hook 이 되는 것입니다.** Custom Hook 도 Hook 이므로 React 함수 컴포넌트의 최상단에서 호출되어야 할 것입니다.

#### 커스텀 Hook
방금 살펴본 대로, 일반 Javascript 함수에서 Hook 을 호출할 경우 커스텀 Hook이 됩니다.  함수 내에서 기존의 Hook 을 호출하고 본인만의 로직을 겯들어 좀 더 향상된 Hook의 기능을 만듭니다. 때문에 전달인자는 어떨지 몰라도 리턴은 Hook 에 의해 생성된 객체를 반드시 리턴 하여 Functional Component 에서 Custom Hook 을 이용할 수 있도록 해야합니다.

##### Custom Hook 정의
```jsx
import React, { useState } from 'react'

// 사용자 정의(custom) 훅
function useCountDownStatus(count) {
  const [countStatus, setCountStatus] = useState('완료 전')
  if (count === 0) {
    setCountStatus('완료')
  }
  return countStatus
}

export default useCountDownStatus
```
countStatus 는 함수가 계속 호출되어도 그 참조를 계속 유지할 것입니다. 

##### Custom Hook 사용
```jsx
import useCountDownStatus from '../hooks/useCountDownStatus'
const CountDownStatusMessage = (props) => {
  const countStatus = useCountDownStatus(props.count)
  return countStatus === '완료' ?
    <div>완료 됨</div> :
    <div>카운트 다운 중...</div>
}
```

지금은 시간이 없어서 여기까지 공부하지만..
https://ko.reactjs.org/docs/hooks-custom.html 의 FriendStatus 예제를 통해 꼭 Custom Hook 에 대한 자세한 사용법을 익힙시다!! 꼭 필요할때 공부하도록 합시다.

#### useCallback
Class Component 의 이벤트 핸들러 등의 콜백함수를 Functional Component 에서도 사용하기 위함입니다. 아주아주 간단한 경우엔 Functional Component 안에서 그냥 내부함수 선언하거나 const 상수에 참조시킬수도 있습니다. 하지만 자주 렌더링되는 Functional Component 인 경우 성능상의 많은 비용을 야기할 수도 있습니다. 따라서 여느 Hook 처럼 콜백함수를 기억해놓는 역할을 수행하는 것입니다.

```jsx
const WeatherInfo = () => {
  // useCallback() 훅을 사용해 이벤트 핸들러 설정
  const handleGetWeatherInfo = useCallback((e) => {
    // 날씨 정보 가져오는 로직
  }, [/* 의존 상태 */])

  // 렌더링
  return <button type="button" onClick={handleGetWeatherInfo}>날씨 정보 가져오기</button>
)
```

의존상태가 변하면 재정의되고, 그렇지 않으면 함수가 그대로 쭉 저장됩니다.

</div>
</details>

---------------------------------------

<details open>
<summary>5일차 학습 - Styled Component : Start</summary>
<div markdown="1">

### Styled Component
Styled Components 라이브러리를 사용하여 Styled Component 를 만들면 기존에 inline style 적용 시 Javascript 객체를 사용해야 했던 불편함을 벗어나 이제 CSS 스타일 문법을 그대로 사용할 수 있게 됩니다.

>더불어
>
>- 요소에 조잡한 style 속성이 없어지고 동적으로 생성된 고유한 class로 CSS Style 관리
>- 벤더 프리픽스 필요 없이 표준 CSS만 신경써서 작성 가능
>- props 를 통한 동적 스타일링 가능
>
>등의 여러가지 유지보수에 유리하다는 장점이 있습니다.

#### 라이브러리 설치
```sh
npm i styled-components
```

#### Babel 플러그인
프로젝트 루트 위치의 babel.config.js 파일에 babel-plugin-styled-components 플러그인을 추가합니다.
```javascript
module.exports = {
  plugins: ['babel-plugin-styled-components'],
}
```
> Babel 플러그인은 필수는 아니나 legible class names, server-side rendering compatibility, smaller bundles, and more. 등의 장점이 있다고 합니다. 저들과 바벨 플러그인을 아직 잘 모르므로 현 시점에서는 그냥 플러그인을 설치하는 과정이 있구나~ 하고 넘어가겠습니다.

#### 사용법
간단합니다. 모듈 import 후 요소명과 동일한 속성을 get 하여 리턴받은 메서드를 Tagged Templates 문법으로 호출하면 됩니다.

이 메서드의 동작방식은 다음과 같습니다.
1. CSS Text 를 백틱 문자열 그대로 구성한다 (interpolation 위치도 그대로)
2. interpolation 에 들어갈 값은 파라미터 1개 에 값을 리턴하는 함수가 되어야 하며, 내부적으로 요소의 props 객체가 인자로 전달되어 결과를 얻은 후 그 값이 그대로 백틱 문자열에서 구성된대로 들어갈 것이다.

```jsx
import styled from 'styled-components';

const AppButton = styled.button`
  color: ${ props => props.reject ? '#f60' : '#06f' };
`

export default AppButton
```
```jsx
<AppButton reject>취소</AppButton> // color: #f60
<AppButton>취소</AppButton>        // color: #06f
```

</div>
</details>