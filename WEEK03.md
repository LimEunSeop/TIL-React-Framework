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

## 3주차 대 주제를 작성합니다.

<details open>
<summary>1일차 학습</summary>
<div markdown="1">

### 여기에 자유롭게 마크다운 정리 하시기 바랍니다.
- Heading 은 최소 '''**###**''' 뎁스부터 시작하기 바랍니다. (대 주제를 '''##'''로 작성했기 때문에)
- Markdown 에디터를 사용하면 마크다운 문법을 알고있지 않아도 작성하기 용이합니다. (https://stackedit.io/app#)

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
