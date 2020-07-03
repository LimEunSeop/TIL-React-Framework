# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------
## 2주차 질문
- Q. svg 접근성 해결법에 대한 궁금증 (svg 태그 안에 title 태그 정의하면 안되는지)
  ```
    A.말씀하신 것처럼 SVG > title 콘텐츠 값을 변경해서 이해 가능한 내용을 입력할 수 있습니다.
    다만 예시의 경우 해당 아이콘(SVG)은 하드 코딩된 경우인데.. 

    만약 하드코딩 형태가 아니라 이미지 링크 형태로 SVG 파일을 아이콘으로 사용한다면? 
    매번 SVG 이미지 파일을 열어 title 콘텐츠 값을 변경해야 하는 문제. 그리고 문맥 상 다른
    콘텐츠로 해석되어야 할 경우 아이콘을 재사용 하기 어려운 문제를 맞게 됩니다.

    즉, SVG가 이미지 링크 또는 하드 코딩 등 다양한 방법으로 사용될 수 있고, 문맥의 흐름 상
    내용이 바뀔 가능성이 있는 경우라면 title 콘텐츠를 고정하기 어렵습니다.

    물론, 이러한 경우 철저히 대응해 접근성을 준수하겠다고 하는 개발자라면 본인의 책임 하에
    SVG의 title 콘텐츠를 변경해도 됩니다. ^ㅡ^

    하지만 그것이 쉽지 않다면? 접근성 있는 감춤 콘텐츠 처리 방법을 사용하거나,
    aria-label을 사용해 스크린 리더에 읽히는 콘텐츠를 덮어쓸 수 있습니다.
    이 방법을 사용하면 SVG 내부의 title을 건드리지 않고도 콘텐츠를 유연하게 관리할 수 있습니다.


    <button type="button">
        <svg
            aria-label="프로젝트 공유하기"
            width="20px" height="22px"
            viewBox="0 0 20 22"
            xmlns="http://www.w3.org/2000/svg"
        > ... </svg>
    </button>
  ```

- Q. 선생님 img 의 alt 속성 대신 hidden span 의 사용 철학은 무엇이죠? 이번엔 바로 타임 링크 걸었습니다 ㅋㅋ

https://www.youtube.com/watch?v=L1YLFYlkGnc&feature=youtu.be&t=4m47s
  ```
    A.사용 철학이라기 보다는 ^^;
이미지의 대체 텍스트로 콘텐츠를 넣어도 되는데요. 

다만 그런 경우 인디케이터 위에 마우스가 올라가거나 포커싱 되었을 때 명시적인 툴팁으로 콘텐츠를 보여줘야 접근성이 향상 되겠죠.

그런 경우 span을 사용해 감춤 처리하되, 툴팁으로 표시하는 방법에 활용할 수도 있어요. 하지만 그런 경우가 아니라면 alt 속성 값을 적절히 주어 접근성을 준수할 수도 있습니다 ^^
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
> props.children은 현재 컴포넌트가 상위에서 사용될 때 자식으로 또다른 Element 를 정의했을 경우 세팅됩니다.

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

### Context API
기존의 React DOM 에서는 상위 컴포넌트가 하위 컴포넌트로 데이터를 전달할 때 계층을 타고 목적지 컴포넌트에 전달될 때까지 일일히 자식컴포넌트에게 전달할 수밖에 없었는데요, Context API 라는 기술은 Context 라는 데이터 관리 모듈을 따로 바깥에 빼서 어느 컴포넌트에서든지 바로 접근이 가능하도록 해줍니다.

#### 기본 사용법
1. **React.createContext() 함수로 컨텍스트를 만듭니다.**
```jsx
// AuthContenxt.js
import React from 'react'

export const authContext = {
  isAuth: false,
  signIn = () => { ... }
}

export default React.createContext(authContext)
```
첫번째 인자로, 컨텍스트가 관리하는 데이터의 default 값을 설정합니다.
> 다른 모듈에서 불러올 수만 있다면 어떤 수단으로 만들어도 상관없습니다.

2. **```<컨텍스트.Provider>``` Element로 데이터를 전달받고자 하는 자손 컴포넌트를 감싼 후, ```value``` Property 에 전달하고 싶은 값을 세팅해줍니다.**
```jsx
// App.js
import AuthContext from './context/AuthContext'

