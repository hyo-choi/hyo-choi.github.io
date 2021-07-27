---
layout: post
title: "[TIL] SPA, Session, MFA, React package"
categories: [TIL]
tags: [TIL, React, SPA, Session, MFA]
comment: true
---

# Today I Learned

2021/07/27

오늘은 일이 있어서 공부를 오래 못 했다……. 거기다 SSR 공부를 하려고 SPA 정리를 했는데 정작 SSR은 뭐라 정리할 수 있을 만큼도 파악이 안 돼서 1~2시간 동안 서치만 했다. SSR은 내일 정신이 말짱할 때 다시 봐야겠다. ㅠㅠ

# [SPA](https://poiemaweb.com/js-spa#1-spa-single-page-application)

SPA는 Single Page Application의 줄임말으로 **단일 페이지로 구성된 어플리케이션을 사용하는 모던 웹 패러다임**이다. 전통적인 웹에서는 link tag(`<a>`)를 통해 이동할 때마다 리소스를 다운받아야 했고 이를 통해 전체 페이지를 새로 갱신하였기 때문에 비효율성이 컸다. SPA는 웹 어플리케이션에 필요한 모든 리소스를 최초에 한 번만 다운로드 받고, 새로운 요청이 있을 때도 페이지 갱신에 필요한 데이터만을 받아 페이지를 갱신하므로 트래픽을 줄이고 UX를 향상시킬 수 있다. 단 초기 리소스 다운로드에 시간이 오래 걸릴 수 있다는 단점과 SEO(검색엔진 최적화) 문제가 있기 때문에 다양한 해결책([코드 스플리팅](https://hyo-choi.github.io/til/2021/07/26/today-I-learned/#react-%EC%BD%94%EB%93%9C-%EC%8A%A4%ED%94%8C%EB%A6%AC%ED%8C%85), [react-helmet과 react-snap을 이용한 최적화](https://mygumi.tistory.com/385)(+ [다른 글](https://velog.io/@byseop/SPA%EC%97%90%EC%84%9C-%EC%84%9C%EB%B2%84%EC%82%AC%EC%9D%B4%EB%93%9C-%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%84-%EA%B5%AC%EC%B6%95%ED%95%98%EC%A7%80-%EC%95%8A%EA%B3%A0-SEO-%EC%B5%9C%EC%A0%81%ED%99%94%ED%95%98%EA%B8%B0)) 등)이 나오고 있다.

# 구현 방법 확인

이론은 공부했지만 실제 구현이 어떻게 되는지 잘 모르는 부분들이 있어서 구현 예시를 찾아보았다. 얼추 찾아본 결과 궁금했던 것들은 대부분 백엔드에서 구현되어야 하는 부분인 것 같았다.

## 세션 인증

- [Node.js와 express를 이용해 메모리, 파일, DB에 세션 데이터를 저장하는 예시](https://millo-l.github.io/Nodejs-express-session-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/)
- [passport.js와 DB를 이용한 세션 인증 예시](https://millo-l.github.io/Nodejs-passport-session-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/)

세션 인증 구현은 지난번에 [서버 기반 인증을 공부했을 때](https://hyo-choi.github.io/til/2021/07/23/today-I-learned/#%EC%84%9C%EB%B2%84-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D) 발견했던 예시를 제대로 읽어보았다. 일단 프론트엔드는 백엔드 서버에서 set해준 쿠키를 건드리지 않고 REST API만 잘 써주면 되는 것이고, 세션 생성과 ID 유효성 검사 등은 전부 백엔드 서버에서 이루어지는 것 같다.

- [“Keep Me Logged In” - the best approach](https://stackoverflow.com/questions/1354999/keep-me-logged-in-the-best-approach)

위 글은 세션 인증 방식에서 로그인 유지를 어떻게 하면 좋을지 찾아보다 발견한 글인데, 내용이 잘 와닿지는 않아서 일단 가볍게 읽어만 보았다. 나중에 이 내용이 필요하게 될 수도 있으니 일단 링크라도 기록은 해둔다.

## 2FA

- [2FA with Node.js and Google Authenticator](https://medium.com/@allistair.vilakazi/2fa-with-node-js-and-google-authenticator-7ddd44881493)
- [speakeasy 2.0.0](http://speakeasyjs.github.io/speakeasy/docs/speakeasy/2.0.0/index.html)
- [node-qrcode](https://www.npmjs.com/package/qrcode)

2FA 구현도 백엔드에서 주로 이루어지는 듯하다. 일반적으로는 Google Authenticator를 이용하는 것 같은데, 구글 인증은 구글 계정 전용인줄 알았더니 그게 아닌 모양이다. 플레이스토어에서 Google OTP를 다운로드하면 모바일 기기에서 인증 번호를 받을 수 있다고 한다. speakeasy를 통해 secret을 발급받고 qrcode를 통해 QR코드를 발급받을 수 있다. 사용자 입장에서는 (1) 사용자 등록을 하고 (2) 인증 중에 QR코드를 발급받아 Google Authenticator에 등록하면 된다. 다음에 로그인할 때는 1차 로그인을 하면 TOTP를 통해 2FA를 사용할 수 있다는 것 같다.

# React 패키지 살펴보기

- [자바스크립트 의존성 지옥](https://yceffort.kr/2020/11/javascript-dependency-hell)

위 글에서 패키지 의존성 관리에 대해 잠깐 살펴볼 수 있다. npm을 사용하는 경우 `npm dedup` 명령어를 사용해 공통 패키지를 재사용하고 node_modules의 크기를 줄일 수 있다고 하는데, yarn의 경우 [yarn install 명령어에서 자동으로 dedupe](https://classic.yarnpkg.com/en/docs/cli/dedupe/)을 해주므로 별도 명령어를 입력하지 않아도 된다고 한다. npm 대신 yarn을 사용할 이유가 하나 더 늘어난 것 같다.

## [React-toastify](https://fkhadra.github.io/react-toastify/introduction)

다른 프로젝트 코드를 구경하다가 toastify라는 패키지가 사용되는 것을 보았다. ([Toast popup](https://brunch.co.kr/@oemilk/91)에 대한 설명은 이쪽에서 확인할 수 있다. 이참에 Dialog, Toast, Snackbar의 생김새와 용도에 대해 제대로 파악하고 지나가자.) React-toastify([github link](https://github.com/fkhadra/react-toastify))는 **React에서 Toast popup을 사용하기 쉽도록 만들어진 패키지**로, 월간 다운로드 횟수가 2.7M이나 되는 것을 보면 다양한 프로젝트에서 흔히 사용되고 있는 것 같다.

문서를 훑어보니 application tree 안에서 `<ToastContainer />`를 한 번만 선언해주면 어디서나 toast popup을 사용할 수 있는 것 같다. `**<ToastContainer />`를 어디 두는 것이 좋을지 모르겠다면 root에 놓는 것이 좋다**고 한다. 프로젝트에서 모달 창 외에 toast popup을 사용하여 UX를 개선할 수 있는 부분이 있다면(아마 로그인 화면이나 네트워크 오류 화면 같은 곳?) 사용해보고 싶다. API 문서가 그리 길고 복잡하지는 않은 것 같으니 자세한 사용 필요할 때 확인해봐야겠다.

## [React-modal](http://reactcommunity.org/react-modal/)

React-toastify를 살펴보고 나니 다른 건 없을까 싶었다. 아직까지 나름 심적 장벽이 있는 modal창을 한 번 검색해봤는데, 역시 없을 리가 없더라. 이 패키지는 심지어 주간 다운로드 횟수가 1.1M이나 되는 것을 보니 신용할 수 있는 패키지임은 확실한 것 같다.

react-modal 패키지의 경우 `<Modal />` 컴포넌트를 사용하면 기본 parent node는 body이고, parent node를 임의로 변경할 수도 있다고 한다. 이 패키지도 직접 Modal을 구현하는 대신 패키지를 사용하자는 이야기가 나오면 그 때 한 번 공부해봐야겠다.

# 오늘 발견한 것

- [TOAST UI의 프론트엔드 가이드](https://ui.toast.com/fe-guide/ko)
- [TOAST UI의 프론트엔드 Weekly Picks](https://ui.toast.com/weekly-pick/ko)

    위 두 페이지는 NHN에서 관리하는 TOAST UI의 하위 페이지인데, TOAST UI 외에도 프론트엔드의 전반적인 가이드와 주간 article을 제공하고 있다. 틈날 때마다 하나씩 읽어보면 좋을 것 같아 일단 기록해둔다.

- [React 상태 관리 기술 소개 2021 ⚜️🌐](https://ui.toast.com/weekly-pick/ko_20210707)

    위의 Weekly Picks에서 발견한 article이다. 나는 React의 상태 관리 기술으로 Redux와 Context API만 알고 있었는데, 그 외에 Apollo의 로컬 캐시, Recoil을 사용할 수 있다는 것을 알게 되었다. 이 중 Apollo의 로컬 캐시를 사용하는 방식은 deprecate 되었다고 하는데, 그래도 [Apollo 3의 최근 구현](https://github.com/apollographql/ac3-state-management-examples)과 [이 글](https://github.com/LauraBeatris/apollo-cache-management-talks)은 읽어봤으면 한다고 한다.

- [12 Useful Packages Every Node.js Developer Should Know](https://betterprogramming.pub/12-useful-packages-every-node-js-developer-should-know-2746db760e)
- [Top 40 Node.js Packages For Increasing Developer Productivity](https://www.esparkinfo.com/node-js-packages.html)

    node.js에서 사용할 만한 패키지들을 추천하는 글 2가지이다. 위 글은 생산성 향상을 주 목적으로 하는 추천인 듯하고, 아래 글은 Angular같은 프레임워크까지 추천해주는 걸 보면 추천 범위가 좀 넓은 것 같기는 하다. 위 글에 소개된 패키지들은 한 번씩 도입을 검토해봐도 괜찮을 것 같다.

- [No Silver Bullet – Essence and Accident in Software Engineering](https://ko.wikipedia.org/wiki/%EC%9D%80%EB%B9%9B_%EC%B4%9D%EC%95%8C%EC%9D%80_%EC%97%86%EB%8B%A4) (논문 제목)

    공부하면서 '**은총알은 없다**'라는 표현을 자주 봤는데, 이것이 논문 제목이라는 것은 오늘에서야 알게 되었다. 소프트웨어 개발을 할 때에는 늘 이러한 생각을 가지고 '정답'이 아닌 최선의 선택지를 찾기 위해 노력해야 할 것이다.

- [React SSR 그 고통의 기록](https://seokjun.kim/react-ssr-the-record-of-pain/)

    SSR 내용을 찾다가 발견한 글인데, SEO를 위해 SSR을 하는 과정을 확인해 보니 'React 를 위시한 SPA 의 미래가 다소 어둡게 보인다.'라는 의견에 어느 정도 공감이 되었다. 구글에 React로 검색하면 [이제 React.js 를 버릴 때가 왔다](https://seokjun.kim/time-to-stop-react/)라는 글이 상단에 보이는 것을 보면(그런데 지금 보니 이 글과 같은 저자다?) React의 대안에 대해서도 한 번쯤 고민해보아야 할 것 같다.

# 느낀 점

## 백엔드도 공부하자

이번 프로젝트에서는 프론트엔드를 맡긴 했지만, 길게 보면 일단 백엔드 코드를 읽을 수 있는 수준까지는 공부를 해봐야 할 것 같다. 프론트엔드를 맡았다고 백엔드를 아예 모른다는 건 (적어도 지금 내 생각에는) 이상하니까…… 프론트엔드에 집중하더라도 백엔드에서 일어나는 일에 대해 어느 정도는 파악해야 할 것 같다. 당장은 아니더라도 여유가 될 때 공식 문서를 가볍게라도 한 번 훑어봐야겠다. 

## 패키지 의존에 대하여

잘 만들어진 패키지가 많으니 사실상 여기저기서 필요한 패키지만 잘 끌어다 쓰면 해야 할 일이 획기적으로 줄어들 것 같기는 한데, 한편으로는 패키지에 어디까지 의존해도 괜찮은 걸까 하는 의문도 든다. 쓰라고 만든 패키지들이지만 toast popup이나 modal을 직접 구현할 줄 모르면서 패키지만 가져다 쓸 줄 알면 좋은 개발자라고 할 수 없을 것 같다. 비단 UI에만 국한된 이야기가 아니라, qrcode 패키지처럼 '이런 것까지 할 필요는 없을 것 같은' 부분이 아니라면 한 번쯤 패키지 없이 구현해보거나 동작 원리라도 알고 사용해야 발전할 수 있지 않을까 싶다. 이 부분에 대해서는 사람마다 의견이 갈릴 것 같지만 일단 내 생각은 그렇다. (그러니까 toast와 modal 잘 만드는 방법부터 공부해봐야겠다……. ^^)

# 다음에 공부할 것

우선순위 높은 것

- 프로젝트 스프린트 재시작하면 바로 작업 할 수 있도록 준비
- HTML5 Canvas

---

우선순위 낮은 것

- [socket.io](http://socket.io/) (필요할 때 마저 공부) ([참고](https://poiemaweb.com/nodejs-socketio))
    - [broadcasting](https://socket.io/docs/v4/broadcasting-events/)
    - [rooms](https://socket.io/docs/v4/rooms/)
- GraphQL
- Next.js
- [lazy loading](https://helloinyong.tistory.com/297)
- [V8 엔진](https://helloinyong.tistory.com/290?category=832499)
- node.js 환경과 브라우환경

---

우선순위 낮고, 언급한 뒤 미뤄둔 것

- [React의 여러 가지 상태 관리 기술](https://hyo-choi.github.io/til/2021/07/27/today-I-learned/#%EC%98%A4%EB%8A%98-%EB%B0%9C%EA%B2%AC%ED%95%9C-%EA%B2%83) (Apollo의 로컬 캐시, Recoil, Redux)
