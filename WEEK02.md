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
