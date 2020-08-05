# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------

## 심화정리

### Redux Hook
지금까지 배운 Redux 상태관리 방법을 쭉 정리해보면 다음과 같습니다. 
![Redux State Management Diagram](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/A-1.JPG?raw=true)

여기서 처음보는것이 useSelector, useDispatch, useStore Hook 인데요, Functional Component 에서 connect 고차컴포넌트를 사용하기 어려워 하는 사람을 위해 만들어졌습니다. 그림에서 이미 보아 알 수 있듯이, React Redux Provider 가 이미 감싸고 있기 때문에, Hook만 사용하면 됩니다.

| Hook | 설명 |
|--|--|
| useSelector | state의 특정 속성을 가져올 때 사용합니다. |
| useDispatch | dispatch 메서드를 가져옵니다. |
| useStore | dispatch 와 state 를 포함하는 Store를 가져옵니다. (전통적인 방법) |

사용법은 매우 간단합니다. useSelector 의 경우는 특정 속성을 가져와야하므로 콜백을 전달하고, dispatch, store 는 기존에 쓰던 방식대로 써 나가면 됩니다.
```javascript
// 스토어 상태 추출
const name = useSelector(({ testReducer }) => {
  return testReducer.name;
});

// 스토어 디스패치 함수 추출
const dispatch = useDispatch();

// 스토어 참조 훅
const store = useStore();

```

### Redux Thunk
Thunk란, **1. 본래의 행동에 추가적인 행동을 지원하기 위해**, **2. 행동을 미루기 위해** 함수로 래핑된 것을 의미합니다. 예를들어, setState의 경우에는 보통 객체를 인자로 받지만, 객체를 반환하는 함수를 인자로 받아 추가적인 행동이 가능해집니다. 객체가 바로 리턴되는 것이 아닌 함수실행으로 리턴될 것이기 때문에 한 템포 미루는 효과도 있고요. 이때 그 인자로 전달한 함수는 thunk의 일종이라고 할 수 있습니다.

Redux 에서의 Thunk 란, Action에 추가적인 행동을 지원하기 위해 사용됩니다. Reducer 함수는 순수함수이므로 그 안에서 SideEffect, API 호출 등의 작업을 할 수 없습니다. 그래서 추가적인 처리를 위해서는 dispatch 에 Action 대신 dispatch, state를 인자로 받는 Thunk를 전달하여 Thunk 내에서 추가적인 처리 후 최종(실제) dispatch를 하도록 하는 것입니다.

**`redux-thunk` 모률을 설치하고 미들웨어에 적용하면, dispatch의 인자 전달 부분을 가로채 thunk 가 전달될 수 있도록 해줍니다.**

> **Q. 왜 액션 Creator 에서 추가 행동을 하여 Action을 리턴하지 않고, Thunk 를 반환하여 그 안에서 추가처리 하도록 하는 걸까? Thunk 필요 없지 않나?**
> A. React Redux 에서 mapDispatchToProps 를 할 때, value 로 Action Creator 를 넘겨줍니다. props에 매핑하는 과정에서 ActionCreator 를 실행한 후 props에다 ()=>dispatch(ActionCreator 실행 리턴값) 을 매핑하는데요, 매핑때 Action Creator 가 실행되기 때문에 Thunk 안에 로직을 넣어서 미뤄야 하는 것입니다. 우리가 원하는 것은 Dispatch 시점에 일이 실행되는 것이지, 초기 Mapping 시에 일이 실행되는 것이 아니기 때문이지요. 이처럼 Redux 는 Action Creator 중심으로 돌아가는 구석이 있기때문에, Action Creator 에서 로직이 실행되지 않도록 Thunk로 미루는 작업이 필요한 것입니다. React Redux 의 connect 를 쓰지 않는다면 상관 없겠지만, connect 를 쓰거나 범용성있는 코드 작성을 위해 Thunk 는 꼭 필요하지 않을까 싶습니다. 이렇게 보니 Thunk 의 미루기 효과가 뭔지 확실히 체감이 됩니다.

> thunk.withExtraArgument 로 3번째 이상 전달인자를 설정할 수 있다. 아직은 나에게 시기상조. 나중에 필요할 때 알아보자.

### Redux Toolkit
아직 Redux를 사용하는데 딱히 불편한건 없지만 불편한 점이 있다면 그때그때 Toolkit 사이트 가서 찾아보는 편이 옳지 않을까?
https://redux-toolkit.js.org/