# Redux?

시작하기 전 Redux가 뭔지 알아보자. Redux는 **상태를 관리하는 라이브러리**다. Redux를 사용하면 컴포넌트의 상태 업데이트에 관한 로직을 각각의 파일로 나누어 더 효율적으로 관리할 수 있게 해주는 녀석이다.

더 알아보기 전 Redux에서 사용되는 키워드들을 먼저 정리하고가보자.

## Action

상태에 어떠한 변화가 필요할 때 Action이라는 것이 발생한다. Action은 하나의 객체로 표현되며 이는 다음과 같은 형식을 가지고 있다.

```jsx
const Action = {
	type: "ACTION_TYPE",
	data: {
		id: 1,
		content: 'Redux를 알아보자'
	}
}
```

Action 객체는 반드시 type 필드를 반드시 있어야 하며 그 type 의 이름이 Action의 이름이라고 생각하면 된다. Action 의 이름은 우리가 마음대로 정할 수있다.

### Actoin 생성 함수

Action 생성 함수는 말 그대로 Action을 만드는 함수다. 상태에 어떠한 변화를 일으켜야 할 때마다 액션 객체를 만들어야하는 번거로운 작업을 방지하기위해 Action 생성함수를 사용한다. Action 생성 함수의 형태는 다음과 같다. (Action 생성 함수 작성은 필수적이지 않음.)

```jsx
export const changeState = text => ({type: "CHANGE_STATE"});
```

## Reducer

reducer 는 변화를 일으키는 함수라고 생각하면 된다. Action을 생성하여 발생시키면 Reducer가 현재 상태와 전달받은 Action 객체를 매개변수로 받아온다. 받아온 두 값을 참고하여 새로운 상태를 만들어 주는게 Reducer다. Reducer의 형태는 다음과 같다.

```jsx
// 초기값 세팅
const initialState = {
	counter: 0
}

// reducer 함수
const reducer(state = initialState, action) {
	switch(action.type) {
		case INCREMENT:
			return { counter: state.counter + 1 };
		default:
			return state;
	}
}
```

## Store

Store는 현재의 앱 상태와, 리듀서, 내장함수 포함하는 것이다. Store는 한 어플리케이션에 하나씩 존재한다. Store를 정의하는 방법은 다음과 같다.

```jsx
const { createStore } = Redux;
const store = createStore(counterReducer);
```

## dispatch

dispatch 는 Store의 내장함수 중 하나다. dispatch는 Action은 발생시키는 것이라고 생각하면 된다. dispatch 는 Action을 매개변수로 전달한다. dispatch의 형태는 다음과 같다.

```jsx
// Action 생성자
const increment = () => ({ type: INCREMENT });
const decrement = () => ({ type: DECREMENT });

// Action 디스패치
store.dispatch(increment());
console.log(store.getState()); // 출력: 1
```

## subscribe

subscribe는 Store의 내장함수 중 하나다. subscribe 함수 안에는 리스너함수가 매개변수로 넣어 호출해 주면, 이 리스너 함수가 Action이 dispatch되어 상태가 업데이트될 때마다 호출된다. subscribe 의 형태는 다음과 같다.

```jsx
const listener =()=> {
	console.log("state is updated");
}

const unsubscribe = store.subscribe(listener);

unsubscribe(); // 구독 비활성화 시 호출하여 사용.
```

react에서는 상태를 조회하는 과정에서 react-router라는 라이브러리가 대신 해주기 때문에 할 필요가 없다. 

# Redux의 3가지 규칙

## 1. 하나의 애플리케이션 안에는 하나에 스토어

하나의 애플리케이션에는 하나에 스토어만 만들어서 사용한다. 여러개가 가능하긴 하지만 권장하지 않음.

## 2. 읽기 전용 상태

Redux는 기존의 상태를 건들이지 않고 새로운 객체를 생성해 불변성을 유지해야한다.

## 3. 리듀서는 순수한 함수

리듀서는 이전 상태와 액션 객체를 매개변수로 받는다. 리듀서는 받은 매개변수 이외에는 의존해선 안된다. 리듀서는 불변성과 멱등성을 항상 만족해주어야한다.

# Redux의 장점

## 1. 단방향 모델링

actoin 을 dispatch 할 때마다 기록이 남아서 에러를 찾기 용이하다. 시간 여행 디버깅이 가능하다.

## 2. 상태의 중앙화

Store를 통해 상태를 한 곳에서 관리하기에 전역상태를 관리할 때 좋다.

## 3. 읽기 전용

