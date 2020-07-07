# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------
## 3주차 질문
- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

## React 훅, 폼, 고차 컴포넌트, 스타일 컴포넌트 그리고 미니 프로젝트 {E1}

<details open>
<summary>1일차 학습</summary>
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

index.js 엔트리 파일에서 개발속도를 위해 serviceWorker unregister 돼있는데 배포용 & PWA 적용하려면 register 해야함. 우리는 node 환경에서 개발중이라 process.env.NODE_ENV 같은코드 사용 가능. ES10 표준으로 제안된 동적 import 기능으로 상단 import 필요없이 조건문 안에서 비동기로 한큐에 처리 가능.
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
<summary>2일차 학습</summary>
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
<summary>3일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>
