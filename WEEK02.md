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
<summary>2일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

</div>
</details>