class App extends React.Component {
  state = {
    authentification: true
  }
  logIn = () => {
    // 로그인 ... 
  }
  render() {
    return (
      <AuthContext.Provider 
        value={{ isAuth: this.state.authentification, signIn: this.logIn }}
      >
        <MenuBar />
      </AuthContext.Provider>
    )
  }
}
```
내가 전달하고 싶은 state 가 지금 보고있는 컴포넌트에 존재한다? 그럼 그 자리에서 ```<컨텍스트.Provider>``` 로 감싸는 작업을 치는 것이죠.
> Provider 가 있을 필요는 없습니다. React.createcontext() 할 때 세팅된 기본값만 열람될 뿐이죠.

3. **데이터를 받고자 하는 컴포넌트에서 ```<컨텍스트.Consumer>``` 엘리먼트를 사용해줍니다.**
```jsx
// SignIn.js
import AuthContext from '../context/AuthContext'

const SignIn = () => (
  <AuthContext.Consumer>
    {
      ({isAuth, signIn}) => isAuth ? 
        <div className="signed">로그인 됨</div> : 
        <button type="button" onClick={() => signIn}>로그인</button>
    }
  </AuthContext.Consumer>
)
```
```<컨텍스트.Consumer>``` 엘리먼트는 ```props.children``` 를 반드시 정의해줘야 합니다. ```props.children``` 가 Provider 로부터 설정되었던 context value 를 인자로 받고 실행하여 값을 리턴해주는 콜백메서드 이기 때문이죠. 실질적으로 이 전달인자로써 데이터를 받게 되는것이고, 콜백에서 이것을 잘 활용하여 JSX 를 리턴해줌으로써 하위컴포넌트에서 바로 데이터를 불러들어오는 꼴이 되는것입니다. 이래서 컴포넌트의 재사용성이 깨질 수밖에 없나봅니다.

#### Context Type 를 활용한 쉬운 활용법
Provider 의 활용은 그대로 두고, Consumer 측에서 Consumer Element 사용하지 않고, 컴포넌트 클래스의 ```contextType``` static 속성에 컨텍스트를 할당하면 ```this.context``` 를 통해 context value 에 손쉽게 접근할 수 있습니다.
```jsx
// SignIn.js
import AuthContext from '../context/AuthContext'

