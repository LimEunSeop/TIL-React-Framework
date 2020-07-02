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

#### 2. 업데이팅
```props``` 또는 ```state``` 가 변경될 경우 다음과 같은 순서로 발생합니다.

|라이프사이클 훅| 설명 |
|--|--|
| static getDerivedStateFromProps() | render() 호출직전에 호출. 마운트 표 설명 다시 정리해야될듯 |
| shouldComponentUpdate() | 컴포넌트 렌더링 이전에 호출 (최적화를 위해 update 할지안할지 결정가능) |
| render() | 컴포넌트 렌더링 |
| getSnapshotBeforeUpdate() | 컴포넌트 업데이트 전 스냅샷 가져와서 리턴하여 componentDidUpdate()에 전달. **실제DOM 접근가능** |
| componentDidUpdate() | 컴포넌트 업데이트 후 호출. **실제DOM 접근가능** |

```jsx
class LifeCycleHook extends Component {
  // 2.1 속성, 상태 설정
  static getDerivedStateFromProps(props, state) {
    console.log('(업데이트) 전달된 속성 및 상태를 가져와 설정')
    return null
  }
  // 2.2 업데이트 할 예정
  shouldComponentUpdate(nextProps, nextState) {
    console.log('성능 최적화 용도로 사용 됨')
    return true // false를 반환할 경우 컴포넌트 렌더링이 취소
  }
  // 2.3 렌더링
  render() {
    console.log('(업데이트) 렌더링')
    return <div />
  }
  // 2.4 DOM에 커밋되기 전
  getSnapshotBeforeUpdate(nextProps, nextState) {
    console.log('DOM에 커밋되기 전 스냅샷 가져오기')
    return null // 여기에서 스냅샷객체 리턴
  }
  // 2.5 업데이트 됨
  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log('업데이트 됨')
  }
}
```
> getSnapshotBeforeUpdate 는 업데이트 되기 전의 DOM 에 접근하는 것입니다. 스냅샷이라는것이 이것과 관련있는 것일까요? 스냅샷 활용이 이쪽에서 이루어지는 것일까요??

#### 3. 언 마운팅
컴포넌트가 **DOM에서 제거될 때** 실행되는 훅입니다.

|라이프 사이클 훅| 설명 |
|--|--|
| componentWillUnmount() | 컴포넌트 제거 예정 시점에 호출 |
```jsx
class LifeCycleHook extends Component {
  // 3.1 DOM에서 언 마운트 되어 제거되기 직전에 실행
  componentWillUnmount() {
    console.log('DOM에서 언 마운트 되어 제거되기 직전에 실행 됨')
  }
}
```
부모 컴포넌트에서 rendering 수행 후 diffing 을 통해 자식컴포넌트가 제거되는것이 결정되면 그 자식컴포넌트의 componentWillUnmount 훅이 실행됩니다. (먼저 getSnapshotBeforeUpdate이 실행되긴 합니다.) 그 후 부모가 DOM 업데이트를 마치면 componentDidUpdate 훅이 실행되는 것입니다. Unmount 는 Mount 의 반대어로, 마찬가지로 실제 DOM 과 관련돼있습니다. 따라서 Update 되기 전 마지막에 실행되는 것입니다. 이 시점에서 다시 생각이 나네요. getSnapshotBeforeUpdate 가 말 그대로 왜 스냅샷이라는 이름을 붙였는지요.

#### 4. 오류 처리
컴포넌트 렌더링, 라이프 사이클 훅이 실행될 때 오류가 발생한 경우 호출됩니다.
|라이프 사이클 훅| 설명 |
|--|--|
| static getDerivedStateFromError() | 자손 컴포넌트 에러 발생시 호출되어 state 수정 |
| componentDidCatch() | 자손 컴포넌트 에러 발생시 호출되어 에러정보를 알 수 있다. |

```jsx
class LifeCycleHook extends Component {
  state = { hasError: false }
  static getDerivedStateFromError(error) {
    return { hasError: true }
  }
  // 자식 컴포넌트의 오류를 throw한 후 실행
  componentDidCatch(error, info) {
    // info 매개변수:
    //  어떤 컴포넌트가 오류를 발생시켰는지에 대한 정보를 가진 componentStack 속성을 가진 객체

    // Example "componentStack":
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToService(info.componentStack)
  }
  render() {
    if (this.state.hasError) {
      return <h1>오류가 발생했습니다.</h1>
    }
    return <div />
  }
}
```

##### static getDerivedStateFromError(error)
자손 컴포넌트 에러 발생시 state 로 에러발생여부 등을 변경하여 이 속성을 render() 메서드에 적용하여 렌더링을 달리하는 데 사용될 수 있습니다.

