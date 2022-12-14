---
layout: post
title: 'Redux 를 넘어 SWR 로(2)'
date: 2020-10-05 00:10
tags: [react, swr]
description: 
draft: false
---

[앞서 첫번째 글](/2020-10-03-swr-intro1)에서는 Redux 를 사용할 때 일반적으로 경험할 수 있는 몇가지 문제들을 언급하였습니다. 이번 글에서는 SWR 은 무엇이고 SWR 이 어떻게 Redux 를 대체할 수 있는 지에 대해 설명해 보겠습니다.


### SWR은 무엇인가?
[SWR](https://www.npmjs.com/package/swr)은 Nextjs 로 유명한 vercel 에서 만든 원격데이터 fetch 를 위한 커스텀 훅 npm 모듈입니다. SWR은 원격서버의 상태를 가져와서 리액트 컴포넌트에 꽂아주는 기능을 제공합니다.

거두절미하고 코드부터 봅시다.

```js{4-6}
import useSWR from 'swr'

function Point(){
  const {data, error} = useSWR('/api/points', url => {
    return fetch(url).then(res => res.json())
  })
  
  if(error){
    return <div>failed to load</div>
  }
  if(!data){
    return <div>Loading..</div>
  }
  return <div>{data}</div>
}
```

1. useSWR 은 첫번째 인자로 원격상태에 대한 key 를, 두번째 인자로 데이터 fetch 함수를 받습니다.
1. 첫번째 인자는 두번째 fetch 함수의 첫번째 인자로 전달됩니다.
1. fetch 함수가 데이터를 로드하면 해당 응답이 `data` 로 세팅되고 오류 발생시 해당 오류가 `error` 에 세팅됩니다.
1. 컴포넌트에서는 `data` 와 `error` 상태에 따라 알맞게 결과를 렌더링 해주면 됩니다.

아주 직관적이고 사용하기도 편하죠 😄

여기서 주목해야 할 부분은 `useSWR` 은 한번 fetch 한 원격상태의 데이터를 내부적으로 캐시하고 다른 컴포넌트에서 동일한 상태를 사용하고자 할 경우 이전에 캐시했던 상태를 그대로 리턴해 주기 때문에 서로 다른 컴포넌트가 동일한 상태를 공유할 수 있다는 점입니다. (여러 컴포넌트들에서 모두 동일하게 원격의 상태를 공유해야 하기 때문에 당연한 얘기겠지요)

서로 다른 컴포넌트가 동일한 상태를 공유한다? 이 부분은 기존의 상태관리 라이브러리들의 역할이었습니다. 그렇다면 우리는 SWR 을 조금 응용해 기존 상태관리 라이브러리가 하던 역할을 완전히 대체할 수 있지 않을까 하는 기대를 걸어볼 수 있을 것 같습니다.

이제부터 그 기대가 어떻게 현실로 이루어지는 지 보여드리도록 하겠습니다.


### 프론트엔드 개발자의 반복적인 작업

프론트엔드 개발의 대부분의 작업은 아마도 원격 서버의 상태를 UI로 표현하는 일일 것입니다. 이 일을 하기 위해 우리는 먼저 로컬 스토어 상태를 정의하고 각 화면의 컴포넌트들이 상태에 따라 어떻게 반응할 지(react) 정의합니다.

그리고 적절한 시점에 원격 서버의 데이터를 fetch 하여 로컬 스토어 상태를 초기화하고 사용자 액션을 받아서 원격상태를 추가/수정/삭제합니다. 그리고 원격상태가 변경될 때마다 그에 맞게 로컬 스토어 상태도 동기화시키는 작업을 빼먹지 말아야 겠지요.

우리 대부분은 늘 이런 일들을 합니다. **이 중 특별히 귀찮은 작업 중 하나가 데이터의 추가/수정/삭제가 발생할 때마다 원격의 상태와 로컬의 상태를 동기화시키는 일이죠. 이 작업에 대한 현기증을 누구나 한번쯤은 느끼셨을 것입니다.**

### SWR이 해결하는 문제

SWR 이 해결하고자 하는 문제의 핵심은 바로 이 지점에 있습니다. **SWR은 원격상태와 로컬상태를 하나로 통합합니다.** SWR이 원격상태의 데이터 스트림을 리액트 컴포넌트에 그대로 연결한다고 생각하셔도 좋습니다. 위에 제시된 코드를 다시 한번 보세요. 정말로 SWR이 그렇게 동작하고 있는 것 같지 않습니까?

SWR은 해당 데이터를 **마치 원격상태와 연결된 데이터 스트림으로서 바라볼 수 있도록 데이터 fetching 단계를 추상화**합니다. 여기서 눈치빠른 개발자라면 SWR 이 어떻게 HTTP 상에서 원격서버의 상태를 실시간 데이터 스트림으로 얻을 수 있단 말이냐 라고 의문을 가지실 수 있습니다. 네 당연한 질문입니다.

그 비밀은 (매우 시시하지만) SWR 이 내부적으로 **적절한 타이밍에 지속적으로 데이터를 폴링하기 때문**입니다. 사실 실시간이라는 용어를 사용하기에 민망스러울 만큼 사실 실시간으로 데이터가 fetch 되는 것은 아닙니다. 하지만 충분히 용납할 수 있는 수준으로 데이터는 최신 데이터로 갱신됩니다. SWR 은 브라우져 창이 focus 를 얻을 때 또는 네트워크가 offline 에서 online 으로 바뀔 때 자동으로 데이터를 fetch 합니다. 그 밖에 polling 주기를 직접 설정하는 것도 가능합니다.  

### SWR은 어떻게 Redux를 대체할 수 있는가
그럼 이제 SWR이 어떻게 기존 상태관리 라이브러리를 대체할 수 있는지 자세히 살펴보겠습니다. SWR 이 기존 Redux 를 대체할 수 있는 이유는 컴포넌트간 전역 상태를 공유할 수 있다는 특성 때문입니다.

많은 경우에 있어 로컬 스토어의 대부분 상태는 아마 원격 서버의 상태를 그대로 저장해 둔 것에 다르지 않을 것입니다.(Redux 를 충분히 고도화해서 사용하고 있지 않다면 😅)

우리가 상태관리 라이브러리를 사용할 때 절차는 보통 아래와 같을 것입니다.
1. 로컬의 상태를 정의한다
1. 컴포넌트에서는 스토어 상태에 따른 렌더링을 정의
1. 적절한 시점에 데이터를 fetch 하여 로컬 스토어를 초기화
    1. 그럼 자연스럽게 해당 데이터들이 컴포넌트에서 정의한대로 화면에 뿌려짐

SWR을 이용하면 위 3가지 과정을 하나로 통합할 수 있습니다. 앞서 제시된 SWR 예시 코드를 통해 이를 확인할 수 있습니다.

코드를 조금 발전시키면 원격서버와 연결된 각 데이터스트림을 상태에 따라 커스텀 훅으로 정의하여 마치 로컬의 상태를 다루듯 사용할 수 있습니다.

```js
// usePoints.js
import useSWR from 'swr'

export default () => {
  const {data, error} = useSWR('/api/points', url => {
    return fetch(url).then(res => res.json())
  })
  return {data, error}
}
```

```js
// useUsers.js
import useSWR from 'swr'

export default () => {
  const {data, error} = useSWR('/api/users', url => {
    return fetch(url).then(res => res.json())
  })
  return {data, error}
}
```

위와 같은 방식으로 SWR을 이용해 개별 상태들을 정의하면 여러 컴포넌트들에서 필요한 상태를 가져다 사용할 수 있습니다.

사용자가 사용자 정보를 수정할 경우에는 SWR 의 내부 스케쥴링에 의한 데이터갱신을 기다리기 보다 수정 즉시 화면에 변경된 데이터가 보여져야 할 것입니다. 이럴 경우에는 `mutate` 함수를 이용할 수 있습니다. `mutate` 함수가 호출되면 해당 상태를 즉시 다시 fetch 하고 데이터를 갱신합니다.

```js{10}
import useSWR from 'swr'

function UserInfo(){
  const {data, error, mutate} = useSWR('/api/users', url => {
    return fetch(url).then(res => res.json())
  })
  
  const handleChange = async (user) => {
    await updateUser(user)
    return mutate() // mutate() 는 프라미스!
  }  

  return <div>~생략~</div>
}
```

예민한 개발자라면 서버의 상태를 갱신한 후 해당 사용자의 정보를 다시 fetch 하는 것은 효율적이지 못하다고 생각하실 수도 있습니다. 어짜피 로컬에서 해당 user 의 데이터가 어떻게 갱신되었는 지를 알고 있기 때문이죠. 옳은 지적입니다. `mutate` 함수를 사용할 때 데이터 fetch 없이 로컬의 캐시되어있던 상태만 갱신하는 것도 가능합니다. 아래와 같이 말이죠.

```js{3}
  const handleChange = async (user) => {
    await updateUser(user)
    mutate(user, false) // 첫번재 인자로 갱신할 데이터, 두번째 인자로 데이터 fetch 여부를 인자로 받습니다.
  }  
```


### only 로컬상태 관리만 필요하다면?
원격상태는 사용하지 않고 오직 로컬상태만 필요한 경우도 얼마든지 있습니다. 이런 경우라도 SWR을 사용할 수 있을까요? 네 가능합니다.

SWR은 기본적으로 원격서버의 상태를 fetch 하는 데에 적합한 도구입니다. 하지만 이를 조금 응용하면 로컬의 상태만을 필요로 하는 경우에도 충분히 SWR을 이용할 수 있습니다. fetch 함수가 접근하는 원격상태가 로컬 환경에 있다고 생각하면 되겠지요.🙂

로컬 환경의 원격상태?는 필요에 따라 `window`, `sessionStorage`, `localStorage`, 클로져 변수 등을 적절하게 사용하실 수 있습니다.

앞선 글에서 처음 제시되었던 동기적 상황만을 고려하는 카운터를 Redux 없이 SWR만을 이용하면 아래와 같이 구현할 수 있습니다.

```js{3-10}
import useSWR from 'swr'

function useCounter(){
  const {data, mutate} = useSWR('state', () => window.count)

  return {data, mutate: (count) => {
    window.count = count
    return mutate()
  }}
}

function Counter(){
  const {data, mutate} = useCounter()
  
  const handleInc = () => mutate(data + 1)
  const handleDec = () => mutate(data - 1)

  return (
      <div>
        <span>count: {data}</span>
        <button onClick={handleInc}>inc</button>
        <button onClick={handleDec}>dec</button>
      </div>
  )
}
``` 

[실행 환경에서 테스트하기](https://codesandbox.io/s/swr-local-global-state-hqupl)

어떤가요? 리듀서를 정의하고 액션을 정의하고 상태변이를 위해 액션을 디스패치하는 번거로운 과정 없이 커스텀훅 하나로 이 모든 것이 간단하게 처리되었습니다!



### 결론

물론 SWR이 Redux 의 모든 기능을 대체할 수 있지는 않을 것입니다. 부족한 부분이 많이 있겠죠. Redux 와 SWR은 처음부터 해결하고자 했던 문제 자체가 달랐으니까요. 하지만 저의 짧은 경험에 비추어 볼 때 우리가 **Redux 를 사용하는 많은 경우에 있어서 번거로운 Redux 없이 SWR 만으로도 보다 쉽게 원하는 결과를 얻을 수 있을 것**이라 생각을 합니다.

이제 당신도 SWR을 프로젝트에 적극적으로 도입해보는 것은 어떨까요? SWR 을 적용하는데 어려운 부분이 있다면 언제든 피드백을 주세요. 저도 함께 관심을 가지고 고민해 보도록 하겠습니다. 😊