앞에서 말했듯이 Redux 는 읽기 전용이다. 즉 이전 상태로 돌아가려면 이전의 상태를 현재의 상태에 덮어쓰기만 하면 된다.

# Redux의 단점

## 1. 코드량이 늘어난다

사용할 때 마다 Action 과 같은 것들을 만들어줘야 하기 때문에 파일이나 코드량이 늘어난다.

## 2. 매번 객체를 생성해줘야한다

시간 여행 디버깅을 위해 불변성을 지켜줘야해서 매번 state(복사한) 객체를 만들어줘야한다.

## 3. 진짜로 읽기 전용이 아니다.

Redux는 상태를 읽기 전용으로 취급하긴 하지만 실제 읽기 전용으로 만들어주지 않아 상태를 변경하지 않도록 주의해야한다.

# Todo List 만들기 (Redux + React)

Redux 와 React 를 함께 사용하여 앞에서 말했던 순서대로 Todo List 를 구현해보자.

## Action type 정의

Action type 의 정의는 프로젝트가 커젔을 때 이름이 곂치는걸 방지하기 위해 ‘모듈명/액션명’ 형식으로 작성한다. 

```jsx
const CHANGE_INPUT = 'todos/CHANGE_INPUT';
const INSERT = 'todos/INSERT';
const TOGGLE = 'todos/TOGGLE';
const REMOVE = 'todos/REMOVE';
```

## Action 생성함수 생성

```jsx
let id = 1;

export const changeInput = input => ({
	type: CHANGE_INPUT,
	input
});

export const onInsertTodo = (contents) => ({
	type: INSERT,
  	todo: {
      	id: id++,
      	contents,
      	isCompleted: false
    }
});

export const onToggleTodo = (id) => ({
  	type: TOGGLE,
  	id
});

export const onRemoveTodo = (id) => ({
  	type: REMOVE,
  	id
});
```

## 초기값 세팅

```jsx
const initTodo = {
 	input: '',
  	todos: [
      	{ id: 1, contents: '리액트를 다루는 기술 공부하기', isCompleted: false },  
      	{ id: 2, contents: '운동하기', isCompleted: true }
    ]
}
```

## Reducer 함수 정의

```jsx
const todoReducer = (state = initTodo, action) => {
    const { type, input, id } = action
    const { todos } = state

    if (type === CHANGE_INPUT) { return { ...state, input } }

    if (type === INSERT) { return { ...state, todos: [...todos, action.todo] } }

    if (type === TOGGLE) {
        return {
            ...state,
            todos: state.todos.map(todo =>
                todo.id === action.id ? { ...todo, done: !todo.done } : todo
            )
        }
    }

    if (type === CHANGE_INPUT) { return { ...state, input } }

    if (type === REMOVE) {
        return { ...state, todos: todos.filter((todo) => todo.id !== id) }
    }
    return state;
}

export default todos;
```

## 루트 리듀서 만들기

createStore 라는 함수를 사용해 스토어를 만들 때는 리듀서를 하나만 써야하기 때문에 리덕스에 있는 combineReducers 라는 함수를 사용하여 함수명처럼 함수를 묶어줄 수 있다.

```jsx
import {...}

const reducer = combineReducers({todos})
```

## 스토어 만들기 & 리덕스 적용하기

Redux 에서 제공하는 Provider 컴포넌트로 App 을 감싸주면 리액트 컴포넌트에서 store 를 사용할 수 있다.

```jsx
import {...}

const store = createStore(rootReducer);

const root = ReactDom.createRoot(document.getElementById('root'));
root.render(
	<Provider store={store}>
		<App />
	</Provider>
);
```

## Connect 시켜주기

컴포넌트와 리덕스를 연동하려면 connect 라는 함수를 사용해야한다. connect 의 형태는 다음과 같다.

```jsx
const makeContainer = connect(mapStateToProps, mapDispatchToProps);
makeContainer(<Component />);
```

mapStateToProps는 스토어 안에 상태를 props로 넘겨주기 위해 설정하는 함수다.

mapDispatchToProps는 액션 생성 함수를 컴포넌트에 props로 넘겨주기 위해 사용하는 함수다. 

## TodosContainer 만들기

이제 Todos 컴포넌트를 위한 TodosContainer 를 만들어보자. 앞에서 말한듯이 connect 함수를 사용하여 만들어보자.

