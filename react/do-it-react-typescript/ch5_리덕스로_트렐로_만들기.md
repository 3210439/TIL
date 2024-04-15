
## 인트로
---
do it react 책을 공부하면서 재밌어 보였던 트렐로를 만드는 방법을 공부하였다. 트렐로를 만들면서 이번 챕터에서 배웠던 redux를 재대로 사용해 볼 수 있게 되었다. 트렐로는 기본적인 기능으로 drag and drop 기능을 제공한다. 이를 위해서 이번 예제에서는 react-dnd, react-beautiful-dnd 를 사용한다. drag and drop을 사용하면 리스트에 속한 요소들의 순서가 변경될 수 있다. 이를 위해선 단순 배열이나 리스트로는 한계가 있기 때문에 redux-toolkit 에서 제공하는 createEntityAdapter를 인용한 ids와 entities로 해당 문제를 해결할 수 있다. 이번 챕터를 마무리 하면서 크게 3가지를 정리할 것이다. 첫번째는 리덕스, 두번째는 ids and entities 및 record 사용법 마지막은 dnd에 대해서 정리하려고 한다.

## Redux
---
리덕스는 애플리케이션 상태를 관리하는 라이브러리이다. 한 곳에서 정보를 관리하며 action을 통해서 값을 변경할 수 있다. 리덕스를 사용하는 이유는 다음과 같다. 점차 애플리케이션이 성장하고 컴포넌트 수가 많아짐에 따라 다수의 컴포넌트가 상태 값을 공유하는 상황이 발생하며 코드도 복잡해진다. 각각이 별도의 state값을 생성해서 관리하게 되면 개발 복잡성이 증가하고 자원 낭비로 이어질 수 있다. 이를 위해서 한 곳에서 상태 값을 관리하며 여러 컴포넌트 간에 원활한 상태 공유가 필요한데 이 문제를 Redux가 해결해준다.
일반적으로 자바스크립트를 사용하게 되면 값이 mutable 하여 변수의 값이 기본적으로 쉽게 변경될 수 있다. 이러한 단점을 redux는 값의 변경을 immutable 하게 업데이트할 수 있도록 도와준다. 리덕스에는 크게 3가지 기능이 있다. action, reducer, store이다. 

### action
---
공식 문서의 설명에 따르면 action은 애플리케이션에서 어떤 이벤트가 발생한 것을 알리는 기능을 수행한다. 액션의 실제 역할을 보면 redux store에 값을 업데이트를 위해서 사용된다. 액션은 2가지 변수를 가지는데 type과 payload이다. type은 값을 어떻게 변경할지에 관한 문자열이며 "domain/eventName" 과 같은 규칙을 지닌다. 이러한 규칙을 가지는 한 곳에서 데이터를 관리하며 규모가 커질 수록 구분을 잘 하기 위해서이다. 그리고 payload에는 변경할 값이 담기게 된다. action에는 값을 변경 시키는 로직은 포함되지 않는다. 값을 변경하는 로직은 reducer에 포함된다.

```javascript
const addTodoAction = {  
type: 'todos/todoAdded',  
payload: 'Buy milk'  
}
```

### reducer
---
액션에 업데이트를 원하는 값을 담게 되면 dispatch에 해당 액션을 담아서 reducer에 전달된다. reducer에서 다양한 종류의 액션을 처리하며 switch 문을 사용해서 여러 액션을 처리한다. 그리고 실질적인 변경 코드가 reducer에 담기게 된다. reducer를 통해 값이 변경된 후 업데이트된 상태 값이 최종적으로 store에 저장된다. 기본적인 코드는 다음과 같다.

```javascript
const initialState = { value: 0 }  
  
function counterReducer(state = initialState, action) {  
// Check to see if the reducer cares about this action  
if (action.type === 'counter/increment') {  
// If so, make a copy of `state`  
return {  
...state,  
// and update the copy with the new value  
value: state.value + 1  
}  
}  
// otherwise return the existing state unchanged  
return state  
}
```

### Store
---
리덕스의 상태값이 관리되는 곳을 store라고 한다. Store는 reducer를 통해 생성된 값이 저장된다. 기본적으로 getState라는 메소드를 통해서 값을 얻어 확인할 수 있다. 기본적으로 store에서 제공하는 dispatch 메소드를 통해서 store에 값 변경을 시도할 수 있으며 dispatch는 action 값을 매개변수로 받는다. 그리고 selector를 통해서 값을 불러올 수 있도록 한다. 기본적으로 다음과 같이 사용할 수 있다.

```javascript
import { configureStore } from '@reduxjs/toolkit'  
  
const store = configureStore({ reducer: counterReducer })  
  
console.log(store.getState())  
// {value: 0}
```


## List보다 데이터 위치 변경이 간편한 ids, entities
---
### ids
---
ids는 개별 엔티티를 식별하는 고유한 값이 저장된다. ids에서 데이터의 위치 변경을 자유롭게  할 수 있도록 id값과 entity의 id 값이 저장되어 순서를 알 수 있게 된다.

### entities
---
entities란 객체를 하나 만들어서 id_값: 아이템 형태로 여러 개의 아이템을 저장한다. 타입스크립트를 사용할 때는 주로 타입스크립트가 제공하는 Record 제네릭 타입을 사용한다. 자바스크립트에서는 일반적으로 다음과 같이 값을 얻을 수 있다. -> card['uuid']
하지만 해당 방식은 문자열이 잘못된 경우 코드를 실행 시키고 나서야 잘못된 것을 알 수 있다. 이러한 문제를 해결하기 위해서 타입스크립트에서는 Record 타입을 사용하도록 한다. Record 타입을 사용하기 위해서는 Record<키_타입, 값_타입> 형태로 2개의 타입 변수를 지정해야 한다. Record 타입은 색인 연사자를 사용해 객체의 특정 속성 값을 설정하거나 얻을 수 있다.

## React-dnd, React-Beautiful-dnd
---
react dnd, react beautiful dnd 모두 드래그엔 드랍을 구현하기 위해서 사용된다. 책에서는 React dnd를 사용하다가 react beautiful dnd를 사용하는 예제로 넘어가게 된다. 트렐로에서는 카드 리스트를 드래그엔 드랍 할 수 있고 카드 리스트안에 있는 카드들을 다른 리스트의 요소로 드래그엔 드랍도 할 수 있다. 다른 리스트의 요소를 다른 리스트로 드래그엔 드랍 하기 위해서는 React-dnd로는 구현할 수 없는 기능이기 때문에  react-beautiful-dnd를 사용하게 된다. 하지만 react-beautiful-dnd는 1년 전에 업데이트가 멈춘 모양이며 rpm으로 패키지를 다운로드할 때 legacy 옵션을 줘야 다운로드 할 수 있다. github에 react-beautiful-dnd의 예제가 있는데 이해하는데 매우 도움이 된다.

참조 <br>
https://github.com/atlassian/react-beautiful-dnd <br>
https://redux.js.org/tutorials/essentials/part-1-overview-concepts <br>
https://product.kyobobook.co.kr/detail/S000200550965 <br>
