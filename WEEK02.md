# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------
## 2주차 질문
- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

- Q. 여기에 질문내용을 작성합니다.
  ```
    A.여기에 답변 내용을 작성합니다.
  ```

## React 컴포넌트, 이벤트 처리, 컴포넌트 통신, 접근성

<details open>
<summary>1일차 학습 - 컴포넌트 & 전달 속성(props)</summary>
<div markdown="1">

### 전달 속성 (props)
React Element 에 전달되는 커스텀 속성입니다.
```jsx
<BaseButton type="reset">
  <span className="a11y-hidden">초기화</span>
  <span className="icon icon-reset" aria-hidden="true"/>
</BaseButton>
```
전달속성을  이렇게 하면 BaseButton 컴포넌트에 props 라는 이름의 객체로 아래와 같이 전달됩니다.
```jsx
{
  type: 'reset',
  children: [<span>, <span>],
}
```
컴포넌트의 속성으로 전달 시 속성명 그대로 전달되고, 하위 Element 로 전달시엔 children 이라는 이름으로 Element 의 배열로 전달됩니다.

### 함수형 컴포넌트
props 를 컴포넌트의 파라미터로 바로 받고 React 요소를 return 문을 통하여 반환합니다. (
```jsx
import React from 'react'

function BaseButton(props) {
  return (
    <button type={props.type || 'button'} className="base-button">
      {props.children}
    </button>
  )
}

export default BaseButton
```
> props.children은 현재 컴포넌트가 사용될 때 자식으로 또다른 Element 를 정의했을 경우 세팅됩니다.

### 클래스 컴포넌트
React.Component 를 상속한 클래스이며, render() 메서드로 React 요소를 반환합니다. props 는 생성자에 의해 this.props에 세팅됩니다.
```jsx
import React, { Component } from 'react'

class BaseButton extends Component {
  constructor(props) {
    super(props)
  }
  
  render() {
    const { type, chilren } = this.props
    return (
      <button type={type} className="base-button">
        {chilren}
      </button>
    )
  }
}

export default BaseButton
```
> props 를 비구조화하는 테크닉 잘 사용하니 숙지하도록 합시다. constructor 메서드는 super(props) 만 있는 형태라면 없어도 똑같이 잘 동작합니다. 하지만 constructor 메서드는 있는데 super(props)가 없으면 컴포넌트가 제대로 초기화되지 못해 에러를 발생시킵니다.

### PropTypes
React 요소가 받아들이는 prop 의 타입을 제한하는 패키지 입니다.

#### 설치방법
```
$ npm i prop-types
```

#### 함수 컴포넌트에 적용
```jsx
import React from 'react'
import PropTypes from 'prop-types'

const Worker = ({ name, career, onCareerUp, isLeave }) => (
  <div className="worker">
    <span classNme="worker-name">{name}</span>
    <span classNme="worker-career">{career}</span>
    <span classNme="worker-isLeave">{!isLeave || '재직'}</span>
    <button type="button" onClick={onCareerUp}>커리어 업</button>
  </div>
)

// 전달 속성 유효성 검사
Worker.propTypes = {
  name: PropTypes.string.isRequired,
  career: PropTypes.number.isRequired,
  onCareerUp: PropTypes.func.isRequired,
  isLeave: PropTypes.bool.isRequired,
}

export default Worker;
```

#### 클래스 컴포넌트에 적용
클래스 필드를 활용하거나 함수형 컴포넌트처럼 컴포넌트에 propTypes 속성을 추가하여 적용할 수 있습니다.
```jsx
import React, { Component } from 'react'
import PropTypes from 'prop-types'

class Worker extends Component {
  // 전달 속성 검사
  static propTypes = {
    name: PropTypes.string.isRequired,
    career: PropTypes.number.isRequired,
    onCareerUp: PropTypes.func.isRequired,
    isLeave: PropTypes.bool.isRequired,
  }
  
  render() {
    const { name, career, onCareerUp, isLeave } = this.props
    return (
      <div className="worker">
        <span classNme="worker-name">{name}</span>
        <span classNme="worker-career">{career}</span>
        <span classNme="worker-isLeave">{!isLeave || '재직'}</span>
        <button type="button" onClick={onCareerUp}>커리어 업</button>
      </div>
    )
  }
}

// Worker 컴포넌트에 전달된 속성 props 유효성 검사 설정
Worker.propTypes = {
  name: PropTypes.string.isRequired,
  career: PropTypes.number.isRequired,
  onCareerUp: PropTypes.func.isRequired,
  isLeave: PropTypes.bool.isRequired,
}

export default Worker;
```

#### PropTypes 검사방법
야무님이 강좌에 잘 정리해주셨지만, 개발자는 본인이 스스로 documentation을 찾고 읽을 줄 알아야 한다. 사고과정은, 일단 react proptypes documentation 을 구글에 검색해보자 -> prop-types 는 npm 패키지였지? -> npm 관련 패키지 documentation 을 찾는다. 링크는 아래와 같다.
https://www.npmjs.com/package/prop-types

나머지 타입체크 식상하다. 나중에 필요할때 찾고 어려운것 정도만 한번 이해하고 넘어가자.
```jsx
MyComponent.propTypes = {
  // You can ensure that your prop is limited to specific values by treating
  // it as an enum.
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),
 
  // An object that could be one of many types
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),
 
  // An array of a certain type
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),
 
  // An object with property values of a certain type
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),
 
  // You can chain any of the above with `isRequired` to make sure a warning
  // is shown if the prop isn't provided.
 
  // An object taking on a particular shape
  optionalObjectWithShape: PropTypes.shape({
    optionalProperty: PropTypes.string,
    requiredProperty: PropTypes.number.isRequired
  }),
 
  // An object with warnings on extra properties
  optionalObjectWithStrictShape: PropTypes.exact({
    optionalProperty: PropTypes.string,
    requiredProperty: PropTypes.number.isRequired
  }),
 
  requiredFunc: PropTypes.func.isRequired,
 
  // A value of any data type
  requiredAny: PropTypes.any.isRequired,
 
  // You can also specify a custom validator. It should return an Error
  // object if the validation fails. Don't `console.warn` or throw, as this
  // won't work inside `oneOfType`.
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },
 
  // You can also supply a custom validator to `arrayOf` and `objectOf`.
  // It should return an Error object if the validation fails. The validator
  // will be called for each key in the array or object. The first two
  // arguments of the validator are the array or object itself, and the
  // current item's key.
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};
```

### defaultProps
props에 기본값을 지정해준다.

#### 함수 컴포넌트
```jsx
import React, { Component } from 'react'

const Worker = ({ name, career, onCareerUp, isLeave }) => (
  // ...
)

// Worker 컴포넌트 전달 속성 기본 값 설정
Worker.defaultProps = {
  name: '야무',
  career: 21,
  onCareerUp: () => console.log('커리어 업!!'),
  isLeave: true,
}

export default Worker;
```

#### 클래스 컴포넌트
```jsx
class Worker extends Component {

  // 전달 속성 검사
  static propTypes = {
    name: PropTypes.string.isRequired,
    career: PropTypes.number.isRequired,
    onCareerUp: PropTypes.func.isRequired,
    isLeave: PropTypes.bool.isRequired,
  }

  // 기본 값  설정
  static defaultProps = {
    name: '야무',
    career: 21,
    onCareerUp: () => console.log('커리어 업!!'),
    isLeave: true,
  }

  // ...
}
```

#### 코드 줄이기 팁
```PropTypes.arrayOf(PropTypes.number)```  을 줄이고 싶으면 import { arrayOf, number } from 'prop-types' 로 모듈 임포트하고 arrayOf(number) 라고 축약하면 됩니다.

</div>
</details>

---------------------------------------

<details open>
<summary>2일차 학습 - state & 라이프 사이클 훅</summary>
<div markdown="1">

### state
컴포넌트의 내부에서 사용되는 말그대로 컴포넌트의 **상태**입니다. 따라서 컴포넌트를 최초 생성할 때 전달되는 일회성의 읽기전용 속성인 props 와는 달리 값을 얼마든지 변경하여 컴포넌트 자체의 상태를 변경할 수 있습니다.

#### 함수형 컴포넌트에서의 state
함수형 컴포넌트는 기본적으로 자신만의 상태와 라이프사이클 훅(LifeCycle hook)이 없습니다. 따라서 따로 변수를 만들어 state비숫한 형태로 관리합니다.
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
>React v16.7 의 훅(Hook) 이라는 것을 이용하면 state 와 LifeCycle hook을 구성할 수 있다고 합니다.

#### 클래스 컴포넌트에서의 state
state 설정이 가능합니다. 2가지 방법이 있습니다.

##### 1. 표준을 이용한 방법
```jsx
import React from 'react'

class GlobalNav extends React.Component {
  // 생성자
  constructor(props) {
    super(props)
    this.state = {
      // 컴포넌트만 접근 가능한 데이터 객체
      activeIndex: 0,
    }
  }
  // 렌더
  render() {
    return (
      <nav 
        className="global-nav"
        dataActiveIndex={this.state.activeIndex}
      >
        {props.children}
      </nav>
    )
  }
}
```

##### 2. 표준명세에 제안된 문법이지만 아직 채택되지 않은 방법
ClassField 로 정의하는 것입니다. 표준은 아니지만 Babel 의 도움을 받아 사용 가능합니다.
```jsx
import React, { Component } from 'react'

class GlobalNav extends Component {
  // 컴포넌트만 접근 가능한 데이터 객체
  state = {
    activeIndex: 0,
  }
  // 렌더
  render() {
    return (
      <nav 
        className="global-nav"
        dataActiveIndex={this.state.activeIndex}
      >
        {props.children}
      </nav>
    )
  }
}
```

### state 교체방법
React 에서의 객체는 기본적으로 immutable 합니다. state 도 예외는 아니며 따라서 state 수정을 위해서는 수정된 state 를 재할당 하는 방법밖에 없는데, setState() 메서드가 그 역할을 합니다.
```jsx
this.setState({
  activeIndex: 10,
})

```
필요하다면  `state`를 업데이트 한 후, 콜백(Callback) 함수를 실행하도록 처리할 수 있습니다.

```jsx
this.setState(
  { activeIndex: 10 }, 
  () => console.log('state.activeIndex가 업데이트 되었습니다.')
)

```

또는 다음과 같이 setState() 메서드에 함수를 전달하는 문법을 사용할 수도 있습니다.

```jsx
this.setState((prevState, props) => {
  const changedIndex = props.changeIndex * 2
  return {
    activeIndex: prevState.activeIndex + changedIndex,
  }
})

// 또는

this.setState(preveState => ({
  activeIndex: prevState.activeIndex + 9,
}))
```

### 라이프 사이클 (Life Cycle)
컴포넌트는 **```마운팅(Mounting, 탄생) → 업데이팅(Updating, 성장) → 언마운팅(Unmounting, 죽음)```** 등 크게 세 단계의 생명주기를 갖는데, 더욱 세부적으로 특정 시간에 코드를 실행하는 다양한 라이프 사이클 훅이 제공됩니다.
![how to use formatting toggle](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/2-2-1.png?raw=true)

> 각 phase 를 숙지한 후, Pre-commit phase 가 어떤 방식으로 사용되는지 눈여겨 봅시다.

#### 1. 마운팅
다음과 같은 이유로 다음과 같은 순서대로 훅이 호출된다.
|라이프 사이클 훅  | 설명 |
|--|--|
| constructor() | 컴포넌트 생성시 호출 |
| static getDerivedStateFromProps() | props 로부터 비롯된 state 라는 뜻으로, **새로운 props** 가 들어올 경우 state 를 조정할 때 사용. **조정할 state 속성:값 객체를 리턴** |
| render() | 컴포넌트 렌더링 시점에 호출. 하위 React 컴포넌트의 constructor() 를 호출한다. |
| componentDidMount() | 실제 DOM 에 마운트 된 이후 시점에 호출. **DOM 조작이 가능하다** |

```jsx
class LifeCycleHook extends Component {
  // 1.1 컴포넌트 생성
  constructor(props) {
    super(props)
    console.log('컴포넌트 생성')
  }
  // 1.2 전달된 속성, 상태를 가져와 설정
  static getDerivedStateFromProps(props, state) {
    console.log('전달된 속성 및 상태를 가져와 설정')
  }
  // 1.3 컴포넌트 렌더링
  render() {
    console.log('컴포넌트 렌더링')
    return <div />
  }
  // 1.4 컴포넌트 마운팅 됨
  componentDidMount() {
    console.log('컴포넌트 마운팅 됨')
  }
}
```

##### getDerivedStateFromProps 은 왜 static 메서드인가?
이전에 componentWillReceiveProps 라는 생명주기 훅이 있었는데 사이드이펙트 관련하여 문제가 있었다고 한다. 이에 대체하여 생긴 훅인데, 사이드이펙트 보완을 위해 static 메서드로 했다고 한다. 자세한 사항은 내 레벨도 아니고 시간낭비라 일단 패스!!

##### render 메서드 안에서의 자식 컴포넌트
render 내에서 반환하는 JSX 는 React.createElement 로 생성된 자식 컴포넌트로 이루어져 있다. React.createElement 는 컴포넌트의 constructor를 호출하고 render 하는 일련의 과정을 내포하기 때문에 componentDidMount 가 호출되기 전에 자식컴포넌트의 마운트 생명주기 훅이 반복적으로 호출되는 것이다. 그렇게 모두 끝나 실제 DOM 에 마운트 된 경우, 가장 마지막 render 를 수행한 자식부터 componentDidMount 훅을 역순으로 호출하게 된다.

##### componentDidMount 의 side-effect(부작용)
componentDidMount 는 실제 DOM 생성 이후에 호출되므로 실제 DOM 에 접근 가능하다. 본래 React 는 props, state에 의존하여 사용자화면을 렌더링 하는데 그 사용의미가 있다. 오류가 있어도 React 의 손아귀에 있기 때문에 React 에 의해 paused, aborted, restarted 되기 때문에 pure하고 side-effect가 없다. 하지만 componentDidMount 내부에서 실제DOM 을 조작한다면 이는 React의 관리 영역을 벗어난 것이므로 side-effect가 생겨 예측할 수 없는 프로그램이 된다. **따라서 componentDidMount 내에서의 DOM 조작은 가급적 많이 사용하지 말고 최대한 React 차원에서 끝낼 것을 권장한다.**
> update 주기때 render() 메서드 안에서도 실제 DOM 에 접근은 가능한것 같다. 하지만 이는 실제 DOM 에 반영되기 이전의 옛것의 DOM 이므로 render 메서드 에서 DOM 에 접근하는건 아무 의미가 없는듯 싶다. render() 취지에 맞지도 않고 그러라고 만들어지지도 않는 것이라고 생각든다.

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