# React Query?
React Query 는 어플리케이션의 **비동기 데이터를 쉽게 관리**하고 동기화 할 수 있도록 도와주는 React 전용 라이브러리다. React Query 는 데이터의 fetching, caching, updating 과 컴포넌트 트리의 변경사항에 대한 과정을 쉽게 만들어 준다. 또한 실시간 업데이트, 페이지네이션, 오류 처리 등 다양한 기능을 제공한다. 

# React Query 장점
- 캐싱을 쉽게 해준다
- get을 한 데이터에 대하여 update를 하면 자동으로 get을 다시 수행한다.
	+ 가져온 데이터로 컴포넌트를 생성하면 데이터를 get 하는 api 가 자동으로 실행됨.
- 데이터가 오래되었다 판단되면 다시 get 해준다.
- 페이지네이션 지원.
- react hook 과 비슷함.

<hr>
오늘은 React Query에 useQuery와 useMutation에 대해 알아보는 절차를 가져보도록 하는 절차를 가져보자!
<hr>

# useQuery
`useQuery`는 서버에서 데이터를 조회해올 때 사용하는 녀석이다. 데이터베이스에 비유하면 `select` 키워드 정도라고 생각하면 된다. `useQuery`를 사용하려면 먼저 `queryKey`와 `queryFn`에 대해 알아보고 가야한다. 가보자잇~

## queryKey
`queryKey`란 useQuery마다 부여되는 고유 Key 값이다. `queryKey`는 문자열 또는 배열 형태로 사용된다. `queryKey`는 React Query가 query 캐싱을 관리할 수 있도록 도와준다.`queryKey`의 문법은 다음과 같다.
```js
const res0 = useQuery('users',queryFn);
const res1 = useQuery(['users', 'add Id']);
const res2 = useQuery(['add Id','users']);
const res3 = useQuery(['users',{type: 'add', name: 'ID'}], queryFn); 
```
`res0`은 `'users'`라는 문자열이 `queryKey`로 사용되고 있다.
`res1`과 `res2`는 언뜻보기에 같아 보이지만 React Query에서는 `queryKey`할당 시 입력되는 순서도 보장해주고 있기 때문에 다른 `queryKey`로 인식한다.
이제 예시를 통해 이놈이 무슨 역할을 하는지 알아보자.
```jsx
import {...}

const Query = () => {
 const getUsers1 = () => {
 	const res1 = useQuery(['users'],queryFn1);
 }
 const getUsers2 = () => {
 	const res2 = useQuery(['users'],queryFn2); // queryFn2는 생략해도 좋다~!
 }
 return(
 	<>
     {getUsers1()}
     {getUsers2()}
    </>
 )
}
```
위 코드에서는 `res1`과 `res2`가 동일한 queryKey를 사용하여 서버에 있는 데이터를 조회하려고 하고있다. `res1`과 `res2`에 대한 요청이 있었으므로 서버에 2개의 request가 전달될 것이라 예상했지만 위 코드에서는 **1개의 request만 전달된다**. 왜 why? `res1`에서 request를 서버에 전달하게 되면 `res2`에서는 이미 조회한 queryKey에 대한 결과값이 존재하기에 서버에 다시 요청하지 않고 `res1`의 결과를 그대로 가져와서 쓴다.<span style="color: #d2d2d2">(굳이 비유하자면 메모이제이션 느낌쓰)</span>
또 queryFn이 다르게 정의되어 있어도 `res2`는 `res1`의 결과를 그대로 받기 때문에 `queryFn1`이 처리된 결과가 확인이 가능하다.
## queryFn
`queryFn`은 query Function을 통하여 **promise 처리가 이루어지는 함수**라고 이해하면 된다. 다시 말해 aixos를 이용하여 서버에 API 요청을 하는 코드인 것이다.
`queryFn`의 문법은 다음과 같다.
```jsx
const res = useQuery({
  queryKey: ['users'],
  queryFn: () => axios.get('http://localhost:8080/users')
});
```
이제 간단한 예제를 통해 useQuery를 써보도록 하자!
useQuery는 앞서 말했듯이 서버에서 데이터를 get 할때 사용한다. useQuery의 unique Key는 고유한 값이 들어가야 하고, 호출 시 해당 키로 호출할 수 있다. Query Function은 api 사용 시 필요한 fetcher 함수가 들아가는 부분이고, Query option 은 onSuccess,onError 와 같은 옵션들이 들아가는 부분이다.
```jsx
const Func = () => {
	const { isLoading, isError, data, error } = useQuery('hello', () => axios.get(''), {
		refetchOnWindowFocus: false, // 사용자가 해당 창을 재방문 했을 때 재실행 여부
		retry: 0, // 실패시 재호출 횟수
		onSuccess: (data) => {
			console.log(data)
		},
		onError: (e) => {
			console.log(e.message)
		},
	})
}
```
`useQuery`에 첫 번째 파라미터로 고유 키가 들어가고 두번째 파리미터로는 api 호출 함수가 들어간다. `useQuery`는 비동기적으로 작동한다. 동기적으로 작동하게 하고싶으면 다음과 같이 해보자.
```jsx
const { status, data, error } = useQuery('안녕!!!', () => axios.get('zuso'), { enabled: !!check, })
```