##### componentDidCatch(error, info)
마찬가지로 자손 컴포넌트 에러 발생시 호출되는데 에러정보를 가진 info 매개변수를 받을 수 있습니다. info 안에서 에러 스택을 조회할 수 있습니다.

</div>
</details>

---------------------------------------

<details open>
<summary>3일차 학습 - 이벤트 핸들링, 컴포넌트 통신</summary>
<div markdown="1">


### 이벤트 핸들링
React 에서의 이벤트 핸들링은 좀 특별합니다. 저번과 마찬가지로 JSX 만의 규칙이 있고 핸들러 내부에서도 특정한 규칙을 따릅니다.

#### 기존과 문법적인 차이
- 이벤트 속성 이름은 camelCase 형식으로 작성합니다. (예: ```onClick```) *이벤트가 뭐가있는지는 문서를 살펴보시오!
- 속성 값에 문자열 대신 JSX 형식으로 메서드 참조를 연결합니다. (예: ```onClick={ onClickHandler }```)
- 브라우저 **기본 동작을 중단하려면 반드시 ```e.preventDefault()``` 를 사용해야 합니다**. 기존의 Javascript 에서는 return false 라는 선택지도 있었습니다. React 에서는 핸들러가 기본적으로 ```SyntheticEvent``` 객체로 래핑되어 모든 브라우저에서 인터페이스가 동일하게 동작합니다. 이에따라 React 에서는 ```return false``` 구문이 통하지 않으며 ```e.preventDefault()``` 나 ```e.stopPropagation``` 을 사용하여 통일된 인터페이스를 사용해야 합니다. 조금 제한을 걸면서 모든 브라우저에서 돌아가게끔 통일한 것이죠.

#### React 이벤트 핸들러 에서의 this
기존 VanilaJS 의 이벤트핸들러에서는 내부에서 this 키워드로 이벤트가 발생한 객체에 접근할 수 있었습니다. e.target, e.currentTarget 을 쓸 수 있었는데도 편의상 this 를 많이 썼었죠. 하지만 React 에서는 이를 허용하지 않습니다. e.target, e.currentTarget 이 떡하니 있는데 왜 this 를 쓰는가! 하여 this 는 기본적으로 undefined 상태입니다. 이러한 this의 공석을 메꾸어 React 에서는 this를 컴포넌트 객체로 지정합니다.

#### this 가 컴포넌트 객체인 이유
React 에서는 이벤트 핸들러를 멤버메서드로 두는 까닭에 그렇습니다. 일단 캡슐화의 이치에 맞게 멤버로 가야하고, 의미적으로 말이 되기도 하고 더불어 이벤트 핸들러에서 state 에 접근하여 수정되면 update 라이프사이클이 실행되고. 이 모든게 자연스럽고 완벽하게 떨어지게 됩니다. 하지만 이벤트 핸들러가 멤버로 왔다고 해서 this 가 생각한 대로 바인딩 되는것이 아니죠.

#### 이벤트 핸들러 this바인딩 문제 해결 방법
이벤트 핸들러는 일반함수로써 호출되기 때문에 JS 문법(+앞서다룬 React만의 규칙)에 따라 undefined 가 바인딩 됩니다. 이를 해결하는 3가지 방법을 다뤄봅시다.

##### 1. this 참조 고정
호출방식에 따라 암시적으로 유동적으로 변하던 this 를 생성자에서 .bind() 메서드를 통해 명시적으로 fix 시키거나, 이벤트핸들러 지정시 즉석으로 .bind() 를 호출하여 컴포넌트를 컴포넌트로 바인딩 시킵니다.

**생성자에서 지정**
```jsx
import React, { Component } from 'react'

class PreventBrowserDefaultAction extends Component {
  constuctor() {
    super()
    // this 참조를 컴포넌트 인스턴스로 변경
    this.handleClick = this.handleClick.bind(this)
  }
  handleClick(e) {
    e.preventDefault()
    console.log(this) // this === PreventBrowserDefaultAction {}
  }
  render() {
    return (
      <a href="https://google.com/" onClick={this.handleClick}>Google</a>
    )
  }
}
```

**이벤트핸들러 지정**
```jsx
class PreventBrowserDefaultAction extends Component {
  handleClick(e) {
    // ...
  }
  render() {
    return (
      <a 
        href="https://google.com/" 
        onClick={this.handleClick.bind(this)}
      >
        Google
      </a>
    )
  }
}
```