```jsx
import {...}

const TodoContainer = ({
	input,
	todos,
	chageInput,
	insert,
	toggle,
	remove,
}) => {
	return (
		<Todos 
			input={input}
			todos={todos}
			onChangeInput={changeInput}
			onInsert={insert}
			onToggle={toggle}
			onRemvoe={remove}
		/>
	)
}

export default conncet(
	({ todos }) => ({
		input: todos.input,
		todos: todos.todos
	}),
	{
		changeInput,
		insert,
		toggle,
		remove
	},
)(TodosContainer);
```

## Todos Component 만들기

```jsx
const TodoItem = ({ todo, onToggle, onRemove }) => {
    const Todos = ({
        input,
        todos,
        onChangeInput,
        onInsert,
        onToggle,
        onRemove
    }) => {
        const onSubmitTodo = (e) => {
            e.preventDefault();
            onInsert(input);
            onChangeInput('');
        }

        return (
            <div>
                <form onSubmit={onSubmitTodo} >
                    <input value={input} onChange={onChange} />
                    <button type="submut">등록</button>
                </form>
                <div>
                    {todos.map((todo) => (
                        <TodoItem {...todo} />
                    ))}
                </div>
            </div>
        )
    }
}
```

# Redux 를 더 편하게

## redux-actions

redux-actions 는 액션 생성 함수를 더 짧게 쓸 수 있게 해준다. 또 리듀서를 작성할 때도 조건문이 아닌 handleActions 라는 함수를 사용하여 작성 할 수 있게 해준다. 간단한 counter 를 만들면서 알아보자

```jsx
import { createAction } from 'redux-actions'

export const increase = createAction(INCREASE)
export const decrease = createAction(DECREASE)

const initialState = {
  number: 0,
}

const counterReducer = handleActions(
  {
    [INCREASE]: (state, action) => ({ number: state.number + 1 }),
    [DECREASE}: (state, action) => ({ number: state.number - 1 }),
  },
  initialState,
) 
```

위에서 사용했던 액션 생성 함수와는 확연히 더 간결해 진 걸 볼 수 있다. 

## redux-actions 액션 생성 함수에 파라미타 주기

```jsx
const MY_ACTION = 'todos/MY_ACTION';
console.log(myAction('반갑다 리덕스야~~'))
// console: { type: MY_ACTION, payload: '반갑다 리덕스야~~' }
```

액션 생성 함수에서 받아온 그대로 payload에 넣는게 아니라 변형을 시켜서 넣고싶으면 아래 코드처럼 createAction의 두번째 함수에 payload를 정의하는 함수를 따로 만들어 넣어주면 된다. 

```jsx
const myActioN = createAction(MY_ACTION, (contents) => `${contents}`)

const insert = createAction(INSERT, (payload) => ({
  id: id++,
  contents,
  isCompleted: false
})
```

# Hooks 를 사용하여 컨테이너 컴포넌트 만들기

react-redux에서 제공하는 훅들을 사용하면 컨테이너를 꼭 만들지 않아도 상태를 불러올 수 있다.

## useSelector 로 상태 조회하기

useSelector 라는 hook 을 사용하면 connect 함수를 사용하지 않고도 리덕스의 상태 조회가 가능하다. useSelecor 의 문법은 counter 를 통해 알아보자.

```jsx

const result = useSelector(state => state.counter.number);
```

위 코드에서 상태 선택 함수는 앞에서 나왔던 mapStateToProps와 같다. 

## useDispatch 로 액션 디스패치하기

```jsx
import { useSelector, useDispatch } from 'react-redux'

const TodoList =()=> {
	const todos = useSelector(state => state);
	const dispatch = useDispatch();

	return <TodoListitem 
		onIncrease={()=>dispatch(increase())}
		onDecrease={()=>dispatch(decrease())}
	/>
}
```

useDispatch 를 사용할 때는 위와 같이 useCallback과 함께 쓰는 습관을 들이는 것이 좋다고 한다.

## useStore 로 스토어 쓰기

useStore 는 컴포넌트 내부에서 스토어 객체를 직접 사용할 수 있게 해준다. 문법은 다음과 같다.

```jsx
const store = useStore();
store.dispatch({type:'EXMPLE_ACTION'});
console.log(store.getState());
// {type:'EXMPLE_ACTION'}
```

## useActions 로 유틸 훅 만들어서 쓰기

useActions 는 리덕스 개발팀이 꼭 필요하지 않다고 판단하여 실제로 출시한 훅은 아니지만 공식문서에서 복사하여 사용할 수 있다.

