# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------
## 4주차 질문
- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

## 4주차 대 주제를 작성합니다.

<details open>
<summary>1일차 학습 - React 팁</summary>
<div markdown="1">

### React 팁

인덱싱 차원에서 컨셉위주로 정리하고, 자세한건 문서를 찾는 방향으로 합니다.
- [js 절대경로 추가](#js-절대경로-추가)
- [sass 절대경로 추가](#sass-절대경로-추가)
- [SVG 컴포넌트](#svg-컴포넌트)
- [Sass 사용](#sass-사용)
- [스타일 모듈 활용](#스타일-모듈-활용)
- [CRA 설정 덮어쓰기](#cra-설정-덮어쓰기)
- [classNames 활용](#classnames-활용)
- [디버깅](#디버깅)
- [패키지 버전 확인](#패키지-버전-확인)
- [cra 업데이트](#cra-업데이트)
- [cra 외부 template](#cra-외부-template)
- [Polyfill 사용해야될때](#polyfill-사용해야될때)
- [호환성 검수](#호환성-검수)
- [CSS 초기화](#css-초기화)
- [Grid Layout](#grid-layout)
- [글로벌 변수 테크닉](#글로벌-변수-테크닉)
- [동적 import](#동적-import)
- [PWA (Progressive Web App)](#pwa-progressive-web-app)
- [프리 렌더링](#프리-렌더링)

#### js 절대경로 추가

원래 상대경로를 안쓰면 node_modules 만을 뒤졌으나 설정한 경로(주로 절대경로)도 뒤질수 있음.

jsconfig.json 파일을 프로젝트 루트에 추가한다.

```json
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
```

#### sass 절대경로 추가

프로젝트 루트에 .env 파일 추가하여 다음과 같이 세팅

```
# Sass 경로 설정
SASS_PATH=node_modules:src

# 커스텀 환경변수
REACT_APP_PRIVATE_KEY=react_private_key_23jkow@klfjsalkd
```

Node 상에서는 process.env 객체를 통해 접근해야하고, html 상에서는 %%을 감싸주어 접근한다. 커스텀 환경변수는 앞에 REACT*APP*을 꼭 붙여줘야 접근가능하다.

> - process.env.NODE_ENV : Node 상에서 개발상태인지 배포상태인지 확인
> - process.env.PUBLIC_URL : public 디렉터리 URL

#### SVG 컴포넌트

img 태그로 불러오는 대신 ReactComponent 로 불러온다면 svg 속성추가 및 스타일링이 가능하다.

```jsx
// SVG 로고 그래픽 파일을 React Component로 처리
import { ReactComponent as Logo } from './assets/logo.svg'
import './logo.css'

// SVG 로고 컴포너트 → React 요소
// <svg> 요소로 처리
const reactLogo = <Logo className="app-logo" />
```

> `import logo from './assets/logo.svg'`라고 불러오면 logo 에는 경로 string 만이 담겨진다.

#### Sass 사용

다음과 같이 node-sass 모듈 설치하면 프로젝트에서 pre-Processer 가 작동하기 시작하여 scss 파일도 import 가능하게 된다.

```sh
$ npm i node-sass
```

#### 스타일 모듈 활용

`*.module.css`, `*.module.scss` 파일을 만들어 import 하면 모듈에서 common.css, normalize.css 등과 같은 전체적용 스타일과의 class 충돌없이 컴포넌트만을 위한 스타일링이 가능해집니다.

```jsx
import React from 'react'
import A11Y from 'styles/A11Y.module.css'

const AppHeader = (props) => (
  <header className="app-header">
    <h1 className={A11Y.Hidden}>
      <abbr title="Cascading Style Sheets">CSS</abbr> 모듈을 사용하여 숨김 콘텐츠 처리
    </h1>
    ...
  </header>
)

export default AppHeader
```

A11Y 객체 속성에는 클래스만이 들어갑니다. 따라서 스타일모듈은 class 만으로 작성해야 합니다. 이렇게 임포트하면 css 소스 속 클래스, A11Y 속성값 모두 고유한 값이 생성되어 맞춰지기 때문에 전체스타일과 충돌날 일이 없는것입니다.

#### CRA 설정 덮어쓰기

create-react-app 은 Webpack, Babel, 기타 등등의 모듈이 합쳐진 모듈입니다. Webpack 옵션에서 Sass의 Sourcemap 기능을 추가하려면 react-scripts eject 명령어를 통해 모듈을 분리하여 설정을 진행해야 하는데 이는 한 번 수행하면 되돌릴 수 없다는 단점이 존재합니다. craco 모듈은 이런 불편함을 해결하기 위해 덮어쓰기 기능을 제공합니다.
설치법 및 사용법은 https://github.com/gsoft-inc/craco 에서 참고합시다.
Sass Sourcemap craco에서 안되는 문제 해결책 : https://github.com/facebook/create-react-app/issues/5707

#### classNames 활용

HTML Element 에 들어갈 CSS class 들을 한눈에 보기 쉽게 조건처리하여 결합해주는 유틸리티 함수입니다.

```
$ npm i classnames
```

다음과 같이 사용합니다. 인자가 falsy falue 가 들어가면 결합되지 않을 것입니다.

```jsx
import classNames from 'classnames'
classNames('foo', 'foo', 'bar') // => 'foo bar'
classNames('foo', { foo: false, bar: true }) // => 'bar'
```

스타일 모듈도 다를것 없습니다. 스타일모듈에 정의되는 동적 클래스를 바인딩 하는 작업이 추가될 뿐입니다.

```jsx
import classNames from 'classnames/bind'
import styles from './LecturerEditDialog.module.css'

var cx = classNames.bind(styles)
var className = cx('foo', ['bar'], { baz: true })
```

#### 디버깅

VSCode의 Chrome Debugger Extension 패키지를 설치하면 에디터 내에서 디버깅이 가능합니다. 사용법은 아래 주소를 참고하세요
https://create-react-app.dev/docs/setting-up-your-editor/#visual-studio-code

> 기본 디버깅은 npm start 후 시작해야하지만, test 디버깅은 npm start 필요없이 그냥 디버깅 시작하면 되는것 같았습니다. 나중에 다시 살펴봅시다.

#### 패키지 버전 확인

패키지 버전리스트를 확인해보고, 업데이트를 수행할 줄 알아야 합니다. 대화식 업데이트(interactive update) 도 있다고 합니다. 여기서 적어서 암기할 것이 아니고, 문서를 찾아 해결합시다. 지금 제가 필요한건 이런것이 있구나의 컨셉뿐인것 같습니다.

#### cra 업데이트

이미 cra 로 프로젝트 설치했다면 react-scripts 를 업데이트하여 최신버전 유지해야합니다. cra 에서는 한줄로 쉽게 가능합니다.

```
npm i react-scripts@latest
```

이것도 cra 문서에서 쉽게 찾을 수 있겠지요?

#### cra 외부 template

npmjs.com 이었나 그런 사이트 가서 가져오면 됩니다. 외부템플릿은 cra-template-\* 로 시작합니다. 한국버전으로 야무님이 cra-template-ko 만드셨고, cra-template-ko-craco 는 craco 환경설정이 추가된 템플릿입니다.

#### Polyfill 사용해야될때

Babel로 ES6+ -> ES5 변환은 어떻게 하겠지만 IE 의 경우는 지원 안하는 기능이 많아서 그마저도 허용 못하는 것이 많습니다. polyfill도 겯들여야 IE 호환 가능합니다. react-app-polyfill 모듈 설치하고 반드시 index.js 엔트리파일 1번째라인에 import 시켜주세요. (react-app-polyfill/ie9,10,11 버전구분, 9로 갈수록 번들사이즈 커질것.)

#### 호환성 검수

caniuse.com 에서 사용할 기능 등의 한국 브라우저 점유율 등을 고려해보고 package.json 에 browserlist query 를 짭니다. 그러면 그 쿼리에 맞춰 나오는 브라우저에 맞게 Javascript 코드가 출력됩니다. nix browserlist 하면 호환 브라우저 리스트를 확인할 수 있습니다.

#### CSS 초기화

요즘은 normalize.css 많이 사용합니다. 다운받고 CSS 엔트리파일에 1회 `@import-normalize;` 라고 한번 호출해주기만 하면 됩니다.

#### Grid Layout

야무님이 IE 에서까지 Grid 를 사용할 수 있게 만들어 놓으셨다고 합니다. 한번 찾아가서 공부해봅시다. 질문드려봅시다. 지금부터 Grid 를 사용해도 되는지.. 아직 전통적인 CSS 의 감도 제대로 못 깨우친 상태라..

#### 글로벌 변수 테크닉

eslint 에서 window 생략하고 setTimeout 등의 함수 부르면 빨간줄 생깁니다. `const { setTimeout, clearTimeout } = window 로 명시적 선언 하세요.

#### 동적 import

표준 제안된 문법으로, default export 포함 모든 export 를 속성으로 모은 객체를 resolve 하는 Promise 를 반환합니다.

#### PWA (Progressive Web App)

service worker 를 등록하면 PWA옵션(오프라인/캐시 우선 사용)이 활성화되어 기존 웹 페이지보다 안정적이고 빠른 모바일 환경이 제공됩니다. Native 환경에 추가가 가능하게 되며, HTTPS 에서만 동작합니다.

#### 프리 렌더링

react-snap 모듈을 사용하여 SPA 를 정적 HTML 로 프리 렌더링 합니다. Headless Chrome 을 사용해 모든 페이지를 변환/생성 합니다. 실제 DOM 요소 노드의 자식 노드 소유 여부를 확인 하여 hydrate() 또는 render()로 조건 분기 합니다. 이렇게하면 서버사이드렌더링 할 필요가 없어집니다.


</div>
</details>

---------------------------------------

<details open>
<summary>2일차 학습 - Styled Component 고급개념</summary>
<div markdown="1">


### Styled Components 고급개념
- [스타일 확장](#스타일-확장)
  - [Styled Component 확장](#styled-component-확장)
  - [일반 컴포넌트 확장](#일반-컴포넌트-확장)
- [컴포넌트 스타일 래퍼](#컴포넌트-스타일-래퍼)
- [가상 클래스/요소, 중첩 규칙](#가상-클래스요소-중첩-규칙)
- [정적/동적 props 할당](#정적동적-props-할당)
  - [attrs + style 작동 알고리즘](#attrs--style-작동-알고리즘)
- [믹스인 (Mixin)](#믹스인-mixin)
- [애니메이션](#애니메이션)
- [글로벌 스타일](#글로벌-스타일)
- [테마 (Theme)](#테마-theme)
  - [ThemeProvider](#themeprovider)
  - [테마함수](#테마함수)
  - [일반 컴포넌트에 테마 적용](#일반-컴포넌트에-테마-적용)

#### 스타일 확장
별거 없습니다. 그냥 기존의 컴포넌트를 확장하는 것입니다만, Styled Component 혹은 일반 Component 을 확장하는 것이 좀 차이가 있을 뿐입니다.

##### Styled Component 확장
그냥 생각하는대로 확장하면 됩니다.
```jsx
mport styled from 'styled-components'
import AppButton from './AppButton'

// AppButton 확장
export const AppFillButton = styled(AppButton)`
  border: 0;
  padding: 0.45em 0.95em 0.6em;
  background-color: ${ props => props.reject ? '#026' : '#06f' }
  color: ${ props => props.reject ? '#09f' : '#fff' }
  font-weight: 600
`
```

##### 일반 컴포넌트 확장
styled-components 에 의해 규정된 규칙인데요, 일반 컴포넌트로부터 확장된 스타일은 className 속성을 전달받거나 classnames 객체를 전달받은 Element 에만 적용됩니다. className 속성이란 CSS 에 있어서 중요한 요소이기 때문입니다.
```jsx
// Styled Components로 생성되지 않은 React 컴포넌트
// className 속성을 전달 받아야 스타일 확장이 됨
function Book({ className, children }) {
  return <div className={className}>{ children }</div>
}

// Book 컴포넌트 스타일 확장
// primary, radius, border 속성 조건 처리
const StyledBook = styled(Book)`
  padding: 0.8em;
  font: 16px/1 Verdana;
  border-radius: ${ props => props.radius || 0 };
  border: ${ props => props.border || '3px solid #06f' };
  color: ${ props => props.primary || '#06f' };
`
```
<details close>
<summary>className 을 전달받아야 하는 이유에 대한 고찰</summary>
<div markdown="1">
단순 styled Component는 props 를 전달하면 그 prop가 그대로 actual element에 전달됩니다. 하나의 element 를 확장하는 형태이기 때문에 하나의 요소에 prop를 forwarding 하는것에 대해 아무 변수나 제약사항이 없는 것이죠. 따라서 이대로 styled Component 를 계속 확장해도 actual element 에게 무리없이 props가 전달됩니다. 하지만 일반 Component 는 이야기가 다릅니다. actual element 를 내 입맛에 맞게 구성할 수 있으며 따라서 전달받은 props의 위치를 필히 정해줄 수밖에 없습니다. 이 중 className props 가 들어갈 위치는 명확히 정해줘야하는게 Styled Component 의 규칙입니다. actual Element 에 className={props.className} 을 정확히 명시해줘야 styled component 에 정의된 CSS가 적용됩니다. (props의 이름은 정확히 className이 되어야 합니다.)
보통은 최상위 element 에 className={props.className} 을 정의해줌이 옳겠지만 다른곳에 정의해줘도 되고 styled Component 의 CSS 가 그쪽에도 정의될 것입니다.
</div>
</details>

#### 컴포넌트 스타일 래퍼
네이밍이 조금 이해가 안가지만, 컴포넌트 정의 위에 컴포넌트 에서 사용되는 스타일 컴포넌트들을 분리없이 같은 공간에 쭉 정의해줬다는 의미에서 컴포넌트 스타일 래퍼라고 한 것이 아닐까 싶습니다.
```jsx
// 스타일 컴포넌트
const Container = styled.div`
  display: flex;
  align-items: center;
  justify-content: center;
  font: 14px/1 Verdana;
  color: ${ props => props.primary || 'hsla(220, 99%, 50%, 0.89)'};
`

// 컴포넌트
const Counter = ({ primary }) => (
  <Container>{/* ... */}</Container>
)

export default Counter
```

<details close>
<summary>`"컴포넌트와 스타일 컴포넌트를 별도 분리하지 않고 컴포넌트 내부에 스타일 컴포넌트를 정의하는 것이 유지 보수에 좋습니다."`</summary>
<div markdown="1">
=> 제 생각도 그렇습니다. 컴포넌트란 애초에 모듈단위의 재사용을 위해 만들어진 것이니.. 다른 프로그램에서 가져와 썼을 때 파일을 찾아 헤멜 일이 없어야 될 것이라고 생각합니다. 다른 컴포넌트에서 그 스타일을 쓴다면 그 쪽에서 다시 스타일 컴포넌트를 정의하는 한이 있더라도 말이죠. 하나의 컴포넌트 구성은 `컴포넌트, CSS in JS, 스타일모듈` 정도로 생각하면 될것 같습니다.
</div>
</details>

#### 가상 클래스/요소, 중첩 규칙
다음과 같은 방식으로 Sass 문법처럼 사용할 수 있다는 의미입니다. (내부적으로 stylis.js 사용)
```
const ControlButton = styled.button`
  :hover,
  :focus {
    color: #036;
    font-weight: 400;
  }
  &.increase::before {
    content: '📤'
  }
  span {
    user-select: none;
  }
`
```
> 왜 Sass 안쓰고 stylis.js 사용? Sass 는 Sass 텍스트 파일을 compile 해주는 컴파일러니까, JS 단에서 프로세싱 해주는게 필요했나보다.

#### 정적/동적 props 할당
styled component 의 attrs라는 생성자를 사용하여 props를 한 눈에 정의할 수 있습니다. props 를 속성으로 받아 attribute 의 집합 객체를 리턴합니다.
```jsx
const AppInput = styled.input.attrs(({ size, primary }) => ({
  // 정적 props
  type: 'text',
  // 동적 props 정의
  color: primary || '#06f',
  margin: size || '1em',
  padding: size || '1em',
}))`
  ${props => console.log(props) /* 전달된 props 확인! Console 패널 */}
  border: 2px solid ${ ({color}) => color };
  border-radius: 3px;
  margin: ${ ({margin}) => margin };
  padding: ${ ({padding}) => padding };
  color: ${ ({color}) => color };
  font-size: 1em;

  ::placeholder {
    color: ${ ({color}) => color };
  }
`
```

##### attrs + style 작동 알고리즘
style 을 설정하고 attrs 생성자를 통해 속성들을 한 눈에 보게 했는데요, 만약 스타일 확장 했을때 최종적으로 어떻게 동작하는지? 궁금하기 마련입니다. 아래와 같이 정리해 보았습니다.
![attrs + style 메카니즘](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/4-2-1.png?raw=true)
먼저 attrs 생성자에 props 가 전달되고요, 콜백 내부에서 setState 와 같이 덮어쓸 속성을 정의합니다. 그렇게 재구성해서 최종적으로 생겨난 props를 바탕으로 css 가 적용되고, 그 props 가 부모 컴포넌트의 attrs 생성자에 전달되어 props의 재구성이 일어나고 그것을 바탕으로 부모의 css 가 적용되는 것입니다. css 는 확장된 컴포넌트의 css 가 우선 적용 됩니다.

#### 믹스인 (Mixin)
css 모듈로 구현합니다.
Sass 에서도 Mixin 이라는 문법이 있는데요, Styled Component 에서도 이 기능이 있습니다. Sass Documentation 에서는 Mixin 을 코드를 재사용하기 위함이라고 적혀있습니다. 이처럼 Styled Component 의 Mixin 도 같습니다. 단순 코드 재사용으로써 상속보다는 조금 범용적인 의미입니다. (함수 정도로 생각하면 될듯)
```jsx
import styled, { css } from 'styled-components'

// CSS 믹스인
const boxMixin = css`
  margin: 20px 10px;
  border: 0;
  padding: 1em;
  font-size: 15px;
  font-weight: bold;
  line-height: 1.7;
`

// Box 컴포넌트 ⬅ CSS 믹스인
const Box = styled.div`
  ${ boxMixin };
  background: #07f;
  color: #fff;
`

// ShadowBox 컴포넌트 ⬅ CSS 믹스인
const ShadowBox = styled.div`
  ${ boxMixin };
  background: #41b883;
  color: #fff;
  box-shadow: 0 6px 8px 1px rgba(0,100,30,0.35)
`
```
>javascript에서 배웠던 mixin 패턴은 두개 이상의 객체의 능력을 합치고, 만약 속성이 중복되면 뒤에있는 객체의 속성으로 덮어쓴다는 개념으로 쓰였다. 그러나 Sass 의 mixin Documentation을 보니 코드 재사용을 위함이라고 적혀있다. CSS 세계의 mixin이란 코드재사용을 의미하는것갈다. 그럼 스타일 확장은 무슨 의미로 써야할까? 의미적으로 컴포넌트의 계층구조를 형성하기 위함이라고 생각한다. 이에반해 mixin은 단지 필요한 기능을 가져다 쓰는것 뿐이다. 내 생각엔 객체지향 용어로 봤을 때 mixin이 Interface Implementation 이고 스타일 확장은 Class Inheritance 이다.

#### 애니메이션
keyFrames 라는 모듈로 동작을 따로 정의합니다. CSS 내부에서 쓰기엔 길이가 너무 길어 제한시켰을까요.

정의한 keyFrames 를 css 에 interpolate 시키면 됩니다.
```jsx
// keyframes, css 불러오기
import styled, { keyframes } from 'styled-components';

const rotateKeyframes = keyframes`
  0% { transform: translateY(0) }
  25% { transform: translateY(-20px) rotate(20deg) }
  50% { transform: translateY(10px) }
  75% { transform: translateY(-15px) rotate(-20deg) }
  100% { transform: translateY(0) }
`

// 마법 모자 animation 속성에 rotate 값 설정
const MagicHat = styled.div`
  font-size: 100px;
  animation: ${rotateKeyframes} 3s infinite cubic-bezier(0.35, 0.29, 0.4, 0.8);
`
```

#### 글로벌 스타일
createGlobalStyle 모듈로 글로벌 스타일을 정의하는 컴포넌트를 만듭니다. JSX 어디든 추가되기만 하면 head 요소에 internal style sheet 로 추가되어 Global 한 스타일이 적용됩니다.
```jsx
import styled, { createGlobalStyle } from 'styled-components'

const GlobalStyle = createGlobalStyle`
  body {
    margin: 0;
    font: 1rem/1.5 "Spoqa Han Sans", Sans-Serif;
    background: ${ ({darken}) => darken ? '#162442' : '#dee1e6' }
    color: ${ ({darken}) => darken ? '#dee1e6' : '#162442' }
  }
  a img {
    border: 0;
  }
`
```

#### 테마 (Theme)
모든 컴포넌트의 스타일을 일괄적으로 변경(테마 기능) 하기 위해 도입된 props 속성인데요, 때문에 다른 props 속성과 달리 명시적으로 정의하지 않아도 항상 정의되어있습니다. undefined 가 아니라는 이야기입니다.

이 외에 다른 props 속성과 비교하면 별다른 차이가 없어보입니다. ThemeProvider 컴포넌트가 쓰이기 전 까지는 말이죠.

##### ThemeProvider
```jsx
import { ThemeProvider } from 'styled-components'
// 테마
import theme from './theme';

<ThemeProvider theme={theme.lightMode}>
  <Wrapper>
    <WrapperHeading>Light Mode</WrapperHeading>
    <AppButton>AppButton I</AppButton>
    <AppButton>AppButton II</AppButton>
  </Wrapper>
</ThemeProvider>
```
이런식으로 ThemeProvider 컴포넌트에 theme 를 정의하여 넘겨주면 ThemeProvider 에 포함되어있는 컴포넌트 모두 props.theme 속성이 세팅 됩니다. 실질적으로 ThemeProvider 에 의해 theme 속성 사용이 시작되는 것입니다. 이 theme을 바탕으로 어떻게 스타일링 할 지는 개발자에게 달렸지요. 아주 두근두근합니다.

##### 테마함수
**ThemeProvider 의 자식 컴포넌트에 함수를 전달하면** 테마함수로서 ThemeProvider 에 정의된 theme 을 전달받아 추가적인 처리를 하게 됩니다.
```jsx
const invertTheme = ({ fgColor, bgColor }) => ({
  fgColor: bgColor,
  bgColor: fgColor,
})
<ThemeProvider theme={theme.darkTheme}>
  <AppButton theme={invertTheme}>테마 반전</AppButton>
</ThemeProvider>
```

##### 일반 컴포넌트에 테마 적용
`withTheme` 고차 컴포넌트(HOC) 를 사용합니다.
styled Component 와 마찬가지로 props에 theme 을 받을 수 있게 됩니다. 이후부턴 일반 컴포넌트 답게 theme 을 알맞게 요리해야겠지요.

저는 그 요리시점을 componentDidMount도 componentDidUpdate도 아닌 `getDerivedStateFromProps`로 잡았습니다. 일단은 sideEffect 가 발생하는 Did 두 Hook 은 뭔가 어색해 보입니다. 렌더링 마치고 또 update 를 트리거한다? 뭔가 비효율적인것 같은 생각이 들었습니다. DidMount는 또한 Mount 되면 다신 호출되지 않고요, componentDidUpdate 는 맨 처음 생성될때 실행되지도 않습니다. 이에반해 `getDerivedStateFromProps`는 매 라이프사이클에 실행되고, 렌더링 되기 전에 props.theme 을 캐치할 수 있어 제가 생각하는 '한 번에 렌더링 & 업데이트마다 theme 즉각 적용' 이 실현될 수 있습니다.
```jsx
import React, { Component } from 'react'
import PropTypes from 'prop-types'
import { withTheme } from 'styled-components'

class AppInput extends Component {
  state = {
    style: {
      border: '2px solid currentColor',
      padding: '0.6em 0.8em',
      borderRadius: '3px',
    },
  }

  static getDerivedStateFromProps(props, state) {
    const { theme } = props
    // withTheme 고차 컴포넌트를 사용하면
    // 상위 영역에 설정된 테마를 전달 받습니다.
    const themeStyle = {
      color: theme.fgColor,
      borderColor: theme.fgColor,
    }
    if (theme) {
      return {
        style: {
          ...state.style,
          ...themeStyle,
        },
      }
    }

    return null
  }

  render() {
    const { theme, type, label, placeholder } = this.props
    return (
      <input
        type={type}
        placeholder={placeholder}
        aria-label={label}
        style={this.state.style}
      />
    )
  }

  static propTypes = {
    type: PropTypes.string,
    label: PropTypes.string.isRequred,
    placeholder: PropTypes.string,
    theme: PropTypes.object,
  }
  static defaultProps = {
    type: 'text',
    placeholder: '',
    theme: {},
  }
}

// export default AppInput
export default withTheme(AppInput)
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