class SignIn extends React.Component {
  static contextType = AuthContext
  render() {
    const { isAuth, signIn } = this.context
    return (
      {
        isAuth ? 
          <div className="signed">로그인 됨</div> : 
          <button type="button" onClick={() => signIn}>로그인</button>
      }
    )
  }
}
```
대신 this.context 를 한 컨텍스트만이 점유하므로 한 컴포넌트당 한 컨텍스트에 한정되고요, 나머지 구성은 Consumer 를 사용해야될 것입니다. 복수 컨텍스트 사용시 Consumer와 Context Type를 섞어쓸지, 일종의 깔끔함을 위해 Consumer 만을 사용할지는 개발자 마음이 될것 같습니다.

#### 한 요소에 2개이상 컨텍스트의 값을 받는법
이런식으로 Consumer 를 중첩하면 된다.
```jsx
// 여러 context의 값을 받는 컴포넌트
function Content() {
  return (
    <ThemeContext.Consumer>
    {theme => (
      <UserContext.Consumer>
      {user => (
        <ProfilePage user={user} theme={theme} />
      )}
      </UserContext.Consumer>
    )}
    </ThemeContext.Consumer>  );
}
```

</div>
</details>

---------------------------------------

<details open>
<summary>5일차 학습 - React 접근성(A11Y)</summary>
<div markdown="1">

### React a11y
접근성을 준수하는 이유는 모든 사용자를 고려하기 위함입니다.

제가 접근성에 욕심이 있는것은 결국 한가지 목적으로 귀결되는것 같습니다. 문서 작성자로서 이왕 작성할 것 대충 작성하지 않고 완벽하게 작성하고 싶을 뿐 입니다. HTML 이 직접적으로 화면에 보이지 않는다고 해서 천시되는 경향이 있습니다. 하지만 우리는 배워왔지 않습니까, HTML은 골격을 담당한다고요. 골격을 담당한다고 배웠으면서 대충짜고 천시한다는 것은 매우 모순적이라고 생각이 듭니다. 그냥 열심히 완벽하게 짜고싶습니다. 이러한 고민이 무언가의 예술적인 감성을 자극하여 더 재미를 느끼곤 합니다. 또한 이러한 노력은 배신을 하지 않는다 굳게 믿습니다.

단 한가지, 접근성을 준수하는것 자체를 즐기고 노력을 기울일 것입니다. 그에대한 보상은 자동적으로 따라올 것입니다. 다만 아직 제 스스로 검증하지 못한 것은 정석을 유지한다고 해서 CSS나 JS 인터렉션 소스를 짤 시 유지보수가 잘 되는 코드가 작성될 수 있냐는 것입니다. 이에 대해서도 제 스스로 경험해가며 꾸준히 고민을 이어나갈 것입니다.

#### 헤딩 레벨 (Heading Level)
문서의 개요가 작성이 되는 아주 중요한 요소입니다. 이를 제대로 준수하지 않은 사이트는 목차가 없는 책이나 마찬가지 입니다.
> 시력이 불편한 사람이 로터 기능을 사용하면 Heading 의 depth 를 선택하여 Heading 을 빠르게 훑어볼 수 있습니다.

##### Tenon-UI 사용
[Tenon-UI](https://www.tenon-ui.info/headings/) 모듈을 사용하면 h1,h2,h3 의 고민없이 depth 에 따른 order 를 자동적으로 구성해줍니다. 개발자는 개요 도식화만 완벽히 하면 됩니다.
```jsx
{ /* 제목: 'kakako TV' */ }
<Heading.H>kakao TV</Heading.H>
{ /* [ 하위 섹션 바운더리 ] */ }
<Heading.LevelBoundary>
  { /* 제목: '검색' */ }
  <Heading.H>검색</Heading.H>
  { /* [ 하위 섹션 바운더리 ] */ }
  <Heading.LevelBoundary>
    { /* 제목: 'kakako TV 홈 메뉴' */ }
    <Heading.H>kakao TV 홈 메뉴2</Heading.H>
    { /* 제목: '인기 PD 동영상' */ }
    <Heading.H>인기 PD 동영상</Heading.H>
    { /* 제목: '카테고리 별 인기 동영상 슬롯' */ }
    <Heading.H>카테고리 별 인기 동영상 슬롯</Heading.H>
    { /* [ 하위 섹션 바운더리 ] */ }
    <Heading.LevelBoundary>
      { /* 제목: '예능 최신 인기 동영상 바로가기' */ }
      <Heading.H>예능 최신 인기 동영상 바로가기</Heading.H>
      { /* 제목: '드라마 최신 인기 동영상 바로가기' */ }
      <Heading.H>드라마 최신 인기 동영상 바로가기</Heading.H>
    </Heading.LevelBoundary>
  </Heading.LevelBoundary>
