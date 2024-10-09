💭 넘 짧은 거 같아서 안 올리려 했는데 고냥 올릴게요!  
💭 원리는 `useState`와 비슷할 듯!
  
### ➊ useReducer 사용법

```javascript
const [state, dispatch] = useReducer(reducer, initialState);
```

- state: 컴포넌트에서 사용할 수 있는 상태
- dispatch: 특정한 액션을 reducer에 전달하는 함수
    - `dispatch({ type: 'INCREMENT' })`
    - `dispatch`는 `reducer`에게 어떤 상태 변경이 일어나야 하는지를 트리거하는 함수
    - `dispatch`가 호출되면 해당 `action`을 `reducer`에 전달하여 새로운 상태를 계산하고 업데이트한다.
- reducer: 상태(state)를 업데이트하는 로직을 정의한 함수
- initialState: 초기 상태

```javascript
function reducer(state, action) {
	switch (action.type) {
		case 'INCREMENT':
			return state + 1;
		case 'DECREMENT':
			return state - 1;
		default:
			return state;
	}
}

function Counter() {
	const [number, dispatch] = useReducer(reducer, 0);
	
	const onIncrease = () => {
		dispatch({ type: 'INCREMENT' });
	};
	
	const onDecrease = () => {
		dispatch({ type: 'DECREMENT' });
	};
	
	return (
    <div>
      <h1>{number}</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
	);
}

export default Counter;
```

### ➋ reducer와 dispatch의 차이점

- **reducer**: 상태를 변경하는 로직을 포함한 **함수**
- **dispatch**: 상태를 변경하고 싶을 때 **action**을 보내는 **함수**
  
`reducer`는 상태를 어떻게 변화시킬지를 결정하고, `dispatch`는 그 변화를 실제로 요청하는 역할을 한다.  
