---
layout: post
title: "[TIL] React Hooks, React-router, MFA, Axios"
categories: [TIL]
tags: [TIL, React, React Hooks, React-router, MFA, Axios]
comment: true
---

# Today I Learned

2021/07/25

진행 중인 프로젝트에서 토큰 기반 인증 대신 서버 기반 인증(세션)을 사용하기로 해서 프론트엔드에서는 access token을 다룰 일이 없게 되었다. 그래서 일단 JWT와 OAuth 공부는 잠시 미뤄두고 필요한 내용을 우선으로 공부하기로 하였다.

<br>

# React

## useContext+useReducer 보충

- [TypeScript 환경에서 리액트 Context API 제대로 활용하기](https://velog.io/@velopert/typescript-context-api)

인터넷에서 몇몇 예제를 보면 context.Provider의 value로 { state, dispatch }처럼 두 가지를 엮은 객체를 실어 보내기도 하는데, 이렇게 두 가지를 묶어 하나의 context로 보냈기 때문에 dispatch만 필요한 컴포넌트도 state가 변화할 때 함께 리렌더링 되게 된다. 낭비 렌더링을 막기 위해서는 [state와 dispatch를 서로 다른 Context로 분리](https://velog.io/@velopert/typescript-context-api#context-%EC%A4%80%EB%B9%84%ED%95%98%EA%B8%B0)하여 두 가지 context를 사용하면 된다.

또, 이 경우 index.jsx(혹은 tsx)에 .Provider를 중첩하기 보다는 [이렇게](https://velog.io/@velopert/typescript-context-api#todosprovider-%EB%A7%8C%EB%93%A4%EA%B8%B0) Provider 컴포넌트를 따로 작성하여 사용하면 깔끔하다.

매번 state와 dispatch의 유효성 검사를 하기보다는, [이런 함수를 작성](https://velog.io/@velopert/typescript-context-api#%EC%BB%A4%EC%8A%A4%ED%85%80-hooks-%EB%91%90-%EA%B0%9C-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0)하면 state와 dispatch를 보다 간편하게 사용할 수 있다. 또 이렇게 작성한 함수만 export하여 사용하면 context와 useContext를 import하지 않고도 필요한 함수만 import하여 사용할 수 있다는 장점이 있다.

## [React.memo](https://ko.reactjs.org/docs/react-api.html#reactmemo)

React.memo는 [HOC](https://hyo-choi.github.io/til/2021/07/23/today-I-learned/#hoc)이다. 어떤 컴포넌트가 동일한 props로 동일한 결과를 렌더링한다면 React.memo를 호출하고 결과를 메모이징하도록 하여 (경우에 따라)성능 향상을 누릴 수 있다.

React.memo는 **오직 props의 변화에만 영향**을 받기 때문에 React.memo로 감싸진 함수 컴포넌트 내부에서 useState, useReducer, useContext 훅을 사용한다면 **state나 context가 바뀔 때 리렌더링**된다.

React.memo는 오직 성능 최적화를 위해서만 사용되므로 렌더링을 '방지'하기 위해 사용해서는 안 된다. React.memo는 기본적으로 얕은 비교만을 수행하므로 다른 비교 동작을 원한다면 두 번째 인자로 별도의 비교 함수를 제공하면 된다. 이때 `areEqual()` 함수는 비교 대상인 props가 서로 같으면 `true`, 다르면 `false`를 반환하므로 동작 방식을 잘 파악하고 사용해야 한다.

### 얕은 비교

[얕은 복사와 깊은 복사](https://velog.io/@recordboy/JavaScript-%EC%96%95%EC%9D%80-%EB%B3%B5%EC%82%ACShallow-Copy%EC%99%80-%EA%B9%8A%EC%9D%80-%EB%B3%B5%EC%82%ACDeep-Copy)에 대해서는 알고 있었는데, 얕은 비교라는 말이 생소하기도 하고 검색했을 때 종종 나오는 내용과 [공식 문서의 설명](https://ko.reactjs.org/docs/shallow-compare.html)이 다르기도 해서 따로 정리해본다.

우선 `shallowCompare`라는 함수 자체는 현재 React(17.0.2)에서 legacy add-on이기 때문에 직접 사용할 일은 없을 것이다. 하지만 위에서 본 것처럼 React.memo가 기본적으로 얕은 비교를 수행하므로 이것이 어떤 방식으로 이루어지는지는 이해할 필요가 있다. 잘못된 설명이 종종 보인다고 했으니 공식 documentation의 설명을 먼저 살펴보자.

> It does this by iterating on the keys of the objects being compared and returning true when the values of a key in each object are not strictly equal.

**얕은 비교는 각 객체의 키-값이 [strictly equal](https://developer.mozilla.org/ko/docs/Web/JavaScript/Equality_comparisons_and_sameness)한지 아닌지를 비교**하는 방식이다. compare 함수는 일반적으로 비교 대상이 같으면 0, 아니면 그 외의 값을 반환하므로 shallowCompare 또한 두 값이 동일하지 않을 때 true(≠0 ≒false)를 반환한다. [shallowEqual 함수의 소스](https://github.com/facebook/react/blob/v16.8.6/packages/shared/shallowEqual.js)에서도 React에서 말하는 얕은 비교란 각 객체 키-값의 strict equal 여부를 확인하는 것임을 재확인할 수 있다. shallowEqual은

> 두 객체가 [Obj.is](http://obj.is/)()에서 true를 반환하면 `true` → 두 객체가 object 타입이 아니거나 null이면 `false` → 두 객체의 key 갯수가 같지 않으면 `false` → 두 객체에 서로의 키가 존재하지 않거나 그 키에 해당하는 값들이 [Obj.is](http://obj.is/)()에서 false를 반환하면 `false` (따라서 여기서는! 키에 해당하는 value가 object라면 참조값을 비교한다.) → 이 비교를 모두 통과했다면 `true`

…라는 로직을 가지고 있는데, **두 객체의 참조값이 다르면 false를 반환한다는 논리는 포함되어있지 않다.** 검색하다 보면 React의 얕은 비교가 두 객체의 참조값을 비교한다는 설명이 많이 보이는데 틀린 설명이라 보면 될 것 같고, `React.memo`를 사용할 때에는 이 내용을 잘 파악하고 사용해야 하겠다.

## [Custom Hook](https://ko.reactjs.org/docs/hooks-custom.html#using-a-custom-hook)

Custom Hook의 이름은 다른 Hook들과 같이 **'use'로 시작**한다.

**Custom Hook을 직접 호출하는 것은 해당 Custom Hook 내부의 Hook들을 직접 호출한 것**과 다름없다.

Custom Hook은 상태 관련 로직(useState, useEffect 등)을 재사용하는 메커니즘이지만 서로 다른 컴포넌트에서 Custom Hook을 사용할 때마다 그 안의 state는 완전히 독립적이다. 같은 종류의 Hook을 컴포넌트 내에서 여러 번 호출하더라도 각각의 Hook들은 완전히 독립적이라는 것과 일맥상통하게, **각각의 Custom Hook 호출은 서로 독립적인 state**를 받는다. 마찬가지로 같은 컴포넌트 내에서 Custom Hook을 여러 번 호출해도 각각의 Hook들은 독립적이다.

- [이건 왜 Hook으로 만들지 않았죠?](https://overreacted.io/ko/why-isnt-x-a-hook/)
- [핀다에서 쓰는 React Custom Hooks: Custom Hooks로 적합한 것과 그렇지 않은 것](https://medium.com/finda-tech/%ED%95%80%EB%8B%A4%EC%97%90%EC%84%9C-%EC%93%B0%EB%8A%94-react-custom-hooks-1a732ce949a5)

위 두 글은 Custom Hook으로 만들기에 적절한 것과 적절하지 않은 것에 대한 인사이트를 제공한다.

1. custom hook을 여러 컴포넌트에서 동시에 사용하였을 때(합성 시) 정상 동작하고, 그 과정을 명확히 파악할 수 있는가?

    위 글의 예제에서는 custom hook이 다른 컴포넌트의 렌더링이나 상태 변경을 막아버리는 경우를 언급했고, 아래 글의 예제에서는 두 custom hook이 같은 자원에 접근한다면 실제로 어떻게 동작할지 파악하기 어렵다는 점을 지적했다. **컴포넌트 합성에 영향을 주지 않는 코드를 custom hook으로 만들어야 한다.**

2. 디버깅을 고려했을 때 디버깅 과정이 너무 복잡해지지는 않는가?

    React의 디버깅은 대체로 상위 컴포넌트를 따라 올라가기만 하면 문제를 찾을 수 있다. 그런데 Custom Hook이 잘못 사용되면 어떤 컴포넌트에서 문제가 발생했는지 찾기 어려워지고, 원인을 찾기 위해 탐험을 떠나야 한다. 결국 이것도 1번 조건을 만족하지 못할 때 발생하는 문제인 것 같다.

3. 공통적으로 사용되는 값인가?

    다른 컴포넌트나 hook들 간에 **공통적으로 사용될 수 있는 값**(리렌더링 여부, DOM 속성 등)**은 custom hook으로 만들기에 적합하지 않다.** 이 또한 1번 조건과 비슷한 이야기인 것 같다.

반복되는 상태관리 로직을 hook으로 만들겠다는 발상은 좋지만, 그것이 늘 좋은 해결법은 아니기 때문에 아래의 조건을 잘 고려하고 custom hook을 만드는 것이 좋다. 아무래도 컴포넌트에 종속되는 값만 custom hook으로 만드는 것이 좋을 것 같다.

<br>

# [React-router](https://velopert.com/3417)

리액트에서는 다른 주소에서 다른 뷰를 보여주는 것을 라우팅이라고 한다.

Route를 사용할 컴포넌트를 BrowserRouter로 감싸주면 React-router를 사용할 수 있다.

## Route

`<Route path="..." component={...} />` 를 사용하면 **경로에 따라 원하는 컴포넌트를 렌더링**할 수 있다. 이 때 location에 path가 포함(정확히는 location의 앞부분과 부분 일치)되어있기만 해도 해당 컴포넌트가 그려지므로 path가 정확히 일치할 때에만 렌더링하고 싶다면 `exact` 키워드를 함께 작성해주어야 한다.

### Route의 props

Route로 설정한 컴포넌트는 `history` (push, replace를 통해 다른 경로로 이동, 앞뒤 페이지로 전환), `location` (현재 경로에 대한 정보. 따라서 URL 쿼리 포함), `match` (어떤 라우트에 매칭되었는지, params 정보)의 **세 가지 props를 전달**받는다.

location.pathname, match.path, match.url이 서로 혼동될 수 있는데, `<Route path="/about/:name" />` 과 실제 접속한 경로 `http://.../about/foo` 를 예시로 두고 알아보면

- `location.pathname`의 경우 현재 브라우저상의 위치 값이다. 하위 Route에서 렌더링해도 같은 값을 가진다. (`/about/foo`)
- `match.path`의 경우 Route에서 설정한 그대로의 path 값이다. params가 존재하면 params의 원형(`:`이 붙은 형태)을 함께 보여준다는 의미이다. (`/about/:name`)
- `match.url`의 경우 Route에서 설정한 path에 params 값이 들어가 치환된 값이다. (`/about/foo`)

### params

`<Route path="/about/:name" ... />` 와 같이 `:name` 를 사용하면 **컴포넌트에서 `match.params.name`를 통해 params의 값을 사용할 수 있다.** 예를 들어 위 예시에서 `/about/foo` 로 접근하면 `match.params.name`의 값이 foo가 되는 식이다. name은 임의로 지은 params 이름이므로 다른 단어로 설정해도 똑같이 동작한다.

### Switch

`Switch` 컴포넌트로 여러 개의 Route를 감싸주면 **location에 맞는 첫 번째 Route만 렌더링**해준다. 이 때 주의할 점은 상위 경로의 Route(`/about`)보다 하위 경로가 있는 Route(`/about/:name`)를 먼저 써주어야 하위 경로의 컴포넌트를 렌더링할 수 있다.

### 쿼리 파싱하기

React-router v3에는 쿼리 파싱 기능이 내장되어 있었으나 쿼리를 파싱하는 방법이 다양하기 때문에 더이상 지원하지 않는다. **`query-string` 라이브러리를 사용하면 자체적으로 구현하지 않고도 쿼리를 해석**할 수 있다. `const query = queryString.parse(location.search);` 로 쿼리 객체를 파싱할 수 있다.

### 라우트 속에서 라우트 사용하기

Route로 렌더링된 컴포넌트 내부에서 또다시 Route를 사용할 수 있다. path를 match props로 받아 `{`${match.url}/subpath...`}`처럼 작성하면 해당 Route를 렌더링한 경로의 하위 경로 지정이 가능하다.

## Link component

`<Link to="path..." />` 컴포넌트를 사용하면 <a> 태그를 사용했을 때 발생하는 **새로고침 효과 없이도 원하는 라우트로 화면을 전환**할 수 있다.

### NavLink component

`<NavLink to="path... />` 컴포넌트는 Link와 거의 똑같은데, `activeStyle`, `activeClassName` 속성을 통해 **활성화 상황에서의 스타일이나 클래스를 지정**할 수 있다. 단 Route에서와 같이 path가 포함되어있기만 해도 활성화된 것으로 간주되므로 정확한 path에서만 스타일을 적용하고 싶다면 마찬가지로 `exact`를 사용해주어야 한다.

<br>
  
# [Multi-factor Authentication](https://en.wikipedia.org/wiki/Multi-factor_authentication)

Multi-factor authentication은 적어도 다음 분류 중 두 가지에 한해 별도의 여러 증거 부분을 인증 매커니즘에 성공적으로 제시한 이후에만 사용자가 접근 권한이 주어지는 컴퓨터 접근 제어 방식의 하나이다: **지식(knowledge), 소유(possession), 속성(inherence)**. 여기에 [위치(Location)](https://en.wikipedia.org/wiki/Multi-factor_authentication#Location)도 포함되고 있는 듯하다. 참고로 [2FA와 2단계 인증(Two-Step Verification, 2SA)](http://wiki.hash.kr/index.php/%EC%9D%B4%EC%A4%91%EC%9D%B8%EC%A6%9D#.EB.B9.84.EA.B5.90)은 비슷해 보이지만 다른 인증 방식이다.

Password를 사용한 인증이 가장 널리 사용되는 **지식** 증거이다. 따라서 password를 사용하는 시스템에서 MFA의 전제를 충족시키기 위해서는 사용자가 소유 혹은 속성 요소의 증거를 제시하도록 해야 한다.

**소유** 요소에는 사용자가 물리적으로 소유하고 있는 무언가가 해당된다. Disconnected Token과 Connected Token으로 나뉜다. Disconnected Token의 경우 클라이언트 컴퓨터와 연결되지 않고, 보통 인증 데이터를 표시하기 위한 디스플레이를 가지고 있다. One-Time Password, 즉 OTP를 주로 사용한다. Connected Token의 경우 물리적으로 클라이언트 컴퓨터와 연결되는 기기를 말한다. 이러한 기기들은 데이터를 자동으로 전송하며 카드 리더, USB 토큰, wireless token 등이 이에 속한다.

**속성** 요소는 일반적으로 사용자의 생체 정보를 의미한다. 지문, 안면, 음성, 홍채 등이 인증에 사용될 수 있다.

<br>

# [Axios](https://axios-http.com/)

Axios는 promise를 기반으로 한 HTTP client library이다. 브라우저와 node.js 환경에서 사용할 수 있다.

## [AJAX](https://velog.io/@surim014/AJAX%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

Asynchronous JavaScript and XML의 줄임말으로, **자바스크립트를 이용해 서버와 브라우저가 비동기 방식으로 데이터를 교환할 수 있는 통신 기능**이다. XML 외에도 다른 데이터 오브젝트 또한 사용할 수 있기 때문에 일반적으로는 JSON을 이용하는 편이다. 예전에는 AJAX를 위해 XMLHttpRequest를 주로 사용하였으나 요즘은 이를 보완하며 나온 [promise](https://hyo-choi.github.io/til/2021/07/14/today-I-learned/) 방식의 [fetch API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)를 주로 사용한다.

기본적인 **HTTP 프로토콜**은 요청→응답을 거치면 **페이지 전체를 갱신**한다. 그러나 페이지의 일부 내용만을 갱신하고 싶다면 이런 식으로 페이지 전체를 다시 읽어오는 것은 큰 낭비가 된다. **AJAX를 사용하면** 비동기 요청을 통해 JSON이나 XML 형태로 **필요한 데이터만 받아 페이지의 일부만 갱신**할 수 있다.

## Axios vs fetch

Axios가 프론트엔드 구현 코드에서 요청을 보낼 때 종종 사용되기에 필수인가 했는데, 이번에 한 번 살펴보니 얼마 전 공부한 fetch API와 동작 방식이 그리 달라 보이지 않았다. 그래서 Axios와 fetch의 차이점을 알아보았다. 상세한 내용은 참고 링크에서 확인할 수 있다. ([참고1](https://velog.io/@kysung95/%EA%B0%9C%EB%B0%9C%EC%83%81%EC%8B%9D-Ajax%EC%99%80-Axios-%EA%B7%B8%EB%A6%AC%EA%B3%A0-fetch#axios-vs-fetch)) ([참고2](https://velog.io/@leeeeunz/TIL-35.-axios%EC%99%80-fetch%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90))

Axios는 **fetch API에서 제공하지 않는 request 취소 기능, reponse timeout API를 지원**하고 **구형 브라우저에서도 동작**한다. fetch는 내장 라이브러리이므로 모듈 설치나 import가 필요 없다는 장점이 있지만 개인적으로는 response timeout API가 없다는 것이 아쉽게 느껴졌다.

그래서 나는 fetch보다 Axios를 우위에 두고 싶고, **Axios를 공부하여 프로젝트에 적용해보려 한다**. 단 React Native 같은 경우 업데이트가 잦아서 Axios가 업데이트를 따라가지 못하는 경우가 있다고 하니 fetch를 사용하는 게 좋겠다.

## Axios API

[한국어로 된 document](https://xn--xy1bk56a.run/axios/guide/#axios%EB%9E%80)

- [Axios로 요청 보내기](https://axios-http.com/docs/api_intro): `axios(config)` , `axios(url[, config])`

    이외에 각 [HTTP 메소드별 alias](https://xn--xy1bk56a.run/axios/guide/api.html#http-%EB%A9%94%EC%84%9C%EB%93%9C-%EB%B3%84%EC%B9%AD)도 준비되어 있다.

- [사용 가능한 request config 옵션들](https://axios-http.com/docs/req_config): url만 필수, 메소드는 기본 `GET`
- [custom config instance 만들기](https://axios-http.com/docs/instance): `axios.create([config])`
- [모든 요청에 공통으로 적용되는 기본 config 설정하기](https://axios-http.com/docs/config_defaults): `axios.defaults`, `configInstance.defaults`

    이후에 적용된 config가 이전에 적용된 config를 [덮어쓴다](https://xn--xy1bk56a.run/axios/guide/config-defaults.html#%EA%B5%AC%EC%84%B1-%EC%9A%B0%EC%84%A0-%EC%88%9C%EC%9C%84-%EA%B7%9C%EC%B9%99).

- [응답 Schema](https://axios-http.com/docs/res_schema)
- [interceptor](https://xn--xy1bk56a.run/axios/guide/interceptors.html)

    then이나 catch로 처리되기 전에 요청이나 응답을 가로챌 수 있다…… 고 하는데 어떨 때 써야 하는지는 아직 잘 모르겠다.

- [오류 처리](https://xn--xy1bk56a.run/axios/guide/error-handling.html)
- [요청 취소](https://xn--xy1bk56a.run/axios/guide/cancellation.html)

    취소 토큰을 사용하여 요청을 취소할 수 있다. ([사용 예제](https://dghg.github.io/canceltoken/))

보다시피 공식 문서도 사용 예제를 위주로 할 만큼 간결하고 fetch API와 비슷한 구석이 많은 만큼 Axios를 사용하는 것 자체는 어렵지 않을 듯하다.

<br>

# 프로젝트 관련 고찰

## 세션 인증은 어떻게

현재 진행 중인 프로젝트에서 토큰 대신 세션을 이용하여 사용자를 인증하기로 결정했는데, context에 토큰 값을 넣어 관리할 필요가 없으니 지난 번에 작성한

- 새로고침을 하면 dispatch로 갱신시킨 context가 사라지고 초기 context 값으로 대체되는데, 이 경우 로그인 유지가 되지 않는다. → [해결법](https://backend-intro.vlpt.us/6/06.html) (으로 추정됨)

이 내용은 필요가 없어진 것 같다. 세션을 사용하면 사용자가 로그인을 한 경우 쿠키에 세션 ID가 저장되어 있을 테니, 프론트엔드에서는

1. root path에 접근하면 세션 ID를 가장 먼저 검증(백엔드 서버와 통신하여 확인)
2. 세션이 유효하다면 해당 사용자의 정보를 받아와 context에 저장한 뒤 → 메인 페이지 렌더링
3. 세션이 만료되었거나 쿠키에 세션 ID가 저장되어있지 않은 경우 → 로그인 화면 렌더링 후 로그인 절차
4. 로그아웃 시에는 서버에 세션 만료 요청

    백엔드 API로 요청을 보내면 백엔드에서 만료 시켜주는 방식일 듯함

이런 흐름으로 구현하면 되지 않을까 싶다. 이외에 로그인 되지 않은 상태에서 root 외의 route로 접근하면 root path로 보내주는 기능도 필요할 듯하다.

## 세션 유지는 어떻게

기본적으로 세션은 브라우저가 종료될 때 종료되는데, 새로고침은 세션을 만료시키지 않으니 상관 없지만 창을 새로 열 때마다 로그인을 새로 해야 하면 UX 측면에서 너무 별로일 것 같다.

관련 내용을 찾아보면서 지속 쿠키와 세션 쿠키 중 어느 곳에 세션 ID를 저장해야 하나 싶어 혼란을 겪었는데, 이 부분은 그냥 개념이 제대로 안 서 있어서 이상한 고민을 한 것이었다.

지속 쿠키와 세션 쿠키는 본질적으로 동일하다. [만료 기간이 정해져있지 않아 브라우저 세션 끝에 자동 만료되는 쿠키를 세션 쿠키라고 부를 뿐](https://ko.javascript.info/cookie#ref-235)이다. 심지어 이 부분은 이전에 쿠키를 공부한 부분에 [메모](https://hyo-choi.github.io/til/2021/07/16/today-I-learned/#%EC%BF%A0%ED%82%A4-%EC%98%B5%EC%85%98)까지 해두었는데 그새 잊어버렸던 거라 허탈하기도 하다…….

아무튼 서버에서 세션 ID를 쿠키에 저장할 때 한 시간~하루 정도 max-age를 설정해 주면 브라우저를 여닫을 때마다 새로 로그인 해야 하는 불상사는 방지할 수 있지 않을까 싶다. 실제로 이렇게 하면 세션을 유지할 수 있을지는 백엔드 팀원들과 실험을 해보아야 할 듯하다.

<br>

# 다음에 공부할 것

- 리액트 라우터 + [code splitting](https://velog.io/@velopert/react-code-splitting)
- toastify
- 웹소켓 (socket.io)
    - 채팅
- HTML5 Canvas
- V8 엔진 (후순위)
- 서버 사이드 렌더링, Next.js (후순위)