##### 2. 화살표 함수 래핑
화살표 익명함수는 상위의 this를 따르기 때문에 render() 메서드의 this 가 그대로 사용될 수 있고, 그 this 를 통해 메서드를 호출하기 때문에 문제없이 바인딩된다. 대신 이벤트객체 e 는 전달해야 한다.
```jsx
import React, { Component } from 'react'

class PreventBrowserDefaultAction extends Component {
  handleClick(e) {
    // ...
  }
  render() {
    return (
      <a
        href="https://google.com/"
        onClick={ (e) => this.handleClick(e) }
      >
        Google
      </a>
    )
  }
}
```

##### 3. 클래스 필드 구문 활용
향상된 객체 표기법이 아닌, ES 표준에 제안된 클래스 필드 구문을 사용하면 this가 컴포넌트 인스턴스로 fix되나 봅니다.
```jsx
import React, { Component } from 'react'

class PreventBrowserDefaultAction extends Component {
  // handleClick() {
  handleClick = (e) => {
    e.preventDefault()
    console.log(this) // this === PreventBrowserDefaultAction {}
  }
  render() {
    return (
      <a href="https://google.com/" onClick={this.handleClick}>Google</a>
    )
  }
}
```

#### 이벤트 핸들러 인자 전달
앞에서 배운대로 this 의 유실을 방지하면서 이벤트 핸들러에 특정인자를 전달하는 2가지 테크닉 입니다.

##### 1.화살표 래핑함수 사용
아까전 처럼 래핑함수를 사용하는 겁니다. 함수 내부는 자유로우니까 알아서 매개변수 배치해가며 다시 호출하는 겁니다.
```jsx
<BaseButton
  onClick={ (e) => this.handleClick(id, e) }
>
  ...
</BaseButton>
```

##### 2. Function.prototype.bind
이 방법 많이 써서 익숙할겁니다. this.handleClick.bind(this) 까지는 동일하나 뒤에 추가인자를 넣는건데요, 복습하자면 this 이후로 1번째 2번째 ... 인자가 되는것이고 마지막에 이벤트객체가 전달됩니다. 함수 정의부와 프로토콜을 잘 맞춰야 겠죠.
```jsx
<BaseButton
  onClick={ this.handleClick.bind(this, id) }
>
  ...
</BaseButton>
```

### 컴포넌트 통신
React 컴포넌트는 상위 컴포넌트가 state 를 관리하지만 일반적으로 하위컴포넌트와 state 를 공유할 수 없습니다. 따라서 하위 컴포넌트가 상위 컴포넌트의 state 를 조작하려면 상위 컴포넌트의 메서드를 전달받아 callback 해야만 합니다.

#### 부모 → 자식 메서드 전달
클래스필드 문법으로 작성된 메서드를 자식 컴포넌트에 전달하면 this 에 관한 걱정 없이 자식 컴포넌트는 호출만으로 부모 컴포넌트의 state 를 바꿀 수 있게 됩니다.
```jsx
class ParentComponent extends Component {
  state = {
    message: '자식 컴포넌트의 요청을 대기 중입니다.'
  }
  // 2. 자식 컴포넌트에 의해 실행되는 부모 컴포넌트의 메서드
  processRequested = (message) => {
    // 3. 부모 컴포넌트의 상태 업데이트
    this.setState({ message })
  }
  render() {
    return (
      <div className="parent">
        // 1. 자식 컴포넌트에 props.receivedMethod 속성으로 메서드 전달
        <ChildComponent receivedMethod={this.processRequested} />
        <p>{ this.state.message }</p>
      </div>
    )
  }
}
```

#### 부모 ← 자식 메서드 실행
```jsx
const ChildComponent = (props) => {
  return (
    <button
      type="button"
      onClick={ () => props.receivedMethod('전달 받은 메서드 잘 사용했습니다!') }
    >
      부모 컴포넌트의 상태를 바꿔봅시다!
    </button>
  )
}
```

### 컴포넌트 통신의 한계점
depth 가 깊은 자손 컴포넌트가 상위 컴포넌트의 state 를 조작해야 하는 경우, 메서드 전달을 주구장창 해야한다는 단점이 존재합니다. 이를 보완하여 데이터를 한번에 전달할 수 있는 Context API 가 생겨났지만, 컴포넌트의 재사용성이 낮아진다는 단점이 존재하여 Redux 라는 상태관리 시스템을 사용하도록 권장하고 있습니다. Redux 는 모든 컴포넌트가 접근할 수 있도록 store라는 한곳에서 통합적으로 상태를 관리하며 어디서든 업데이트요청을 받아 필요한 곳에 업데이트를 수행할 수 있습니다.


</div>
</details>

---------------------------------------

<details open>
<summary>4일차 학습 - Context API</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>

---------------------------------------

<details open>
<summary>5일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>