# useMutation
`useMutation`은 React Query를 이용해 서버에 **데이터 변경 작업을 요청할 때** 사용한다. 데이터베이스와 비교하면 `insert`, `update`와 비슷한 친구다. `useMutation`을 알기 전 먼저 mutate와mutationFn를 알아봐야한다. 알아보자~
## mutationFn
mutationFn은 **promise 처리가 이루어지는 함수다.** axios를 서서 서버에 API를 요청하는 부분이라고 알면 된다. mutationFunction의 문법은 다음과 같다.
```jsx
const saveUser = useMutation({mutationFn: (user: Iuser) => axios.post('이거슨 주소다~',user)})
```
## mutate
mutate 는 useMutation을 이용해 작성한 내용들이 실행될 수 있는 트리거 역할을 한다. useMutation을 정의한 다음 이벤트가 발생하면 mutate를 사용하면된다. 간단한 예시로 알아보자.
```jsx
const [person, setPerson] = React.useState<Iperson>({
  id: 0,
  name: '',
  phone: '',
  age: 0,
})

const saveuser = useMutation((user: Iuser) => axios.post('주주줏소', user){
	onSuccess: (res) => { console.log(res) }, // try
    onError: (error) => { console.log(res) }, // catch
    onSettled: () => { console.log('끝났소') } // finally
});

const onSaveUser = () => {
  savePerson.mutate(user);
}
```
위 코드에서 `saveuser` 에서 useMutate를 정의하고 `onSaveUser()`에서 데이터를 저장한다.

## invalidateQueries
invalidateQueriessms useQuery에서 사용되는 고유키의 유효성을 없애기 위한 목적으로 사용된다. 고유키의 유효성을 제거하는 이유는 서버로부터 다시 데이터를 조회하기 위해서다.
```jsx
const queryClient = useQueryClient()
const users = useMutation((user) => axios.post('', user), {
  onSuccess: (res) => {
    console.log(res)
	queryClient.invalidateQueries('user')
},
  onError: (err) => { console.log(err) },
  onSettled: () => { console.log('ended') },
})
```
위 코드에서 `queryClinet`에서 queiryClient 값을 가져온다. 다음으로 `users`에서 고유키의 유효성을 없앤다.

## setQueryData
setQueryData는 기존에 queryKey에 매핑되어 있는 데이터를 새롭게 정의하여 데이터를 업데이트하는 방법이다. setQuery는 유효성을 제거하지 않고도 값을 업데이트 할 수 있다. 쓰는법 알아보자~
```jsx
const users = useMutation((user) => axios.post('', user), {
  onSuccess: (res) => {
	console.log(res)
	queryClient.setQueryData('user', (data) => {
		  const res = data
       	  res.data.push(user)
      	  return res
		})
	},
	onError: (err) => { console.log(err) },
	onSettled: () => { console.log('ended') },
})
```