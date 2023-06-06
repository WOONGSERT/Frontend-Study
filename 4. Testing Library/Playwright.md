## 테스팅 라이브러리로 리액트 앱 테스트 진행하기

우리가 가끔씩 어플리케이션을 설계하고 구조를 짤 때에, 단순하게 console을 통해 잡기 어려운 디버그나  
유닛과 관련한 테스트를 진행해야하는 경우가 있을 수 있다.

그래서 우리는 테스팅 라이브러리들을 통해서 테스트들을 진행하며, 현업에서도 많이 사용되고있다.  
우리는 여기서 많이 알려진 Jest와 Playwright 사용법을 알아보도록 하자.

## Jest

### Jest란?

Jest는 페이스북에서 리액트와 더불어 개발한 자바스크립트 테스팅 라이브러리이다.  
많은 개발자들이 사용하고 있는 테스팅 라이브러리이며, Jest 하나만 설치해도 테스트를 수월히 진행할 수 있다.  
Jest가 출시되기 이전에는 여러가지 라이브러리를 섞어서 사용하여 테스트를 했었는데, Jest의 출시 이후 훨씬  
테스트가 편해졌다고 한다.

거두절미하고 바로 설치 후 세팅을 해보자.

#### Jest 시작하기

```
$ yarn add -D jest @types/jest
```

그리고 package.json의 scripts 안에 있는 test의 값을 "jest"로 바꿔주자.

```json
{
	"scripts": {
		"start": "react-scripts start",
		"build": "react-scripts build",
		"test": "jest",
		"eject": "react-scripts eject"
	}
}
```

이렇게 준비하면 Jest를 프로젝트에서 사용할 수 있다!

### Module Load하기

import 키워드를 사용한 파일을 테스트하려고 하면 Jest가 올바르게 인식을 하지 못한다.  
보통 트랜스파일러를 이런 상황에서 사용하지만, 여기서는 타입스크립트를 사용해보겠다.  
먼저 패키지를 설치해보자.

```
$ yarn add -D ts-jest
$ npx ts-jest config:init
```

명령어를 실행하고 나면 jest.config.js 파일이 최상위 디렉터리에 생성된다.  
명령어가 정상적으로 작동하지 않거나 파일이 생성되지 않았을 경우, 직접 생성해주어도 된다.  
jest.config.js 파일의 내용을 다음과 같이 바꿔주자.

```js
/** @type {import('ts-jest/dist/types').InitialOptionsTsJest} */
module.exports = {
	preset: 'ts-jest',
	testEnvironment: 'node',
}
```

이제 test 파일을 생성하고, yarn test를 실행해보면 정상적으로 test가 진행된다.

### DOM 테스트 세팅하기

DOM을 테스트하려면 추가적인 라이브러리가 필요하다. 다음 명령어를 통해 설치하자.

```
$ yarn add -D @testing-library/jest-dom @testing-library/react
```

그리고 나서 setupTests.ts 파일을 생성해 다음과 같은 코드를 작성해주고 저장하면 된다.  
보통 setupTests.ts 파일은 cra로 리액트 프로젝트를 생성했을 때 디폴트로 설정되어있다.  
이는 react-scripts로 실행하는 test도 jest와 똑같은 설정이 필요하기 때문이다.

```js
require('@testing-library/jest-dom')
```

그 다음 jest.config.js 파일을 바꿔주자.

```js
module.exports = {
	preset: 'ts-jest',
	testEnvironment: 'jsdom',
	setupFilesAfterEnv: ['<rootDir>/setupTest.js'],
	globals: {
		'ts-jest': {
			tsconfig: 'tsconfig.jest.json',
		},
	},
}
```

이제 테스트 파일을 작성하여 테스트하면 테스트가 잘 이루어질 것이다.

### render 설정으로 Wrapper 적용하기

