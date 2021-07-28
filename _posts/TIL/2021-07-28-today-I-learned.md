---
layout: post
title: "[TIL] React, React-router, CSR과 SSR"
categories: [TIL]
tags: [TIL, React, React-router, CSR, SSR, SPA, fake API]
comment: true
---

# Today I Learned

2021/07/28

오늘은 오늘까지 공부한 것(Axios, React custom hook, React-router)들을 이용해 프로젝트에 응용할 수 있는 [샘플 어플리케이션](https://hyo-choi.github.io/react-test-app/)을 만들어보았다. UI에는 하나도 신경을 안 써서 정말 샘플 수준이지만 일단 로직 정도는 짤 수 있게 되어 뿌듯하다. 

그런데 이 샘플 어플리케이션의 배포 경로가 [https://hyo-choi.github.io/react-test-app/](https://hyo-choi.github.io/react-test-app/) 인데, React-router에서 path를 절대 경로 기반으로 설정해 주었더니 Link를 타면 /react-test-app이라는 경로가 사라져버렸다. (https://hyo-choi.github.io/game 으로 대치되어 버리는 식)

처음에는 환경변수로 `/react-test-app`을 설정해주고 모든 path string 앞에 붙여주려고 했는데…… 아무리 생각해도 이건 너무 기괴한 해결책인 것 같았다. 😅 그래서 검색을 해보니 `<BrowserRouter>`에 `basename` 옵션을 줄 수 있다는 것을 깨달아 이 방식으로 고쳐주었다. 지금의 내 수준에서 '이걸 이렇게까지 해야 하나?' 하는 생각이 들면 99% 확률로 더 나은 해결책이 있을 테니 **API 문서를 먼저 찾아보는 버릇을 들여야겠다**는 생각이 들었다.

# [JSONPlaceholder](https://jsonplaceholder.typicode.com/)

샘플 어플리케이션 코드를 작성하면서 백엔드 서버 없이+별도 등록이나 설정 없이 비동기 요청-응답을 연습해볼 수 있는 방법을 찾게 되었다. 역시 '이런 거 필요하다'는 생각은 다른 사람도 똑같이 하는 건지 잘 만들어진 **fake API**가 이미 존재했다. Resources에서 받아오고 싶은 fake data의 종류를 고른 뒤 클라이언트에서 요청을 보내면 잘 응답해준다. 샘플 앱에서 [로그인](https://github.com/hyo-choi/react-test-app/blob/378d568e60c09434178c75ccdd577c701d92b4db/src/component/LoginPage.tsx#L17) 파트, [커뮤니티](https://github.com/hyo-choi/react-test-app/blob/378d568e60c09434178c75ccdd577c701d92b4db/src/component/CommunityPage.jsx#L48)(추후 다른 유저의 정보를 가져와서 채워넣어야 하는 부분) 파트에 한 번씩 적용시켜 보았다.

# React

## React 앱 github page에서 배포하기

vanilla JS 프로젝트에서는 build 폴더에 있는 .js 파일을 html에 추가해주면 bulid된 앱을 확인할 수 있었고, github page 배포도 build 브랜치의 .gitignore에서 build 폴더를 제외하고 push하는 식으로 할 수 있었다. 그런데 React는 build된 html 파일이 프로젝트의 root 경로에 있지 않아 어떻게 배포를 할 수 있을까 고민하다 방법이 있겠거니 하고 검색해보았다.

- [React JS를 Github Pages에 호스팅하기](https://www.hohyeonmoon.com/blog/react-js-github-pages-deploy/)

역시 간단한 방법이 있었다. devDependencies에 `yarn add --dev gh-pages`로 gh-pages 패키지를 추가해주고 package.json에 `predeploy`, `deploy` script를 추가한 뒤 deploy 명령어만 실행해주면 된다. 자세한 내용은 위 글을 참고해보자.

## Pagination 구현하기

- [[React] 리액트 페이지네이션(pagination) 구현하기 (1)](https://chanhuiseok.github.io/posts/react-12/)
- [[React] 리액트 페이지네이션(pagination) 구현하기 (2)](https://chanhuiseok.github.io/posts/react-13/)

위 글을 참고하여 샘플 어플리케이션에서 Pagination을 구현해보았다. 막연히 어려울 것이라고만 생각했었는데, 포스트 목록만 관리할 수 있으면 어렵지 않게 구현할 수 있는 내용이었다. 위 글에서는 state를 컴포넌트 내에 여러 개 만들어 썼는데, 이런 부분을 Custom hook으로 분리해 사용하면 컴포넌트도 보다 깔끔하게 작성할 수 있고 여러 페이지에서 범용성 있게 사용할 수 있을 것 같다.

## [React-router](https://reactrouter.com/web/guides/quick-start)

[지난번](https://hyo-choi.github.io/til/2021/07/25/today-I-learned/)에 튜토리얼에 소개된 API만 살펴보고 [공식 문서](https://reactrouter.com/web/guides/quick-start)를 살펴보지 않았기에 API 문서만 간단하게 다시 살펴보았다. 

### `<Router>`

[`<Router>`는 다섯 종류](https://reactrouter.com/core/api/Router)가 있는데, [`<BrowserRouter>`](https://reactrouter.com/web/api/BrowserRouter)는 말 그대로 브라우저 환경에서, [`<NativeRouter>`](https://reactrouter.com/native/api/NativeRouter) 는 Native(mobile App) 환경에서 사용하는 기본 라우터이고, [`<MemoryRouter>`](https://reactrouter.com/core/api/MemoryRouter)와 [`<StaticRouter>`](https://reactrouter.com/core/api/StaticRouter) 는 React-router 코어에 속해있어 각각 브라우저가 아닌 환경(마찬가지로 native 용도인 듯), SSR 환경에서 사용할 수 있다. [`<HashRouter>`](https://reactrouter.com/web/api/HashRouter)는 legacy 브라우저를 지원하기 위한 라우터로 가능하면 사용하지 않고 `<BrowserRouter>`를 사용하는 것을 권장한다고 한다.

### [useRouteMatch](https://reactrouter.com/web/api/Hooks/useroutematch)

`useRouteMatch` hook을 사용하면 `<Route>`에서 `render`를 사용하지 않고도 같은 방식으로 현재 URL의 match 여부를 확인하고 컴포넌트를 그릴 수 있다. 내 생각에 컴포넌트 내부에서 이 hook을 사용하면 컴포넌트의 재사용성이 떨어질 것 같은데 실제로 많이 사용되는지는 잘 모르겠다.

### [history](https://reactrouter.com/web/api/history)

history 자체는 [HTML5 명세에 포함되어있는 웹 API](https://developer.mozilla.org/ko/docs/Web/API/History_API)이다.

- [useHistory](https://reactrouter.com/web/api/Hooks/usehistory)

    history 객체에 접근할 수 있도록 해주는 hook

- push(path[, state])

    history 스택에 새로운 entry를 추가한다.

- replace(path[, state])

    history 스택의 현재 entry를 교체한다.

- [React Router History : push와 replace의 차이점](https://velog.io/@gwak2837/React-Router-History-push%EC%99%80-replace%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)

    push를 사용하면 현재 페이지가 이력에 남고, replace를 사용하면 이력에 남지 않는다.

### [`<Prompt>`](https://reactrouter.com/core/api/Prompt)

`<Prompt>`를 사용하면 사용자가 페이지를 떠나려 할 때 Prompt를 띄울 수 있다. `when` 속성(boolean)을 통해 conditional prevent도 가능하다.

### [`<Redirect>`](https://reactrouter.com/web/api/Redirect)

`<Redirect>`를 렌더링하면 history stack의 현재 location을 덮어쓰고 다른 location으로 redirect할 수 있다. `push` 속성(boolean)을 사용하면 현재 location을 덮어쓰는 대신 history.push처럼 동작하도록 할 수도 있고, 이외에 `exact`, `strict`, `sensitive` 옵션 등도 가지고 있다.

# CSR과 SSR

- [서버사이드 렌더링 (개발자라면 상식으로 알고 있어야 하는 개념 정리 ⭐️)](https://www.youtube.com/watch?v=iZ9csAfU5Os)
- [왜 서버 사이드 렌더링이 필요할까요?](https://donggyu9410.medium.com/%EC%99%9C-%EC%84%9C%EB%B2%84-%EC%82%AC%EC%9D%B4%EB%93%9C-%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%B4-%ED%95%84%EC%9A%94%ED%95%A0%EA%B9%8C%EC%9A%94-eb41a594f94b)
- [왜 React와 서버 사이드 렌더링인가?](https://subicura.com/2016/06/20/server-side-rendering-with-react.html)

static site의 비효율성과 UX 수준을 개선하기 위해 SPA가 등장하였고, SPA는 CSR(Client Side Rendering)으로 구현되었다. 그러나 [CSR에도 단점](https://hyo-choi.github.io/til/2021/07/27/today-I-learned/#spa)이 있고, 이것을 보완하기 위해 서버에서 html을 구성하고 약간의 스크립트 파일을 함께 클라이언트에 전송해주는 방식인 SSR가 등장하게 되었다. 그런데 SSR에도 여전히 단점은 있다. Blinking issue, 서버 과부하, TTI 문제가 대표적인 단점이다. (역시 은탄환은 없다…….)

- **TTV**(Time To View): 화면이 보이는 시간
- **TTI**(Time To Interaction): 화면과 상호작용할 수 있게 되는 시간

CSR에서는 index.html이 빈 파일이므로 스크립트를 다 불러왔을 때만 보이고, 상호작용할 수 있게 된다. 따라서 CSR에서는 TTV === TTI인데, SSR은 html이 완성되어 있는 상태에서 스크립트를 불러오므로 TTV ≠ TTI이다.

CSR과 SSR 중 꼭 한 쪽만 사용해야 할 필요는 없고, React+Gastby로 **SSG**(Static Site Generation)를 수행하거나 React+Next.js(SSR 프레임워크지만 지금은 SSG도 지원)로 CSR와 SSR을 유연하게 섞어 사용할 수도 있다.

… 일단 개념적인 것은 이 정도로 정리할 수 있는데, 어떻게 SSR을 수행하는지에 대해서는 지금 공부해야 할 부분인지 잘 모르겠다. 나중에 프로젝트에 SSR을 도입할 필요가 느껴지거나 공부의 필요성이 느껴지면 그 때 좀 더 자세히 알아봐야겠다.

# 다음에 공부할 것

우선순위 높은 것

- [HTML5 Canvas](https://velog.io/@mokyoungg/React-React%EC%97%90%EC%84%9C-Canvas-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0%EB%A7%88%EC%9A%B0%EC%8A%A4-%EA%B7%B8%EB%A6%AC%EA%B8%B0)

---

우선순위 낮은 것

- [socket.io](http://socket.io/) (필요할 때 마저 공부) ([참고](https://poiemaweb.com/nodejs-socketio))
    - [broadcasting](https://socket.io/docs/v4/broadcasting-events/)
    - [rooms](https://socket.io/docs/v4/rooms/)
- GraphQL
- Next.js
- [lazy loading](https://helloinyong.tistory.com/297)
- [V8 엔진](https://helloinyong.tistory.com/290?category=832499)
- node.js 환경과 브라우저 환경

---

우선순위 낮고, 언급한 뒤 미뤄둔 것

- [React의 여러 가지 상태 관리 기술](https://hyo-choi.github.io/til/2021/07/27/today-I-learned/#%EC%98%A4%EB%8A%98-%EB%B0%9C%EA%B2%AC%ED%95%9C-%EA%B2%83) (Apollo의 로컬 캐시, Recoil, Redux)