```jsx
import { bindActionCreators } from 'redux';
import { useDispatch } from 'react-redux';
import { useMemo } from 'react';

const useActions = (actions, deps) => {
	const dispatch = useDispatch();
  	return useMemo(
    	() => {
         if(Arrays.isArray(actions)){
          	return actions.map(a => bindActionCreators(a, dispatch))
         }
         return bindActionCreators(actions, dispatch);
        },
      deps ? [dispatch, ...deps] : deps
    );
}

export default useActions
```

useActions를 사용하면 액션함수가 여러개 있을 때 코드의 유지보수를 용이하게 해주고 가독성을 높혀준다. Todo 를 예시로 이녀석의 장점을 알아보자

```jsx
const onChangeInput = useCallback((input) => dispatch(changeInput(input)), [dispatch]);
const onInsert = useCallback((text)=>dispatch(insert(text)), [dispatch])
const onToggle = useCallback((id)=>dispatch(toggle(id)), [dispatch])
const onRemove = useCallback((id)=>dispatch(remove(id)), [dispatch])
```

```jsx
import {...}
const [onChangeInput, onInsert, onToggle, onRemove] = useActions([changeInput, insert, toggle, remove], []);
```

원래 였으면 4줄이나 나왔을 코드를 한줄만에 줄일 수 있는 개꿀 훅이다.

# 리덕스 미들웨어

리액트에서 리덕스를 사용할 때 비동기 작업을 관리해야 할 때 미들웨어를 사용하면 매우 효율적이고 편하게 상태를 관리 할 수있다. 미들웨어는 액션을 디스패치할 때 처리하기 전 저장된 작업들을 실행시킨다.

```jsx
const loggerMiddleware = store => next => action => {
		console.log(`prev : ${store.getState()}`)
  	console.log(`action : ${action}`)
  	next(action);
  	console.log(`next : ${store.getState()}`)
}
```

다음 코드를 보면 함수가 함수를 반환하고 또 그 함수가 다음 함수를 반환하는 형식이다.

미들웨어를 추가하는 방법은 다음과 같다.

```jsx
import { createStore, applyMiddleware } from 'redux'
import loggerMiddleWare from './lib/loggerMiddleWare'

const store = createStore(reducer, applyMiddleware(loggerMiddleWare))
```

## redux-logger 사용하기

redux-logger 은 앞서 사용했던 loggerMiddleWare 보다 더 유용한 라이브러리다.

알아보자~

```jsx
const logger = createLogger();
const store = createStore(rootReducer, applyMiddleware(logger));
```

## redux-thunk

미들웨어가 어떻게 동작하는지 알아봤으니 관련된 라이브러리를 알아보자. 첫 번째로 redux-thunk다.

redux-thunksms 리덕스를 사용할 때 비동기 작업을 처리할 때 가장 기본적으로 사용하는 미들웨어다.

### Thunk?

thunk는 특정 잡업을 나중에 할 수 있도록 미뤄주는 것을 말한다. 코드를 통해 더 알아보자

```jsx
const addOne = x => x + 1;
const addOneThunk = (x) => {
	const thunk = () => addOne(x);
	return thunk;
}

setTimeout(() => {
	console.log(addOneThunk(1))
},1000)
```

위와 같이 하면 addOne 작업을 나중으로 미룰 수 있다.

## thunk 생성 함수 만들기

redux-thunk는 액션 생성 함수에서 일반 액션 객체를 반환하는 대신에 함수를 반환한다. counter 를 예시로 알아보자.

```jsx
export const increaseAsync =()=> dispatch => {
	setTimeout(() => {
		dispatch(increase());
	},1000)
}

export const decreaseAsync =()=> dispatch => {
	setTimeout(() => {
		dispatch(decrease());
	},1000)
}
```

다음과 같이 코드를 짜고 실행을 해본다면 값이 1초뒤에 증가/감소하는 것을 볼 수 있을것이다.

## API 비동기 작업 처리하기

