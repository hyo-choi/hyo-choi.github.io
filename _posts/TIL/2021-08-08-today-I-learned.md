---
layout: post
title: "[TIL] 8월 2주차 스프린트 회고"
categories: [TIL]
tags: [TIL, React, axios, Agile, scrum, HTTP, CORS]
comment: true
---

# TIL

2021/08/08, 지난주(8/2~8/6) 작업 회고

현재 진행중인 팀 프로젝트에 애자일 방법론 중 [스크럼](https://medium.com/dtevangelist/scrum-dfc6523a3604) 방식을 도입하여 작업하고 있다. 프로젝트 시작 전 팀원분께서 스크럼 방법론을 소개해주셨고, 일정 기간 동안+구현할 기능을 기준으로 진행할 수 있다는 점이 좋다고 생각해 우리 프로젝트에도 도입하게 되었다.

우리 팀은 시니어 팀도 아니고 상용 어플리케이션을 개발하는 것도 아니기 때문에 한 회차를 1주일으로 잡았고, 그 중 5일을 스프린트+버퍼 기간, 하루를 회고 기간, 하루를 휴식 기간으로 두었다.

스프린트 기간 동안 미리 정해둔 목표를 달성해야 하는 스크럼 방법론의 특성 상 구현과 이론 정리를 동시에 하는 것은 어렵다고 느꼈다. 그래서 **본 프로젝트 진행 중에는 주당 1-2회 정도 프로젝트에 어떤 기술을 사용하였는지, 어떤 내용을 배웠고 어떤 부분을 보충해야 할지를 TIL에 정리해보려고 한다.**

스크럼 방법론에 대한 [비판 글](https://jinhojapan.tistory.com/72)도 있는데, 스크럼 방법론을 적용해본 경험에 기반해 읽어보니 어느 정도 공감가는 부분이 있었다. 같은 기능을 위해 작업한다 하더라도 맡은 부분에 따라 구현 난이도가 다르고 구현에 걸리는 시간이 달라지기 때문에 작업에 비효율이 생길 수 있다는 부분이었다. 팀이 가진 인적 자원을 정확히 파악하고 있지 않으면 목표 설정부터 잘못될 수 있다는 점 또한 스크럼의 어려운 점인 것 같다. 우리 팀 프로젝트의 목표에는 이 방법이 잘 맞는 것 같아 괜찮다고 생각하지만, 팀이 커지면 커질수록 비효율적으로 굴러가기 쉬울 것 같기는 하다.

---

# 회고 내용

8월 2주차 스크럼 회고 기간에 작성한 회고 내용이다.

## 전체

- 작업을 적절한 분량으로 분배하고 진행하는 것이 어렵다는 것을 깨달았습니다. 인적 자원을 잘 활용하기 위해서는 할 수 있는 것, 못 하는 것을 정확히 파악하는 것이 중요한 것 같습니다.
- 백엔드 팀과 협업하는 방법을 경험을 통해 배워나가는 중입니다. 프론트엔드와 백엔드는 각자의지향점을 갖고 같은 이슈라도 처리하는 방법이 다르기 때문에, 서로의 영역을 존중하는 것과 서로가 사용하는 기술에 대해 어느 정도 이해하고 있는 것이 중요하다는 생각이 들었습니다. 당장은 프로젝트 완수가 최우선 목표이므로 미뤄두겠지만 프로젝트가 완료되면 백엔드도 공부해보고 싶습니다.
- 겉핥기로만 공부하는 습관이 나쁘다는 것은 알고 있으나 스프린트의 목적 상 구현을 우선해야 하기 때문에 밸런스를 잡기 어렵다고 느꼈습니다. 또, 스크럼 방법론에서 회고 기간과 휴식 기간을 따로 두는 이유가 이런 점을 보충하기 위해서라는 것을 깨달았습니다. 앞으로 스프린트 전 예습, 스프린트 후 복습을 철저히 해야 할 것 같습니다.
- 회고 시간에 이야기를 나누면서 제가 구현에만 집중하고 그 저변의 코어 기술에 대해서는 잘 알아보지 않는다는 것을 깨달았습니다. 좋은 개발자가 되려면 코어를 잘 이해하고 있어야 한다고는 생각해왔으나 정작 이 부분에 소홀했던 것에 반성했고 갈 길이 멀다고 생각했습니다. 남는 시간에 마냥 쉬기만 할 것이 아니라 평소 흥미를 느끼던 부분부터 조금씩 살펴봐야겠습니다.

## React

- React Context API, custom hook을 사용하는 데 익숙해졌습니다. ([관련 PR 링크](https://github.com/404-DriverNotFound/frontend-b/pull/25)) 이론만 공부했을 때에는 사용법이 와닿지 않아 어려웠는데, 실제 프로젝트에서 사용될 코드를 작성하며 useReducer hook과 어떻게 연관하여 사용하는지, custom hook을 어떻게 사용하여 더욱 간결한 코드를 작성할 수 있는지 깨달았습니다. 앞으로 프로젝트에 custom hook을 적용할 만한 부분이 있으면 적극적으로 검토해보고 싶습니다.
- useEffect를 좀 더 효율적으로 사용하기 위해 useRef를 이용하는 custom hook을 사용해보았습니다. ([관련 커밋 링크](https://github.com/404-DriverNotFound/frontend-b/pull/40/commits/de6f5fe77ea2c9aed19ec1ab6057780e16ba707e)) 이전에 React를 처음 배우기 위한 과제를 진행할 때에는 useEffect를 어떻게 사용하면 좋을지 알 수가 없어 [정말 이상한 코드](https://github.com/hyo-choi/react-payments/blob/main/src/components/templates/CardRegisterForm/CardRegisterForm.tsx)를 작성했었는데(지금 보니 Page여야 할 것이 Template으로 작성되어 있기도 하네요…….) 새로운 방법을 배웠으므로 앞으로는 비슷한 문제에 능숙하게 대응할 수 있을 것 같습니다.
- 사용시의 장단점을 고려하지 않고 atomic design을 도입했었는데, 컴포넌트가 이미 잘 만들어져 있는 Material-UI를 사용하다 보니 atomic design의 필요성을 느끼기 어려울 때도 있었습니다. 은탄환은 없다는 말처럼 모든 케이스에 잘 들어맞는 기술은 없으므로 프로젝트의 성질에 따라 기술을 적절히 도입하는 것이 중요하겠다는 것을 다시금 느꼈습니다.

## HTTP

- API에 비동기 요청을 보내고 응답을 적절히 처리할 수 있게 되었습니다. ([관련 PR 링크](https://github.com/404-DriverNotFound/frontend-b/pull/30)) axios는 편하게 사용할 수 있는 라이브러리지만 만능이 아니라는 점을 깨달았고({ responseType: 'stream' }을 [받지 못하는](https://yogae.github.io/etc/2019/06/11/node_client_stream.html) 등) fetch를 사용해 받은 응답(오류 응답도 네트워크 오류 등이 아니라면 resolve)과 axios를 사용한 응답(오류 응답(400, 500번대 등)까지도 reject)에 차이가 있다는 점도 알게 되었습니다.
- 과제 진행중 프론트엔드+백엔드 양쪽에서 withCredentials 옵션을 통해 CORS를 허용하고 문제를 해결한 부분이 있었는데, 당장 문제를 해결하는 데 급급하여 어떤 특성 때문에 이런 해결이 가능했는지 찾아보지 못했습니다. 이번 회고 및 휴식 기간에 공부하고 넘어가려 합니다. ([관련 커밋 링크](https://github.com/404-DriverNotFound/frontend-b/pull/30/commits/9db282fc7c1d07906fd6ce261ecb08bb29a935fb))
- Content-type: image/png인 이미지를 응답으로 전송받아 프론트엔드에서 보여줄 수 있게 되었습니다. ([관련 PR 링크](https://github.com/404-DriverNotFound/frontend-b/pull/40)) 이 부분에서 어떻게 처리해야 할지 파악하지 못해 팀원과 함께 많이 헤맸는데, 침착하게 찾아보니 해결법을 금방 찾을 수 있었습니다. ([제대로 구현하지 못했을 때의 임시 커밋](https://github.com/404-DriverNotFound/frontend-b/pull/40/commits/c6ccc5c080efe4ac6dc375d3c98eb62c1a66f5f1)) 이 부분에서 헤매게 된 가장 큰 원인은 HTTP에 대한 이해가 부족했던 것이라 판단했는데, 웹서버 만들기 프로젝트를 진행하며 파악한 것 이상으로 공부해서 이해도를 높여야겠다고 생각했습니다.

## HTML form

- Input 태그로 입력받은 이미지를 처리하고 이를 multipart/form-data로 전송할 수 있게 되었습니다. FormData 객체를 처음 사용해보았는데 정확한 이해 없이 사용했기 때문에 마찬가지로 회고, 휴식 기간을 이용해 공부해보려 합니다. ([관련 PR 링크](https://github.com/404-DriverNotFound/frontend-b/pull/30/commits/0d0acfc25e97f6efac846288588dd54c26963ff3))

# CORS 허용하기

CORS 이슈 해결에 대한 자세한 설명은 [이 포스팅](https://odajuwotda.tistory.com/102)에서 확인할 수 있다. `XMLHttpRequest`나 fetch API는 별도 옵션 없이 브라우저의 쿠키 정보나 인증 관련 헤더를 요청에 담아 보내지 않는데, 이것을 가능하게 해주는 옵션이 `credentials`이다.

credentials 옵션은 `same-origin`, `include`, `omit`의 세 가지 값을 가질 수 있는데 요청을 보낼때 `{ credentials: include }` 옵션을 사용하면 요청에 쿠키 정보, 인증 관련 헤더를 담아 보낼 수 있다. 단 이 경우 응답의 `Access-Control-Allow-Origin` 헤더가 `*` 대신 명시적인 URL으로 설정되어 있어야 하며, `Access-Control-Allow-Credentials: true` 헤더를 추가해야 한다.

이외에 Webpack Dev Server를 이용해 [리버스 프록싱을 하는 방법](https://evan-moon.github.io/2020/05/21/about-cors/#webpack-dev-server%EB%A1%9C-%EB%A6%AC%EB%B2%84%EC%8A%A4-%ED%94%84%EB%A1%9D%EC%8B%B1%ED%95%98%EA%B8%B0)도 존재하는데 Dev Server라는 이름을 보면 알 수 있듯 개발용 서버에서 백엔드를 건드리지 않고 CORS 문제 없이 요청-응답을 할 수 있게 해주는 트릭이라 보면 된다. ([추가 참고](https://react.vlpt.us/redux-middleware/09-cors-and-proxy.html)) 이 방법을 사용하면 간편하게 CORS를 우회할 수 있다는 것은 이해했지만 프록시, 리버스 프록시에 대한 이해가 부족해서 이 부분은 네트워크를 공부하면서 한 번 살펴봐야 할 것 같다.

# FormData 객체

- [FormData: MDN](https://developer.mozilla.org/ko/docs/Web/API/FormData)
- [[JavaScript]Form Data란?](https://2ham-s.tistory.com/307)

FormData 인터페이스는 form 필드와 그 값을 나타내는 일련의 key/value 쌍을 쉽게 생성할 수 있는 방법을 제공한다.

브라우저에서 파일을 전송할 때 `application/x-www-form-urlencoded`는 적합하지 않다. 파일 전송 시 enctype은 `multipart/form-data`로, text값으로 이루어진 영,숫자는 `application/x-www-form-urlencoded` type으로 보내는게 효율적이다.

# 다음에 공부할 것

우선순위 높은 것

- [socket.io](http://socket.io/) (필요할 때 마저 공부) ([참고](https://poiemaweb.com/nodejs-socketio))
    - [broadcasting](https://socket.io/docs/v4/broadcasting-events/)
    - [rooms](https://socket.io/docs/v4/rooms/)
- [HTML5 Canvas](https://velog.io/@mokyoungg/React-React%EC%97%90%EC%84%9C-Canvas-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0%EB%A7%88%EC%9A%B0%EC%8A%A4-%EA%B7%B8%EB%A6%AC%EA%B8%B0)

---

우선순위 낮은 것
- GraphQL
- Next.js
- [lazy loading](https://helloinyong.tistory.com/297)
- [V8 엔진](https://helloinyong.tistory.com/290?category=832499)
- node.js 환경과 브라우저 환경
- WAS
- 네트워크 전반(DNS, proxy, reverse proxy, …)

---

우선순위 낮고, 언급한 뒤 미뤄둔 것

- [React의 여러 가지 상태 관리 기술](https://hyo-choi.github.io/til/2021/07/27/today-I-learned/#%EC%98%A4%EB%8A%98-%EB%B0%9C%EA%B2%AC%ED%95%9C-%EA%B2%83) (Apollo의 로컬 캐시, Recoil, Redux)
