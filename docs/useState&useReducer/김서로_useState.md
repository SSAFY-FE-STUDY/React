## ➊ useState을 사용하는 이유

버튼을 클릭하면 `number`가 1씩 증가하는 컴포넌트를 구현해보자!  
```javascript
function App() {
  let number = 0;

  const handleButtonClick = () => {
    number += number;
  };

  return (
    <>
      <div>{number}</div>
      <button onClick={handleButtonClick}>1 증가</button>
    </>
  );
}

export default App;
```

이때 값이 변할 `number`라는 상태는 `let`으로 선언하였다.  
이를 실행하고 버튼을 클릭하면 어떤 결과가 나타날까?  
  
![](https://velog.velcdn.com/images/okxooxoo/post/ab8b8f53-417f-48d4-ab65-ca14e73f481e/image.png)
  
**버튼을 눌러도 `number`의 값은 0으로 변하지 않는다.**  
  
🤔 왜 그럴까?  
  
먼저 지역 변수는 값이 변경되어도 자동으로 리렌더링이 되지 않는다.  
따라서 변경된 `number`의 값이 화면에 반영되지 않는다.  
  
만약 리렌더링을 강제로 시킨다면 어떨까?  
그렇다 하더라도 `number`의 값은 화면에 0으로 출력될 것이다.  
  
이유는 다음과 같다.  
  
함수형 컴포넌트는 렌더링될 때마다 전체 함수가 다시 실행되고 JSX를 반환한다.  
그 결과, `number`는 함수 내의 지역 변수이기 때문에,  
컴포넌트가 다시 렌더링될 때마다 항상 0으로 초기화된다.  
  
이 문제를 해결하려면 `useState`, `useReducer`와 같은  
**State Hooks**을 사용하여 상태를 관리해야 한다!  
  
**State Hooks**을 사용하면  
컴포넌트가 다시 렌더링되더라도 상태 값이 유지되며,  
상태가 변경될 때마다 컴포넌트는 자동으로 리렌더링되어  
변경된 값을 화면에 반영할 수 있다!  
  
## ➋ useState 사용법

대표적인 상태 관리 Hook인 `useState`을 활용하여  
상태 값이 유지될 수 있도록 변경해보자!  
  
```javascript
import { useState } from 'react';

function App() {
  const [number, setNumber] = useState(0);

  const handleButtonClick = () => {
    setNumber(number + 1);
  };

  return (
    <>
      <div>{number}</div>
      <button onClick={handleButtonClick}>1 증가</button>
    </>
  );
}

export default App;
```

`useState` 함수의 인수로 상태의 **초기값**을 전달하면,  
해당 상태의 **현재 값**과 그 값을 **업데이트할 수 있는 함수**가 포함된 배열을 반환받는다.  
  
위 코드에서 비구조화 할당을 통해 각각`number`, `setNumber`에 저장하였다.  
  
이처럼 `useState`을 사용하면 버튼을 클릭할 때마다 정상적으로 `number`가 증가하며 화면에도 바로 반영된다.  
  
![](https://velog.velcdn.com/images/okxooxoo/post/0cf72c0d-273b-4f38-a9de-f0709103ea4a/image.png)
  
🤔 그런데 코드를 찬찬히 살펴보면 이상한 점이 있다.  
  
첫 번째로, `number`가 `const`로 선언되었다는 점이다.  
`const`은 재할당이 불가능하므로 값이 수정될 수 없다.  
  
두 번째로, 리렌더링될 때마다 App 컴포넌트가 다시 실행되기 때문에  
그 때마다 `number`가 0으로 초기화될 것이다.  
  
이런 점들이 의문을 불러일으키지만  
버튼을 클릭하면 `number`가 정상적으로 증가하는 것을 확인할 수 있다.  
  
그러면 어떻게 상태를 유지할 수 있는 것일까?  
다시 App 이 실행되면, `useState` 는 다시 처음부터 실행되어 0을 넣는 것은 아닐까?  
대체 값을 어떻게 기억하고 있는걸까 ❓  
  
## ➌ useState의 동작 원리

상태를 어떻게 기억하고 있는가에 대한 답변을 한 문장으로 정리하면  
`useState`는 내부적으로 **클로저**를 사용하여 구현되어 있기 때문에 가능하다!  
  
> 클로저란?  
외부 함수에서 선언한 변수를 참조하는 내부 함수에서 발생하는 현상이다.  
  
클로저에 대해 이해하기 좋은 간단한 예제 코드는 다음과 같다.  
  
```javascript
function outer() {
  let outerVar = 1;
  function inner() {
    console.log(outerVar);
  }
  return inner;
}
const closure = outer();
closure(); // 출력: 1
```

`outerVar` 변수는 외부 함수인 `outer`에서 선언되었기 때문에  
`outer` 함수가 정상적으로 실행되어 반환을 마치고 나면 `outerVar` 또한 메모리 상에서 사라져야 할 것이다.  
  
그러나 실제로는 반환된 `inner` 함수에서 `outerVar` 변수를 참조하고 있으므로  
`outerVar`은 메모리에서 사라지지 않으며 정상적으로 접근 가능하다.  
  
이처럼 클로저는 **함수가 호출된 후에도 살아있는 변수**를 제공한다.  
  
`useState`은 이러한 클로저 현상을 이용하고 있기 때문에 App 컴포넌트의 상태를 기억하고 있다.  

```javascript
let _val

useState(initialValue) {
	if (_val === undefined) {
    	_val = initialValue
    }
  
  	const setState = (newVal) => {
    	_val = newVal
    }
  
  return [_val, setState]
}
```

`useState`의 내부 코드를 단순화하면 위와 같다.  
이때 `_val`은 외부 변수지만 `useState` 내에서 참조하고 있다.  
그리고 이 `_val`을 return문을 통해 반환하고 있다. **(클로저)**  
  
`_val` 은 처음에는 `undefined`가 할당되어 있을 수밖에 없는데,  
`undefined`일 경우에는 `initialValue` 를 할당한다.  
`useState` 가 두 번째로 불릴 때부터는,  
`_val` 에 이미 값이 할당되었을 것이므로 기존 값을 그대로 사용한다.  
  
`useState` 메서드 안에도 `setState` 메서드가 있다.  
컴포넌트가 `useState`를 사용하면 반환 받는 setter로,  
컴포넌트에서 값을 업데이트할 때는 이 `setState` 를 이용하게 된다.  
이 `setState` 는 외부 scope에 정의되어 있는 `_val` 을 변경한다.  
  
> 결론 😎  
state은 클로저를 통해 참조되고 있는 외부 변수임을 알 수 있다!  
  
이러한 원리를 잘 이해했다면 아까의 코드를 다시 살펴보자!  

```javascript
import { useState } from 'react';

function App() {
  const [number, setNumber] = useState(0);

  const handleButtonClick = () => {
    setNumber(number + 1);
  };

  return (
    <>
      <div>{number}</div>
      <button onClick={handleButtonClick}>1 증가</button>
    </>
  );
}

export default App;
```

처음으로 App 컴포넌트를 호출하면 `useState`가 실행된다.  
이때 `_val`에는 아무것도 없기 때문에 초기값인 0이 `_val`에 할당된다.  
  
`useState`은 반환값으로 `number`을 0으로, `setNumber`는 `_val`에 접근하는 setter을 돌려준다.  
이후 App 컴포넌트는 버튼 컴포넌트를 포함한 JSX를 반환한다.  
  
화면에 나타난 버튼을 클릭하면 `setNumber(number + 1)`을 실행한다.  
이때 외부 scope에 정의되어 있는 `_val`에 접근하여 값을 변경한다 ❗️  
이때 setter을 실행하면 리렌더링이 된다. (위 코드엔 없지만 내부적으로 구현되어 있음)  
  
리렌더링을 한다는 것은 다시 App 컴포넌트가 호출된다는 것이다.  
그러므로 또 다시 `useState`가 실행된다.  
이때 `_val`에는 1이라는 값이 존재한다.  
(기존에 초기값으로 설정해두고 버튼을 클릭했기 때문에)  
따라서 `initialValue`을 할당하지 않으며 1의 값이 그대로 유지된다!  
  
`useState`은 반환값으로 `number`을 1으로, `setNumber`는 `_val`에 접근하는 setter을 돌려준다.  
즉, App 컴포넌트 내부에서 1의 값을 갖는 `number`가 const로 선언된다.  
  
이런 식으로 클로저의 특징을 이용해 함수형 컴포넌트에서도 상태를 유지시킬 수 있다!  
  
.  
.  
  
💭 클로저의 개념만 두루뭉술하게 알고 있었는데,  
이렇게 React Hooks 구현에 실제로 사용된 것을 보니 신기하다!  
  
> 📎 참고 자료  
https://medium.com/hcleedev/web-usestate의-동작-원리와-함정-7b4825c16b9  
