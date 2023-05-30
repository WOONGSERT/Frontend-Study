### 1번 문제
다음 코드에서 res1 과 res2가 다른 이유는?
```jsx
const res1 = useQuery(['users', 'add Id']);
const res2 = useQuery(['add Id','users']);
```
### 2번 문제
useMutation에서 invalidateQueries와 다르게 유효성을 지우지 않고 값을 바꾸는 방법은?
### 3번 문제
다음 코드에서 res2에 queryFn2 를 생략해도 되는 이유는?
```jsx
import {...}

const Query = () => {
 const getUsers1 = () => {
 	const res1 = useQuery(['users'],queryFn1);
 }
 const getUsers2 = () => {
 	const res2 = useQuery(['users'],queryFn2);
 }
 return(
 	<>
     {getUsers1()}
     {getUsers2()}
    </>
 )
}
```
<details>
<summary>정답</summary>
1번 문제 정답 : res1과 res2는 언뜻보기에 같아 보이지만 React Query에서는 queryKey할당 시 입력되는 순서도 보장해주고 있기 때문에 다른 queryKey로 인식한다.

2번 문제 정답 : setQueryData를 사용하면 된다.

3번 문제 정답 : res1 과 res2가 동일한 queryKey를 사용하여 서버에 데이터를 조회하고 있기 때문에 res1의 결과를 res2가 가져와 그대로 사용하기 때문이다.
</details>