</Heading.LevelBoundary>
{ /* 실습 영역: 끝  */ }
```

##### tota11y 크롬 익스텐션 사용
이를 사용하면, 사이트의 접근성을 검사해보고 오류를 발견해볼 수 있습니다.

#### 히든 콘텐츠 (Hidden Contents)
시각적으로 직관적으로 인식될 수 있는 콘텐츠가 시각이 배제된 스크린리더 에서 접근성이 엉망으로 인식될 수가 있습니다.
이처럼 화면에 보이진 않지만 스크린 리더에서 읽혀야하는 콘텐츠가 있습니다.

이를 처리하기 위해서는, 히든 콘텐츠가 스크린리더에 올바르게 읽혀지도록 하는 작업이 필요합니다.

다음은 스크린리더가 인식할 수 없는 잘못된 숨김처리 예시입니다.
```html
<div style="display: none"> ... </div>
<div style="visibility: hidden"> ... </div>
<div hidden> ... </div>
```
이러한 속성은 스크린리더로 하여금 읽지 마라고 제대로 명시하는 꼴이 돼버립니다. 이를 해결하기 위해선 trick 이 필요합니다. 널리 통용되는 a11y-hidden CSS 클래스 스타일링 입니다.

##### CSS a11y-hidden Class 사용
```css
.a11y-hidden {
  position: absolute;
  clip: rect(0 0 0 0);
  width: 1px;
  height: 1px;
  overflow: hidden;
  margin: -1px;
  border: 0;
  padding: 0;
  white-space: nowrap;
}
```
보시면 스크린리더에게 읽히기 위해 부단한 노력을 하고있는걸 알 수 있습니다. 일단 nomal-flow 를 벗어나기 위해 absolute position 을 사용했고, clip 속성으로 rect 영역 너비를 0으로 잘랐습니다. 그리고 width, height 를 1로 주고 margin 을 -1로 줌으로써 실질적으로 차지하는 너비가 0이 되도록 퉁쳤습니다. width나 height 를 0으로 주면 되지 않느냐 의문이 생길 수 있지만 이는 조금이라도 걸림돌이 되어 요소가 인식되지 않을 수도 있습니다. overflow: hidden 을 통해서도 확실히 너비차지 안하도록 설정했네요. 이를 이제 class 속성에 적용시키면 됩니다.

##### React A11yHidden 컴포넌트 사용
React 에서는 Hidden Contents 처리를 위해 A11yHidden 래핑 컴포넌트 사용을 고려해볼 수 있습니다. a11y-hidden 을 안쓰고 굳이 A11yHidden 컴포넌트를 쓰는 이유는 컴포넌트에 정의된 기능을 사용함으로써 접근성에 대한 도움을 받을 수 있기 때문입니다. (Ex. focusable 속성을 위해 포커싱되면 일시적으로 visible하게 가능)
```jsx
<A11yHidden tag="button" focusable className="button"> ... </A11yHidden>
```

##### svg 아이콘이 들어간 버튼 오류 바로잡기
svg내에있는 title 태그의 알수없는 문자를 읽습니다. 이를 해결하기 위해 svg의 속성에 aria-hidden="true" 를 줌으로써 스크린리더 해석을 배제하고 위에 A11yHidden span 태그를 간단히 추가합니다.

###### 오류
```html
<button>
  <i class="_IQY7TzdLHKX3-BKPDNYKF"></i>
  <svg
    width="20px"
    height="22px"
    viewBox="0 0 20 22"
    xmlns="http://www.w3.org/2000/svg"
  >
    <title>ic_share_B</title>
  </svg>
</button>
```

###### 해결 결과
```jsx
<button>
  <A11yHidden>프로젝트 공유하기</A11yHidden>
  <svg
    aria-hidden="true"
    width="20px"
    height="22px"
    viewBox="0 0 20 22"
    xmlns="http://www.w3.org/2000/svg"
  >
  </svg>
</button>
```
svg 내부의 title 태그를 수정하면 좋겠지만 우리가 접하는 svg는 대부분 파일링크 형태입니다. 따라서 aria-hidden로 스크린리딩을 막거나 aria-label 로 스크린리딩을 덮어쓰는 방법밖엔 없습니다. 아니면 svg arid-hidden 후 sibling 으로 A11yHidden 컴포넌트 둘 수도 있습니다.

#### 버튼 컴포넌트 (Button Component)
~~a[href=#], div, img로 버튼을 마크업하지 맙시다.~~ 버튼이라고 생각드는건 **무조건 버튼**으로만 작성해야 합니다. **스크린 리더는 요소의 종류를 읽기 때문** 입니다. 또한 **focusing 접근성을 위해서도 필요합니다!!!**

##### 버튼 마크업의 정석
```html
<button type="button">
  <img src="..." alt>
  <span class="a11y-hidden">더워도 걱정 없는 아이스 립 파우치</span>
</button>
```
필요한 경우 아이콘이나 img를 넣고, span 으로 버튼 설명을 넣습니다. (글자만으로 이루어진 버튼이라면 span를 숨김처리하진 않겠죠.) img 의 alt 속성을 쓰지 않는 이유는 혹시라도 생길 툴팁에 대한 변수를 생각해서 그렇습니다. **img 태그가 쓰이는 상황에서는 alt 의 내용을 따로 element 로 빼야할지 생각하면서 작업해야겠습니다.**


##### Carosel Component
다음과 같이 Navigating(prev, next), Play&Pause 를 관장하는 Control 컴포넌트, 현 컨텐츠의 위치 표시 및 원하는 위치 바로탐색 기능을 제공하는 Indicator 컴포넌트로 구성돼있는 컴포넌트로, 컨텐츠의 SLIDE 기능을 담당합니다.
![Carosel Component](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/2-5-1.png?raw=true)

##### Control 컴포넌트
Navigating(prev, next), Play&Pause 를 관장하는 컴포넌트입니다. button Native element 로 스트럭쳐 구성하고 정석대로 작성된 것을 볼 수 있습니다.
```jsx
import React, { Component } from 'react'
import A11yHidden from './components/A11yHidden'

