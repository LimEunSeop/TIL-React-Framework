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
<summary>2일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>
