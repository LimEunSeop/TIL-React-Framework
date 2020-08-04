# TIL
오늘 내가 배운 것들(Today I Learned)   


---------------------------------------

## 심화정리

### Redux Hook
지금까지 배운 Redux 상태관리 방법을 쭉 정리해보면 다음과 같습니다. 
![Redux State Management Diagram](https://github.com/LimEunSeop/TIL-React-Framework/blob/master/assets/A-1.JPG?raw=true)

여기서 처음보는것이 useSelector, useDispatch, useStore Hook 인데요, Functional Component 에서 connect 고차컴포넌트를 사용하기 어려워 하는 사람을 위해 만들어졌습니다. 그렴에서 이미 보아 알 수 있듯이, React Redux Provider 가 이미 감싸고 있기 때문에, Hook만 사용하면 됩니다.

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