```jsx
// api 를 함수화
export const getPost = (id) => {
    axios.get('url');
}

// 상수 선언
const GET_POST = 'GET_POST';
const GET_POST_SUCCESS = 'GET_POST_SUCCESS';
const GET_POST_FAILURE = 'GET_POST_FAILURE';

// thunk 함수 생성
export const getAsyncPost = (id) => {
    dispatch({ type: GET_POST })
    try {
        const payload = (await getPost(id)).data
        dispatch({
            type: GET_POST_SUCCESS,
            payload
        })
    } catch (err) {
        dispatch({
            type: GET_POST_FAILURE,
            payload: err,
            error: true
        })
        throw err;
    }
};

// 상태 초기화
const initState = {
    loading: {
        GET_POST: false,
        GET_USERS: false
    },
    post: null,
}

const reducer = handleAction({
    [GET_POST]: (state) => ({
        ...state,
        loading: {
    ..state.loading,
            GET_POST: true
        }
    }),
    [GET_POST_SUCCESS]: (state, action) => ({
        ...state,
        loading: {
    ..state.loading,
            GET_POST: false
        },
        post: action.payload
    }),
    [GET_POST_FAILURE]: (state, action) => ({
        ...state,
        loading: {
    ..state.loading,
            GET_POST: true
        }
    }),
    initState
});
```

위 코드는 thunk 함수를 작성하는 것 보다 로딩 상태를 리듀서에게 관리하는 작업은 귀찮고 코드만 길어진다. 그래서 반복되는 녀석들을 따로 분리해서 코드를 예쁘게 다듬어보자.

```jsx
createRequestThunk = (type, request) => {
 	const SUCCESS = `${type}_SUCCESS`;
	const FAILURE = `${type}_SUCCESS`;
  
  return (params) => async (dispatch) => {
	dispatch({ type });
    try {
     	... 
    } catch (error) {
	    ...
    }
  }
}
    
createRequestThunk('GET_POST', getUsers);
```

# redux-saga

다음으로 알아볼 라이브러리는 redux-saga라는 것이다. 이 미들웨어는 함수 형태의 액션을 디스패치하여 미들웨어에서 해당 함수에 스토어의 dispatch와 getState를 파라미타로 넣어서 사용하는 원리다.

## redux-saga 를 쓰면 좋을 때

- 기존 요청을 취소
- 리덕스와 관계 없는 코드를 실행할 때
- 웹 소켓을 사용할 때
- API 요청 실패 시 재요청해야 할 때

## 들어가기 전 필요한 문법

redux-saga 를 배우기 전 알아야 할 문법들을 먼저 알아보도록 하자.

### 제네레이터 함수

제네레이터 함수는 한 함수에서 여러개의 값을 리턴시킬 수있는 함수다. 제네레이터 함수를 만들 때는 function 키워드 뒤에 *를 붙혀줘야하며 반환 시 yield 라는 키워드를 사용한다. 거두절미하고 코드로~~!!

```jsx
function* generator() {
  console.log(1);
  yield 1;
  console.log(2);
  yield 2;
  console.log(3);
  return 3;
}

const fn = generator();

fn.next();
// 1
// { value: 1, done: false }
fn.next();
// 2
// { value: 2, done: false }
fn.next();
// 3
// { value: 3, done: true }
fn.next();
// { value: undefined, done: true }
// return 뒤로는 undifined
```

## redux-saga 시작하기

```jsx
import {...}

/* 기본적 코드는 생략... */

// 마우스 클릭 이벤트가 payload에 들어가지 않도록 undifined 두번째 파라미타로 넣어주기
const increaseAsync = createAction(INCREASE_ASYNC, ()=> undifined);
const decreaseAsync = createAction(DECREASE_ASYNC, ()=> undifined);

function* increaseSaga() {
  yield delay(1000);
  yield put(increase()); // 특정 액션 디스패치
}

function* decreaseSaga() {
  yield delay(1000);
  yield put(decrease()); // ``
}

function* counterSaga() {
  // 들어오는 모든 액션에 대한 특정 작업 처리
  yield takeEvery(INCREASE_ASYNC, increaseSaga)
  // 진행중ㄱ이던 작업을 취소 처리하고 마지막으로 실행된 작업만 수행
  yield takeLatest(DECREASE_ASYNC, decreaseSaga)
}

// 사가들을 하나로~
function* rootSaga() {
  yield all([counterSaga()]) 
}
```

## redux-saga 써보기

```jsx
const sagaMiddleware = createSagaMiddleware()

const store = createStore(
  rootReducer,
  applyMiddleware(logger, thunk, sagaMiddleware) // 미들웨어 적용
)

sagaMiddleware.run(rootSaga);
```

# 마치며

redux 에 대한것들을 여럿 알아보았다. 거의 이해가 되었지만 아직 redux-thunk 와 redux-saga 는 아직 미숙하여 몇번의 복습을 거치면 내 것으로 만들 수 있을 것같다. 이런 스터디는 처음인데 관심분야를 사람들과 함께 공부하고 말 할 수 있는 기회인것 같아 열심히 해서 실력을 더 갈고 닦아야겠다. 끄으읏