만약 Wrapper, 즉 자기 자신이 children으로 들어가있는 컴포넌트를 테스트할 때에는  
test 파일의 render에 Wrapper를 따로 작성해주어야한다. 그러나 render를 재정의하는 파일을 하나 만들어주면  
하나하나 변경하지 않고 간편하게 사용할 수 있다. 아래 코드는 Recoil를 사용했을 때의 예시다.

src/test/util/render.tsx

```js
import * as React from 'react'
import { ReactElement } from 'react'
import { render as reactRender } from '@testing-library/react'
import { RecoilRoot } from 'recoil'

const render = (ui: ReactElement, { ...options } = {}) =>
	reactRender(ui, {
		wrapper: ({ children }) => <RecoilRoot>{children}</RecoilRoot>,
		...options,
	})

export * from '@testing-library/react'
export { render }
```

다음과 같은 render를 test 파일에서 모듈로 import하여 사용하면 자동으로 Wrapper가 적용되어 테스트된다.

### 명령어들

```js
toBe(a) // 예상한 값이 매개변수와 같은 값일 것인지 확인합니다.
toEqual(obj) // 매개변수(객체)와 같은 값일 것이라 예상합니다. 객체가 가진 값의 비교가 가능합니다.
not.toBe(a) // 뒤의 결과를 부정하는 값과 비교합니다.

toBeNull() // 예상한 값이 null 인지 확인합니다.
toBeUndefined() // 예상한 값이 undefined 인지 확인합니다.
toBeDefined() // 예상한 값이 undefined 가 아닌지 확인합니다.
toBeTruthy() // 예상한 값이 truthy 한 값인지 확인합니다.
toBeFalsy() // 예상한 값이 falsy 한 값인지 확인합니다.

toBeGreaterThan(number) // number보다 큰 값인지 확인합니다.
toBeGreaterThanOrEqual(number) // number보다 크거나 같은 값인지 확인합니다.
toBeLessThan(number) // number보다 작은 값인지 확인합니다.
toBeLessThanOrEqual(number) // number보다 작거나 같은 값인지 확인합니다.
toBeCloseTo(float) // float인 매개변수와 같은 값인지 확인합니다. 부동소수점 에러를 해결하기 위해 고안되었습니다.

toMatch(string) // string을 포함하는 문자열인지 확인합니다.
toContain('item') // item을 포함하는 배열(iterator)인지 확인합니다.

toThrow() // 예외를 발생시키는지 확인합니다.
```

자세한 명령어 (매처)들은 Jest의 공식문서에 잘 설명되어있다.  
<a href="https://jestjs.io/docs/using-matchers" target="_blank">링크</a>

## Playwright

Playwright는 안정적인 자동화를 지원하는 마이크로소프트에서 만든 자동화 도구다.

여러 페이지, 도메인, iframe에 걸친 시나리오를 작성할 수 있다는 점이 장점이며,  
클릭 같은 액션을 실행하기 전에 엘리먼트가 준비될 때까지 자동으로 wait를 수행한다.  
또한 API 요청을 모킹하기 위한 인터셉터 역할도 수행해주며, 마우스, 키보드의 기본 입력 이벤트도 지원해준다.

와우 역시 E2E 라이브러리 갱갱갱 이ㅔ제 함 사용해보자!!

### 세팅하기

```
$ npm init playwright@latest
```

초기화를 하면 여러가지의 다양한 세팅을 해줄 수 있다. 이제 테스트를 진행해보자.

### 테스트 진행하기

다음 명령어를 통해 test 디렉터리 내에 있는 모든 테스트 파일을 실행시킬 수 있다.

```
$ npx playwright test
```

다음 명령어를 통해 단일 파일을 실행시킬 수 있다.

```
$ npx playwright test file-name
```

실행이 끝나면 `npx playwright show-report`로 테스트 결과를 볼 수 있다.  
이제 테스트를 진행하면 된다!! 테스트 명령어들은 공식문서에서 참고하자~~~!!
