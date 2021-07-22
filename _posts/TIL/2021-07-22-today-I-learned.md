---
layout: post
title: "[TIL] 2021/07/22"
categories: [TIL]
tags: [TIL, API, OAuth 2.0, React, Webpack, React Hooks]
comment: true
---


# Today I Learned

2021/07/22

프로젝트 구현에 집중하느라 작심삼일 할 뻔 했다. 눈으로만 공부했더니 역시 기억에 잘 남지 않고 겉핥기로만 알게 되는 느낌이 커서 기록을 남긴다.

<br>

# API와 Endpoint

어렴풋이 느낌으로만 알고 있던 용어였는데, 생각난 김에 확실히 하고 넘어가려고 내용을 찾아보았다.

## [API](https://ko.wikipedia.org/wiki/API)

Endpoint를 이해하기 위해서는 먼저 API에 대해 이해해야 한다.

API는 **다른 서비스에 요청을 보내고 응답을 받기 위해 정의된 명세**를 의미한다. API를 사용하면 구현 방식을 알지 못해도 제품 또는 서비스가 서로 커뮤니케이션할 수 있으며 애플리케이션 개발을 간소화하여 시간과 비용을 절약할 수 있다. ([출처](https://www.redhat.com/ko/topics/api/what-are-application-programming-interfaces))

식당에 손님으로 방문한 경우, 요리 과정을 모르더라도 메뉴판을 보고 종업원에게 주문을 요청하면 종업원이 주방과 소통하여 음식을 내어준다. 여기서 손님이 API를 사용하는 사용자, 주문을 받는 종업원이 API, 메뉴는 API의 인터페이스, 주방의 직원은 API를 제공하는 프로그램 혹은 운영체제라고 생각하면 된다. ([참고](https://moonspam.github.io/What-is-an-API/))

## Endpoint

엔드포인트는 API를 통해 자원에 접근할 수 있게 해주는, **요청에 대한 응답을 제공하는 각각의 URL**을 의미한다. 어떤 API에 같은 메소드와 같은 URL로 요청을 하는 경우 같은 엔드포인트로 요청한 것이고, 같은 메소드라도 다른 URL을 사용하면 다른 엔드포인트로 요청한 것이 된다. ([참고 1](https://toneyparky.tistory.com/6)) ([참고 2](https://velog.io/@kho5420/Web-API-%EA%B7%B8%EB%A6%AC%EA%B3%A0-EndPoint))

<br>

# [OAuth 2.0](https://oauth.net/2/)

## [용어 정의](https://datatracker.ietf.org/doc/html/rfc6749#section-1.1)

- Resource Owner: app을 통해 본인의 리소스 사용 권한에 허가를 내리는 주체, 즉 사용자.
- Resource Server: 리소스를 가지고 있는 서버.
- Authorization Server: 인가를 전담하는 서버. 리소스 서버와 같은 서버일 수도 있고 분리되어 있을 수도 있으며, 하나의 인가 서버가 여러 개의 리소스 서버에 access token을 발행해줄 수도 있다. 리소스 서버와 인가 서버가 어떻게 상호작용하는지는 OAuth 2.0 명세 밖의 부분이다.
- Client: 리소스 서버에 접속해 허가받은 정보를 가져가 사용하는 주체, 즉 어플리케이션.

## [인증 vs 인가](https://auth0.com/docs/get-started/authentication-and-authorization#authentication-vs-authorization)

- 인증(Authentication): 사용자가 **누구인지를 확인**하는 행위. 보통 인가 이전에 이루어지며 유저가 본인이라는 것을 증명하도록 한다.

    예) 방문자가 회사 건물에 들어갈 수 있는지 확인 받는 과정. 회사에 등록된 정직원으로 인증받을 수도 있고, 누군가를 통해 인증 받을 수도 있다.

- 인가(Authorization): 사용자에게 접근 **권한이 있는지를 확인**하는 행위. 보통 인증 이후 OAuth 2.0을 통해 이루어진다.

    예) 회사 건물 관리자의 경우 거의 모든 공간에 접근 가능, 직원의 경우 특정 공간에 접근 가능, 방문자의 경우 아주 일부 공간에만 접근 가능할 텐데 이 권한을 확인하는 것.

[[참고1](https://minholee93.tistory.com/entry/OAuth20-OAuth20%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0?category=924036)] [[참고2](https://gintrie.tistory.com/36)]

## [Confidential vs Public Client](https://datatracker.ietf.org/doc/html/rfc6749#section-2.1)

### Confidential Client

Confidential client는 인가 서버와 안전하게 인증할 수 있는 어플리케이션을 말한다. Client secret을 보안상 안전하게 보관할 수 있다면 confidential하다. 웹서버 위에서 구동되는 웹 어플리케이션은 Confidential Client로 간주된다.

### Public Client

Public client는 Client secret을 사용할 수 없는, 브라우저나 모바일 기기에서 돌아가는 어플리케이션을 말한다. 유저 agent를 기반으로 한 어플리케이션, resource owner에 의해 설치되고 실행되는 native 어플리케이션은 Public Client로 간주된다.

<br>

## [OAuth2.0 인가](https://datatracker.ietf.org/doc/html/rfc6749#section-4)

access token을 요청하기 위해 클라이언트는 리소스 오너의 허가를 받는다. OAuth 2.0에는 네 가지 허가 방식이 정의되어 있다: Authorization Code Grant, Implicit Grant, Resource Owner Password Credentials Grant, Client Credentials Grant

나는 42 api에서 사용하는 방식인 Authorization Code Grant 방식에 대해서 알아보려 한다.

### [OAuth 2.0 protocol flow](https://datatracker.ietf.org/doc/html/rfc6749#section-1.2)

해당 타입에 대한 케이스를 살펴보기 전에, OAuth 2.0의 인가가 대체로 어떻게 이루어지는지 먼저 간단하게 살펴보자. 간략하게 말하면 OAuth 2.0 프로토콜은 **6단계의 흐름**을 가진다. 방향을 잘 보고 이해해보자. 이 항목 제목에 링크된 rfc의 figure 1을 보면 더 좋다. 

Client가 resource owner에게 인가 허가를 받는 작업(아래의 1, 2번)은 [figure 3](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1)처럼 인가 서버가 중개인이 되어 대리로 해주는 방식이 선호된다.

1. Client → Resource Owner / 인가 요청

    인가 요청은 리소스 오너에게 바로 보낼 수도, 인가 서버를 중간에 끼고 할 수도 있다.

2. Client ← Resource Owner / 인가 허가

    위에 나열한 네 가지 허가 방식(혹은 extension grant type) 중 하나를 사용하여 허가를 받는다.

3. Client → Authorization Server / 인가 허가 전달

    2단계에서 받은 인가 허가를 전달하며 access token을 요청한다.

4. Client ← Authorization Server / Access Token 발행

    Client를 인증하고 인가 허가를 확인한다. 유효하다면 access token을 발행한다.

5. Client → Resource Server / Access Token 전달

    4단계에서 받은 access token을 전달하며 protected resource를 요청한다.

6. Client ← Resource Server / 리소스 전달

    Access token을 확인하고 유효하다면 protected resource를 반환한다.

일단 오늘의 OAuth는 여기까지! 나머지는 내일 이어 공부하겠다.

<br>

# React

## React 개발 환경에 필요한 것

### 참고한 글

- [CRA 없이 React 개발 환경 구축하기](https://velog.io/@ksh4820/CRA-%EC%97%86%EC%9D%B4-React-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0)
- [CRA없이 React환경 구축하기](https://velog.io/@kmlee95/CRA%EC%97%86%EC%9D%B4-React%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0): TypeScript 포함
- [React 빌드(webpack) 환경 직접 구성하기 (without CRA)](https://p-iknow.netlify.app/front-end/react-webpack-config)
- [[React] Create-react-app 없이 Webpack, Babel 빌드 해보기](https://sihus.tistory.com/32)
- [Webpack 핸드북](https://joshua1988.github.io/webpack-guide/)
- [AriaFallah/WebpackTutorial](https://github.com/AriaFallah/WebpackTutorial/tree/master/ko-arahansa/part1)

### 필요한 것

- **react, react-dom**: 리액트 코어, 리액트와 DOM 연결

    → 설치 후 src, dist(or build), public 디렉토리 만들고 index.html, index.js(x) 작성

- **Babel**: ES6를 ES5로, JSX를 js로 트랜스파일링 해주는 모듈. yarn이라면 --dev 옵션, npm이라면 --save-dev 옵션(or -D 옵션)을 사용해 devDependencies에 설치해야 한다.

    → 설치 후 config 설정

- **Webpack**: 모듈 번들러. ([참고](https://github.com/AriaFallah/WebpackTutorial/tree/master/ko-arahansa/part1#why-webpack)) devDependencies에 설치해야 한다.

    웹팩에서 지칭하는 모듈이라는 개념은 자바스크립트 모듈에만 국한되지 않고 **웹 애플리케이션을 구성하는 모든 자원**을 의미한다. 웹 애플리케이션을 제작하는 데 쓰이는 파일(이미지, 스크립트, 폰트 등) 하나하나가 모두 모듈이다. 웹 애플리케이션을 구성하는 몇십, 몇백개의 자원들을 하나의 파일로 병합 및 압축 해주는 동작을 모듈 번들링이라고 한다. **빌드, 번들링, 변환 모두 같은 의미로 쓰인다**고 생각하면 된다. ([출처](https://joshua1988.github.io/webpack-guide/webpack/what-is-webpack.html#%EB%AA%A8%EB%93%88%EC%9D%B4%EB%9E%80))

    - **Webpack Loader**: 로더는 웹팩이 웹 애플리케이션을 해석할 때 자바스크립트 파일이 아닌 웹 자원(HTML, CSS, Images, 폰트 등)들을 변환할 수 있도록 도와주는 속성이다. ([출처](https://joshua1988.github.io/webpack-guide/concepts/loader.html#loader))
    - **Webpack Plugin**: 플러그인은 웹팩의 기본적인 동작에 추가적인 기능을 제공하는 속성이다. 로더는 파일을 해석하고 변환하는 과정에 관여하는 반면, 플러그인은 해당 결과물의 형태를 바꾸는 역할을 한다고 보면 된다. ([출처](https://joshua1988.github.io/webpack-guide/concepts/plugin.html))

    → 설치 후 config 설정

<br>

## React Hooks

React Hook은 React 16.8([2019/2/6](https://github.com/facebook/react/blob/main/CHANGELOG.md#1680-february-6-2019))에 새로 추가되어 함수형 컴포넌트에서도 state나 그 외 React 기능을 사용할 수 있게 해주는 함수다. 클래스 컴포넌트를 계속 사용하는 대신 함수 컴포넌트에 Hook을 추가한 동기는 [이쪽](https://ko.reactjs.org/docs/hooks-intro.html#motivation)에서 확인할 수 있다.

- [대표적인 Hook](https://ko.reactjs.org/docs/hooks-reference.html): [useState](https://ko.reactjs.org/docs/hooks-state.html), [useEffect](https://ko.reactjs.org/docs/hooks-effect.html), useMemo, useCallback, useReducer, useRef, (react-router의) [useHistory](https://reactrouter.com/web/api/Hooks/usehistory) 등

Hook은 반드시 React 함수형 컴포넌트의 [최상위(at the Top Level)에서만 호출](https://ko.reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level)되어야 한다. 즉 반복문, 조건문, 중첩된 함수 내에서 Hook을 호출하지 않아야 한다. Custom Hook을 사용하는 경우만 예외가 된다.

### [useState](https://ko.reactjs.org/docs/hooks-state.html#declaring-a-state-variable)

- 인자로 초기 state값을 하나 받으며 state는 컴포넌트가 렌더링될 때 오직 한 번만 생성된다.
- useState로 받은 state 변경 함수로 **state를 변경하면 컴포넌트가 새로 렌더링**된다. ([참고](https://velog.io/@katanazero86/react-hooks))
    - 이 내용 찾으려다가 발견한 리렌더링 관련 글들
        - [리액트 컴포넌트 Rerendering 파헤치기](https://hoontae24.github.io/posts/12)
        - [React Hooks는 어떻게 function component를 다시 그릴까?](https://awesomezero.com/development/reacthook/)
- 리액트는 상태값을 비동기적으로 변경하며 batch(일괄 처리)로 처리하려 하므로, state를 연속적으로 변경하고자 할 때에는 상태값 변경 함수에 콜백 함수를 넘겨준다. 이 콜백 함수는 이전 상태값을 매개변수로 받도록 되어 있으므로 의도한 대로 state가 변경된다.
- useState의 반환값은 [배열 구조분해](https://developer.mozilla.org/ko/docs/orphaned/Web/JavaScript/Reference/Operators/Destructuring_assignment)를 이용하여 반환된 배열의 [0]번째, [1]번째 내용물이 할당되어 나온 것이다.

### [useEffect](https://ko.reactjs.org/docs/hooks-effect.html)

- useEffect Hook은 클래스형 컴포넌트 생명주기의 componentDidMount와 componentDidUpdate, componentWillUnmount가 합쳐진 것으로 생각할 수 있다.
- useEffect는 컴포넌트가 **렌더링 이후**에 어떤 일을 수행해야하는 지를 설정할 수 있는 hook이다. 컴포넌트가 리렌더링할 때마다 effect는 모두 이전과 다른 effect로 교체되는데, 리렌더링 될 때마다 (내용은 같더라도) 새로운 함수가 생성되어 useEffect에 등록되기 때문이다. 각각의 effect는 특정한 렌더링에 종속된다고 할 수 있다. ([참고](https://ko.reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update))
- useEffect도 useState처럼 여러 번 사용할 수 있으므로 이를 통해 관심사를 분리할 수 있다. ([참고](https://ko.reactjs.org/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns))
- 특정 state가 변경되었을 때에만 side effect를 수행하고 싶다면 useEffect의 두 번째 인자로 배열을 넘겨주면 된다. 이 경우 useEffect는 해당 값을 이전과 비교하여 변경되었을 경우에만 side effect를 수행한다. ([참고](https://ko.reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects))

    배열에 넘겨주지 않은 요소는 effect 내부에서 이전 렌더링 시의 값을 참고하게 된다. 마찬가지로 빈 배열(`[]`)을 인수로 넘기면 effect 내부의 prop과 state는 [초기값을 유지](https://ko.reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)하며 mount, unmount시에만 side effect를 수행한다.

React도 여기까지! useReducer와 custom hook이 너무 생소해서 시작했는데 이 부분은 내일 마저 공부해야겠다.