const Control = (props) => {
  const c_name = props.prev ? 'btn-prev' : props.next ? 'btn-next' : ''
  return (
    <button
      type="button"
      className={c_name}
      onClick={props.onClick}
    >
      <A11yHidden>{ props.children }</A11yHidden>
    </button>
  )
}

export default Control
```

##### Indicator 컴포넌트
Navigating(prev, next)을 관장하는 컴포넌트입니다. 이것도 마찬가지로 button 엘리먼트를 정석적으로 사용하여 키보드 접근성 준수되고 스크린리더의 라벨읽기 접근성도 준수된 것을 볼 수 있습니다.
```jsx
import React, { Component } from 'react'
import A11yHidden from './components/A11yHidden'
import placeholder from '../assets/placeholder-indicator.jpg'

class Indicator extends Component {
  state = { isActive: false }
  render() {
    const { isActive } = this.state
    const { image, label } = this.props
    return (
      <div className={`indicator ${isActive ? 'is-active' : ''}`}>
        <button type="button" className="btn-indicator">
          <img src={ image || placeholder } alt />
          <A11yHidden>{label}</A11yHidden>
        </button>
      </div>
    )
  }
}

export default Indicator
```
> placeHolder 테크닉 꿀팁이다. 그리고 다들 괜찮은 소스인데 이 indicator 가 어떻게 동작하여 is-active 가 스위칭되는지 크게 감이 안서는데 이에대한 활용을 나중에 꼭 다뤄보도록 하자.

##### Indicators 컴포넌트
Indicator 들을 map으로 생성하여 이렇게 배열로 관리합니다.
```jsx
import React, { Component } from 'react'
import Indicator from './Indicator'

const Indicators = (props) => (
  props.indicators.map(indicator => (
      <Indicator
        key={indicator.id}
        image={indiator.image}
        label={indicator.label}
      />
    )
  )
)

export default Indicators
```

#### React 에서 사용에 주의가 필요한 HTML 표준
컴포넌트 프로그래밍 에서는 HTML 코드를 한 눈에 보기 힘들어 표준을 준수하지 못하는 실수가 발생하곤 합니다. 주로 list item 과 list 컴포넌트 사이에서 그러한데요, 주의해야할 표준을 다시 한번 짚어봅시다.

```html
<!-- 1 -->
<ul>
  <li>리스트 아이템 요소는 리스트(ul 또는 ol) 요소 내부에만 배치되어야 합니다.</li>
</ul>
 
<ol>
  <li>리스트 아이템 요소는 리스트(ul 또는 ol) 요소 내부에만 배치되어야 합니다.</li>
</ol>
 
 
<!-- 2 -->
<dl>
  <dt>설명 리스트의 Key 내용은 dt 요소에</dt>
  <dd>설명 리스트의 value 내용은 dd 요소 안에 위치 해야 합니다.</dd>
  <div>
    <dt>필요한 경우, 선택적으로 dt, dd를 감싸는 div 요소를</dt>
    <dd>dl 안에서 사용할 수 있도록 HTML 5.2에서 개정되었습니다.</dd>
  </div>
</dl>
 
 
<!-- 3 -->
<figure>
  <figcaption>피규어를 설명하는 내용은 반드시 figure 요소 안에 배치되어야 합니다.</figcaption>
</figure>
 
 
<!-- 4 -->
<table>
  <caption>table의 첫번째 자식 요소로 배치되어야 합니다.</caption>
  <tr>
    <th>tr 요소 내부에 배치되어야 합니다.</th>
  </tr>
  <tr>
    <td>tr 요소 내부에 배치되어야 합니다.</td>
  </tr>
</table>
```

#### 접근성 자동 검사
```react-axe``` 패키지 설치 후 다음과 같이 설정을 담당하는 엔트리파일 index.js 에 세팅해 놓으면 라이브러리로부터 접근성에 대한 도움을 많이 받을 수 있습니다.

##### 설치
```npm i react-axe```

##### 적용
```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import axe from 'react-axe'

if (process.axe.NODE_ENV !== 'production') {
 axe(React, ReactDOM, 1000)
}

ReactDOM.render(<App />, document.getElementById('root'))
```

</div>
</details>
