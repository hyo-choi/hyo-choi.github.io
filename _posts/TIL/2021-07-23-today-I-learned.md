---
layout: post
title: "[TIL] React Hooks, 서버 기반 인증과 토큰 기반 인증"
categories: [TIL]
tags: [TIL, React, React Hooks, Session, Token]
comment: true
---



# Today I Learned

2021/07/23

<br>

# React

## [HOC](https://velopert.com/3537)

Higher order component의 줄임말으로 특정 함수 또는 값을 props 로 받아와서 사용하고 싶은 경우에 이러한 패턴을 사용한다. 리액트에 Hook이 도입되기 전에는 HOC 패턴이 자주 사용되어왔으나, **리액트에 Hook 이 도입된 이후에는 대부분의 경우 Hook으로 대체할 수 있게 되었으므로 HOC를 만들 이유가 없어졌다.** HOC를 직접 구현하게 되는 일은 거의 없기 때문에 지금 시점에 와서 HOC를 직접 작성하는 방법을 배워보거나 이해하기 위해 시간을 쏟을 필요는 없고, HOC의 용도가 "컴포넌트를 특정 함수로 감싸서 특정 값 또는 함수를 props로 받아와서 사용 할 수 있게 해주는 패턴"이라는 것 정도만 알아두면 된다고 한다. ([출처](https://react.vlpt.us/redux/09-connect.html))

<br>

## [React with Typescript](https://velog.io/@velopert/series/react-with-typescript)

- 함수형 컴포넌트의 props는 type이나 interface중 하나를 [일관적으로 사용](https://velog.io/@velopert/create-typescript-react-component#%EC%83%88%EB%A1%9C%EC%9A%B4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0)하면 된다.
- 함수형 컴포넌트는 화살표 함수를 사용하거나 `function` 을 사용하는데, [`function`을 사용하여 선언하는 추세](https://velog.io/@velopert/create-typescript-react-component#%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1)인 것 같다.
- **React.FC**는 props의 타입을 Generic으로 넣어서 사용하는데, defaultProps를 지원하지 않고 children을 optional하게 제공하므로 **사용하지 않는 편이 좋다**.

<br>

## React component lifecycle

React 17부터 componentWillMount, componentWillUpdate, componentWillReceiveProps 생명주기가 deprecated 되었기 때문에 자세한 내용을 알 필요는 없지만, **mount와 render의 차이**를 알기 위해 아래의 lifecycle 이미지를 참고하면 좋을 것 같다.

![](https://cdn.filestackcontent.com/ApNH7030SAG1wAycdj3H)

(출처: [https://www.zerocho.com/category/React/post/579b5ec26958781500ed9955](https://www.zerocho.com/category/React/post/579b5ec26958781500ed9955))

컴포넌트가 mount될 때와 update될 때 두 경우 모두 렌더링이 일어나지만, componentDidMount와 componentWillUnmount는 오직 mount, unmount될 때만 발생하는 것을 확인할 수 있다. 아래 useRef의 특성에 서술된 것처럼 '**전 생애주기를 통해 유지되는**' 이라는 설명이 있다면 해당 객체(등등)는 리렌더링에 영향 받지 않고 같은 reference를 유지할 것이다.

<br>

## React Hooks

### useState 보충

- state hook의 **[갱신 결과가 현재와 동일한 값일 경우](https://ko.reactjs.org/docs/hooks-reference.html#bailing-out-of-a-state-update) React는 자식을 렌더링하거나 무언가 실행하는 것을 회피하고 그 처리를 종료**한다. React는 이 비교에 [Object.is()](https://developer.mozilla.org/ko/docs/orphaned/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 비교 알고리즘을 사용한다.

### useEffect 보충

- useEffect에 async 함수를 파라미터로 넘겨주면 안 된다. **useEffect의 반환값은** unmount(deps에 빈 배열 넘겨준 경우)나 update(deps에 아무것도 넘겨주지 않았거나 값이 있는 배열을 넘겨준 경우) 직전의 **뒷정리를 수행하는 함수여야 하는데, async 함수는 프라미스를 반환**하기 때문에 오류가 발생한다. ([참고](https://velog.io/@velopert/react-hooks#82-usepromise))

### 공통 내용

`deps`를 받는 hook의 **`deps`에 아무것도 넘겨주지 않으면 매 렌더링마다 hook 함수가 실행된다**. 여기서 아무것도 넘겨주지 않았다는 것(두 번째 인자를 아예 쓰지 않는 것)과 빈 배열`[]`을 넘겨주는 것은 다른 의미이고 정반대의 동작을 하니 혼동하지 말아야 한다.

deps 배열이 함수에 인자로 전달되지는 않지만, 함수 안에서 참조되는 모든 값은 deps 배열에 포함되어야 한다. ([참고](https://ko.reactjs.org/docs/hooks-reference.html#usememo)) 참고로 deps는 '전달되는 배열'을 의미한다기 보다는 두 번째 인자 그 자체를 의미하므로 이 부분도 혼동하지 않도록 해야 한다.

<br>

### [useMemo](https://react.vlpt.us/basic/17-useMemo.html)

`const memoizedValue = useMemo(() => computeExpensiveValue(a, b), **[a, b]**);`

`useMemo`는 [이 예제](https://react.vlpt.us/basic/17-useMemo.html)의 `count`처럼 렌더링에 영향을 주면서 특정 상황에서만 변하는 값을 메모이제이션하여 **리렌더링 성능을 최적화하는 데 사용**되는 hook이다. 

<br>

### [useCallback](https://react.vlpt.us/basic/18-useCallback.html)

```jsx
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  **[a, b]**,
);
```

`useMemo` 는 특정 결과값을 재사용할 때 사용하는 반면, `useCallback` 은 특정 함수를 새로 만들지 않고 재사용하고 싶을때 사용한다. **컴포넌트 내부의 함수들은 컴포넌트가 리렌더링될 때마다 새로 만들어지는데**, 함수 선언 자체가 메모리 등의 리소스를 많이 차지하지는 않지만 한 번 만든 함수를 필요할 때에만 새로 만들고 재사용하는 일이 중요하다. `React.memo`와 함께 사용하면 props가 변경되지 않았을 때 리렌더링을 방지하여 **컴포넌트의 리렌더링 성능을 최적화**할 수 있다.

<br>

### [useRef](https://ko.reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)

`const refContainer = useRef(initialValue);`

useRef는 `.current` 프로퍼티로 전달된 인자(`initialValue`)로 초기화된 변경 가능한(mutable) ref 객체를 반환한다. **반환된 객체는 컴포넌트의 전 생애주기를 통해 유지**된다.

본질적으로 useRef는 .current 프로퍼티에 변경 가능한 값을 담고 있는 container와 같다. 보통은 `.current.focus()` 등을 이용해 **DOM에 접근하기 위한 용도**로 사용하지만, [이렇게](https://ko.reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables) 컴포넌트 내부에 저장하여 컴포넌트 **업데이트에 구애받지 않고 유지되는 변수처럼 사용**할 수도 있다. 단, **렌더링 중에 ref를 설정하는 것은 피해야 한다**. 일반적으로 ref는 이벤트 처리와 effect에서 수정하는 것이 좋다.

**`.current` 프로퍼티를 변형해도 리렌더링이 발생하지 않는다.**

DOM 노드에 ref를 attach/detach할 때 어떤 코드를 실행시키고자 한다면 대신 [콜백 ref를 사용](https://ko.reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node)해야 한다.

ref에 콜백 함수를 넘겨주는 것을 [콜백 ref](https://ko.reactjs.org/docs/refs-and-the-dom.html#callback-refs)라고 표현한다. **ref에 설정된 콜백 함수들은 컴포넌트의 인스턴스가 마운트 될 때 DOM 엘리먼트와 함께 호출**되는데, 따라서 `componentDidMount`나 `componentDidUpdate`보다 일찍 호출된다. *ref를 attach/detach할 때 어떤 코드를 실행시키고자 한다면 콜백 ref를 사용하라*는 말은 이러한 성질 때문에 나온 것 같다. 

콜백 ref 또한 props를 통해 다른 컴포넌트에 전달될 수 있다. 

<br>

### [useContext](https://ko.reactjs.org/docs/hooks-reference.html#usecontext)

`const value = useContext(MyContext);`

`React.createContext`에서 반환된 **context 객체를 받아 그 context의 현재 값을 반환**한다. context의 현재 값은 트리 안에서 이 Hook을 호출하는 컴포넌트에 가장 가까이에 있는 `<MyContext.Provider>`의 `value` prop에 의해 결정된다. ([출처](https://ko.reactjs.org/docs/hooks-reference.html#usecontext))

useContext에 전달하는 인자는 createContext로 만든 *context 객체 그 자체*여야 한다. 그래서 대부분의 코드에서 Context 객체를 export한 뒤 나중에 import해서 useContext를 사용하는 것이다. useContext를 호출한 컴포넌트는 **context 값이 변경되면 항상 리렌더링**된다. 만약 컴포넌트를 리렌더링 하는 것에 비용이 많이 든다면, [메모이제이션을 사용하여 최적화할 수 있다.](https://github.com/facebook/react/issues/15156#issuecomment-474590693)

**여담**: 종종 React의 Context API 관련 글에서 `<MyContext.Consumer>`라는 표현을 찾아볼 수 있는데, 이것이 **`useContext(MyContext)`와 같은 뜻**이므로 useContext Hook을 사용한다면 useContext를 사용하면 되겠다.

**context 값을 변경하려면 useReducer와 조합**해 사용해주면 된다. ([예시 1](https://egg-programmer.tistory.com/281)) ([예시 2 with TypeScript](https://dev.to/hellomuthu23/how-to-use-usereducer-and-usecontext-hooks-with-typescript-in-react-14d1))

1. **useReducer로 state, dispatch를 만들고**

    TypeScript에서는 state의 타입, reducer에서 사용될 action의 타입을 함께 정해 reducer의 인자에 typing을 해주어야 한다.

2. **createContext로 context를 만든 다음**

    TypeScript에서는 `createContext<T>`의 타입 변수 `T`를 각각 state의 타입, dispatch의 타입(혹은 둘을 포함하는 객체 타입)으로 설정해주어야 한다.

3. **context.Provider의 value에 state와 dispatch를 주입해주고**
4. **하위 컴포넌트에서 useContext를 통해 state, dispatch를 사용한다.**

1, 2는 우선순위 없이 개별 구현되어도 되는데, TypeScript에서는 위에 작성한 것처럼 context의 타입 변수를 state, dispatch의 타입으로 지정해야 하므로 1-2의 순서로 작업해야 할 것 같다.

**createContext로 만든 객체**는 그 객체의 `.Provider`와 대응하는 value를 얻어오기 위한 매개체라 생각하면 되고, **useContext로 받은 객체**는 `.Provider`가 내려보낸 value의 현재 값이다. 개인적으로 createContext로 만든 context 객체와 useContext로 받은 객체의 관계가 많이 헷갈렸는데 글을 쓰며 정리하니 이제야 개념이 제대로 서는 것 같다.

<br>

### [useReducer](https://ko.reactjs.org/docs/hooks-reference.html#usereducer)

`const [state, dispatch] = useReducer(reducer, initialArg, init);`

useReducer는 **useState의 대체 함수**이다. useState를 사용할 때보다 **더 다양한 상태를 다양한 값으로** 업데이트 하고 싶은 경우, **다음 state가 이전 state에 의존적인 경우**에 주로 사용한다.

`reducer` 함수는 `(state, action) => newState` 형태를 가지고, 현재 상태, action(업데이트를 위해 필요한 정보를 담고 있음)을 인자로 받아 새로운 state를 반환한다. 새로운 상태를 만들 때에는 useState와 마찬가지로 반드시 불변성을 지켜야 한다.

`dispatch` 함수는 reducer에 action과 state를 넘겨주는 역할을 한다. **dispatch 함수는 리렌더링 시에도 변경되지 않는다**. 따라서 useEffect나 useCallback 등의 deps에 dispatch를 포함하지 않아도 된다.

  - useReducer의 **action 객체**는 Redux에서와 달리 type 프로퍼티가 optional하고, action 그 자체가 객체가 아닌 문자열이거나 숫자라도 상관 없다. ([action을 `event.target`으로 넘겨준 예](https://velog.io/@velopert/react-hooks#42-%EC%9D%B8%ED%92%8B-%EC%83%81%ED%83%9C-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0))

  - **payload**는 action의 property 중 하나로 `action.type`처럼 `action.payload`로 reducer 내부에서 불러 쓸 수 있다. payload에는 데이터를 할당할 수 있다. [꼭 payload라는 이름일 필요는 없는 듯](https://alligator.io/react/usereducer/#example-store)하다. ([다른 이름으로 사용한 예](https://jcon.tistory.com/176)) ([payload라는 단어 자체의 개념](https://minieetea.com/2017/03/archives/5001)) 위에서 언급한 것처럼 action 객체의 타입이 자유롭기 때문에 프로퍼티 또한 원하는 이름으로 사용할 수 있는 것 같고, payload라는 프로퍼티명을 사용하는 것은 관습인 듯하다.

useState와 마찬가지로 변경 전후의 state가 Object.is() 비교 알고리즘에서 같은 값이라 판단될 경우 자식을 리렌더링하거나 effect를 발생시키지 않는다.

**콜백 함수를 props로 내려주는 방식 대신** useContext와 조합하여 [context.Provider의 value로 dispatch를 내려주는 방식을 사용하면](https://ko.reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down) 유지보수에도 편하고 depth가 깊은 컴포넌트의 업데이트에도 적합하다.

<br>

# 서버 기반 인증과 토큰 기반 인증

세션과 JWT 토큰은 각각 서버 기반 인증과 토큰 기반 인증에서 쓰이는 용어이다. ([참고 1](https://mangkyu.tistory.com/55)) ([참고 2](https://backend-intro.vlpt.us/4/))

## [서버 기반 인증](https://velopert.com/2350)

서버 기반 인증은 서버에 부담이 많이 가고 서버 확장에 어려움을 주므로(각 서버로 세션을 분산시키고 동기화해야 하는데 이 작업이 아주 까다롭다는 듯) 선호되지 않는다.

또, 세션 ID를 쿠키에 저장하는 특성 상 CORS(기본적으로 동일 도메인, 서브 도메인에만 허가)로 인해 여러 도메인에서 사용하기 어렵다는 단점이 있다.

  - **의문점**: 확장성을 고려하지 않을 만큼 작은 프로젝트에서는 세션을 사용하는 것이 간편하긴 한가?

  - **자문자답**: 두 방식 모두 장단점이 있고, 서버 기반 인증에 단점이 있다고는 하지만 현재도 사용되는 방식이므로 어플리케이션 규모나 편의 등에 따라 판단해야 하는 문제인 듯하다.

**세션 처리 과정** ([출처](https://dooopark.tistory.com/6))

  ① 사용자가 로그인 한다. (로그인 정보를 서버로 request)

  ② 서버는 request가 들어오면 DB를 쿼리 하여 사용자를 검증하고 유효할 경우 사용자의 고유한 ID값을 부여하여 세션 저장소에 저장한 후, 이와 연결되는 세션 ID를 생성하여 response header에 포함시켜 반환한다.

  ③ 사용자는 **서버에서 해당 세션ID를 받아 쿠키에 저장**을 한 후, 제한된 end point(인증이 필요한 요청)에 접근할 때 마다 **쿠키를 request header에 포함시켜 보낸다.**

  ④ 서버에서는 쿠키를 받아 세션 저장소에서 검증한 후 요청에 해당하는 데이터를 반환한다.

- [Node.js와 express를 이용해 메모리, 파일, DB에 세션 데이터를 저장하는 예시](https://millo-l.github.io/Nodejs-express-session-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/)
- [passport.js와 DB를 이용한 세션 인증 예시](https://millo-l.github.io/Nodejs-passport-session-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/)

## [토큰 기반 인증](https://velopert.com/2350)

세션의 단점을 보완하기 위해 등장한 것이 **토큰 기반 인증**.

토큰 기반 시스템은 유저의 인증 정보를 서버나 세션에 담아두지 않기 때문에 **stateless**하므로 서버 **확장성Scalability**을 보장한다는 장점이 있다. 로그인 정보가 사용되는 곳이 확장될 수 있다는 또다른 **확장성Extensibility**도 가지고 있다. 예를 들면 토큰을 사용하여 다른 서비스에서도 권한을 공유할 수 있게 되었는데, 이를 기반으로 OAuth 2.0의 선택적 권한 부여(인가)가 가능해졌다.

**JWT 토큰**은 JSON Web Token의 줄임말으로 **토큰 기반 인증 시스템의 구현체**다. 웹 표준이므로 여러 환경에서 지원되고 수많은 회사의 인프라에서 사용되고 있다.

[JWT 토큰](https://velopert.com/2389)을 저장하는 장소는 두 가지로 나뉘는데, **웹스토리지**에 토큰을 담으면 XSS 에 취약하고(브라우저에서 바로 웹스토리지에 접근 가능하므로) **쿠키**에 담으면 CSRF 에 취약해진다. 하지만, CSRF는 보안에 신경을 쓰면 차단 할 수 있으므로 **httpOnly 속성이 활성화된 쿠키를 사용하는 것이 선호된다**.

**[XSS와 CSRF(=XSRF)](https://lucete1230-cyberpolice.tistory.com/23)**

- **XSS**는 웹사이트 관리자가 아닌 사람이 웹페이지에 **악성 스크립트를 추가하는 취약점 공격 방식**이다. 웹 어플리케이션이 입력값을 제대로 검사하지 않고 사용하면 발생한다. **클라이언트를 공격**하며, 쿠키와 세션을 갈취하거나 웹사이트를 변조하는 것이 목적이다. ([사례](https://webhack.dynu.net/?idx=20161231.001))
- **CSRF**는 사용자가 **본인의 의지와 무관하게 공격자가 의도한 행위를 특정 웹사이트에 요청하게 하는 공격 방식**이다. 사용자의 권한을 탈취해 서버에 가짜 요청을 전송한다. **서버를 공격**하며, 권한을 도용해 악성 공격을 하는 것이 목적이다. ([예시](https://ko.javascript.info/cookie#ref-238)) ([사례](https://t-okk.tistory.com/80))

<br>

# 다음에 공부할 것

- custom hooks
    - [핀다에서 쓰는 React Custom Hooks: Custom Hooks로 적합한 것과 그렇지 않은 것](https://medium.com/finda-tech/%ED%95%80%EB%8B%A4%EC%97%90%EC%84%9C-%EC%93%B0%EB%8A%94-react-custom-hooks-1a732ce949a5)
- [Typescript에서 React Hooks 사용하기](https://velog.io/@velopert/using-hooks-with-typescript): React Hooks 공부한 뒤에 다시 읽어볼 것
- 새로고침을 하면 dispatch로 갱신시킨 context가 사라지고 초기 context 값으로 대체되는데, 이 경우 로그인 유지가 되지 않는다. → [해결법](https://backend-intro.vlpt.us/6/06.html) (으로 추정됨)
- JWT 보충
- OAuth 2.0 공부 이어서
- axios
