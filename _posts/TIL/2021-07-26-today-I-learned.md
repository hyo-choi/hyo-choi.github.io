# Today I Learned

2021/07/26

<br>

# [React 코드 스플리팅](https://velog.io/@velopert/react-code-splitting)

웹팩은 빌드 시 모든 자바스크립트 코드를 **하나의 파일으로 번들링**하는데, 이 파일에는 한 페이지에서 사용되는 코드만 포함되어 있는 것이 아니라 프로젝트 내의 모든 코드가 포함되어 있다. 따라서 **페이지를 처음 로딩할 때 프로젝트에 사용되는 모든 코드를 로드하게 되어 첫 로딩이 오래 걸리게 된다.**

이러한 **성능 문제를 해소하기 위해 제시된 것이 코드 스플리팅Code splitting**이다. 코드 스플리팅을 사용하면 전체에서 당장 필요하지 않은 코드를 분리시키고, 필요할 때만 불러 사용할 수 있다. 나는 리액트 앱에서 코드 스플리팅을 사용하겠지만, 웹팩으로 프로젝트라면 코드 스플리팅을 사용할 수 있다.

코드 스플리팅은 주로 [세 가지 경우](https://medium.com/humanscape-tech/react%EC%97%90%EC%84%9C-%ED%95%B4%EB%B3%B4%EB%8A%94-%EC%BD%94%EB%93%9C-%EC%8A%A4%ED%94%8C%EB%A6%AC%ED%8C%85-code-splitting-56c9c7a1baa4)에 사용한다: **Route 레벨**(각 react-router마다 적용), **컴포넌트 레벨**(모달 창처럼 페이지 내부의 요소지만 처음에는 보이지 않는 컴포넌트에 적용), **페이지 내부에서 스플리팅**(하나의 페이지가 굉장히 긴 경우 보이는 부분만 렌더링하고 나머지 부분에 적용). 이 중에서 [스플리팅할 부분을 찾기 가장 쉬운 부분이 Route 레벨](https://ko.reactjs.org/docs/code-splitting.html#route-based-code-splitting)이라고 한다.

코드 스플리팅이 React-router와 묶여서 설명되는 것을 종종 봤는데 이런 특성 때문이었던 것 같다. 지금 진행중인 프로젝트는 소규모라 로딩이 그리 느릴 것 같지는 않지만, Route 레벨 코드 스플리팅을 한 번 적용해보면 좋은 경험이 될 것 같으니 도입을 검토해 봐야겠다.

## dynamic import

코드 스플리팅에는 **dynamic import 문법**을 사용한다. `import`를 함수로 사용하면 Promise를 반환하는데, `import('filepath').then(...);` 형식으로 사용할 수 있다. 단 모듈을 비동기적으로 [CommonJS](https://d2.naver.com/helloworld/12864) 형태로 불러오기 때문에 어떤 방식으로 export 했는지에 따라 맞춰서 사용해야 한다.

import를 함수로 사용하면 웹팩이 알아서 코드를 분리해서 따로 저장해주고, import가 호출될 때 불러와서 사용할 수 있게 해준다. 이렇게 분리된 파일을 **청크 파일**이라고 부른다.

## [React.lazy](https://ko.reactjs.org/docs/code-splitting.html#reactlazy)

`React.lazy` 함수를 사용하면 dynamic import를 사용해서 컴포넌트를 렌더링할 수 있다. `import OtherComponent from './OtherComponent';` 처럼 단순 import하는 대신 `const OtherComponent = React.lazy(() => import('./OtherComponent'));` 처럼 사용해주면 된다. 이 때 **React.lazy의 인자로는 동적 import를 호출하는 함수를 전달**한다. 또, React.lazy로 import하려는 **컴포넌트는 [default로 export](https://ko.reactjs.org/docs/code-splitting.html#named-exports)**되어있어야 한다.

lazy 컴포넌트는 **Suspense 컴포넌트 하위에서 렌더링**되어야 하며, Suspense 컴포넌트의 `fallback` props를 이용하면 컴포넌트가 로드되는 동안 로딩 화면과 같은 예비 컨텐츠를 보여줄 수 있다. 하나의 Suspense 컴포넌트로 여러 개의 lazy 컴포넌트를 감쌀 수도 있다.

React.lazy는 서버 사이드 렌더링을 지원하지 않아 [다른 라이브러리](https://loadable-components.com/docs/server-side-rendering/)를 사용해야 한다고 하는데 나는 아직 서버 사이드 렌더링을 고려하고 있지 않으므로 이 부분은 나중에 다시 생각해봐야 할 것 같다.

## [Error boundaries](https://ko.reactjs.org/docs/error-boundaries.html)

네트워크 장애 등으로 모듈 로드가 아예 실패할 수도 있는데, **Error boundary**를 이용해 lazy 컴포넌트 및 Suspense 컴포넌트를 감싸주면 **에러 상황을 관리**할 수 있다. 단, [Error boundary는 함수형 컴포넌트로 사용할 수 없기 때문에](https://stackoverflow.com/questions/48482619/how-can-i-make-use-of-error-boundaries-in-functional-react-components) 다른 컴포넌트는 함수형으로 작성하더라도 Error boundary만은 클래스형 컴포넌트로 작성해주어야 한다. Error boundary 사용을 간편하게 할 수 있도록 해주는 [패키지](https://www.npmjs.com/package/react-error-boundary)도 있으니 명세대로 사용하는 것이 어렵다면 한 번 시도해봐도 좋겠다.


<br>

# [Socket.io](https://socket.io/)

[HTML5 프로토콜인 웹소켓](https://hyo-choi.github.io/til/2021/07/16/today-I-learned/#%EC%9B%B9%EC%86%8C%EC%BC%93)은 HTTP 통신과는 다르게 클라이언트가 특정 주기를 가지고 Polling하지 않아도 변경된 사항을 시기적절하게 전달할 수 있는 지속적이고 완전한 양방향 연결 스트림을 만들어 주는 기술이다. 이러한 특성으로 인해 WebSocket은 서버의 데이터를 클라이언트에 즉시 전달할 수 있는 실시간 애플리케이션 작성에 매우 효과적이다. ([출처](https://poiemaweb.com/nodejs-socketio#1-websocket))

그러나 웹소켓은 오래된 브라우저에서는 지원되지 않는 경우가 있다. 따라서 브라우저 간 호환이나 이전 버전 호환을 고려하면 **Cross-platform 웹소켓 API인 Socket.io**를 사용하는 것이 좋다.

## [Socket.io는 무엇인가](https://socket.io/docs/v4#What-Socket-IO-is)

Socket.io는 **클라이언트-서버 간 실시간, 양방향, 이벤트 기반 통신을 가능하게 하는 라이브러리**이다.

Socket.io는 **Node.js 서버와 통합할 수 있는 [socket.io](https://github.com/socketio/socket.io)**, 브라우저에서 불러올 수 있는 **JS 클라이언트 라이브러리 [socket.io-client](https://github.com/socketio/socket.io-client)**로 [나뉜다](https://socket.io/get-started/chat#Integrating-Socket-IO). [다른 프로그래밍 언어를 위한 클라이언트 구현체](https://socket.io/docs/v4/)도 커뮤니티에 의해 만들어져 유지되고 있다.

Socket.io는 클라이언트가 웹소켓을 지원하면 웹소켓에 연결하고, 아닐 경우 [HTTP long polling](https://ko.javascript.info/long-polling) 방식을 이용한다. Socket.io를 **웹소켓 API의 wrapper**처럼 여겨도 괜찮다. 본 항목 제목에 링크된 문서에 웹소켓 대신 Socket.io를 이용하는 간단한 예시가 제시되어 있다. (서버 사이드, 클라이언트 사이드 모두)

Socket.io는 웹소켓이 지원하는기능 외에도 [다음과 같은 기능들](https://socket.io/docs/v4#Features)을 지원한다: 신뢰성(웹소켓을 사용할 수 없는 환경에서의 long polling 사용), 자동 재연결, 패킷 버퍼링, Acknowledgements(전통적인 요청-응답 API), 일부/전체 클라이언트에게 broadcasting, 멀티플렉싱(=namespace).

## [Socket.io는 무엇이 아닌가](https://socket.io/docs/v4#What-Socket-IO-is-not)

**[Socket.io](http://socket.io)는 웹소켓 구현체가 아니다**. Socket.io는 가능한 상황에서는 웹소켓을 사용하기는 하지만, 각 패킷에 부가적인 메타데이터를 덧붙인다. 웹소켓 클라이언트가 Socket.io 서버에 연결할 수 없고 Socket.io 클라이언트가 웹소켓 서버에 연결할 수 없는 원인이 이것이다.

## [Socket.io는 어떻게 동작하는가](https://socket.io/docs/v4/how-it-works/)

Socket.io는 Engine.io라고 하는 low-level plumbing과 high-level API인 Socket.io(그 자체)의 두 가지 레이어로 나뉜다. 

### [Engine.io](https://socket.io/docs/v4/how-it-works/#Engine-IO)

Engine.io는 서버와 클라이언트 사이의 하위 레벨 연결을 성립시키는 역할을 맡고 있다. 

클라이언트는 **기본적으로 HTTP long polling 방식으로 연결을 성립**시킨다. 그 이유는 다음과 같다.

- 웹소켓이 양방향 통신에 있어서 명확하게 가장 좋은 방법이기는 하지만, 기업용 프록시/개인 방화벽/안티바이러스 소프트웨어 등으로 인해 웹소켓 연결을 성립시킬 수 없는 경우가 있다.
- 유저 관점에서 생각했을 때, 웹소켓 연결에 실패하면 **실시간** 어플리케이션이 데이터를 교환하기까지 **최소 10초 이상을 대기**하여야 하기 때문에 웹소켓 연결 실패는 UX를 크게 해칠 수 있다. Engine.io는 신뢰성과 UX를 첫 번째 관심사로, 이외의 잠재적 UX 개선과 서버 퍼포먼스 향상을 두 번째 관심사로 둔다.

따라서 Engine.io는 우선 HTTP long polling으로 연결한 뒤 **업그레이드를 시도**한다. 업그레이드 시도는 다음과 같이 일어난다. (1) handshake를 마치고 (2) HTTP long polling으로 데이터를 전송한 뒤 (3) 데이터를 받고 (4) 웹소켓으로 **업그레이드**한 뒤 (5) HTTP long polling으로 데이터를 받는다. 이 때 (4)에서 웹소켓 연결이 성공적으로 이루어지면 HTTP long polling 통신은 종료된다. (즉 5번 단계가 발생하지 않는다.)

**업그레이드 단계**는 다음과 같다. (1) 발신할 버퍼가 비어있는지 확인하고 (2) 현재 방식을 읽기 전용으로 바꾸고 (3) 다른 방식을 사용한 연결을 시도하고 (4) 성공하면 첫 번째 방식을 이용한 통신을 종료한다.

Engine.io 연결은 다음 경우에 **종료되었다고 간주**된다: HTTP 요청(GET이나 POST)이 실패했을 때(서버가 셧다운 된 경우 등), 웹소켓 연결이 종료되었을 때(유저가 브라우저에서 탭을 닫는 등), 서버 사이드/클라이언트 사이드에서 `socket.disconnect()`를 호출했을 때.

## [Socket.io 클라이언트 설치](https://socket.io/docs/v4/client-installation/#Installation)

나는 프론트엔드를 맡았으므로 Socket.io의 클라이언트 측을 담당하게 될 것이다. 이 경우에 어떻게 설치하면 좋을지 간단하게 알아보겠다.

Socket.io 서버가 기본적으로 클라이언트용 번들을 가지고 있기 때문에 서버와 같은 환경에서 작업한다면 가져다 쓰는 것도 나쁘지 않을 것 같지만, 우리 프로젝트의 경우 백엔드와 프론트엔드가 별도의 레포지토리에서 작업하고 있으므로 이 방법은 고려하지 않는 게 좋을 듯하다. 따라서 서버 사이드에서 `serverClient` 옵션을 `false`로 설정해주어야 할 것 같다.

가장 무난하게 쓸 수 있는 방법은 역시 **npm을 통해 설치**하는 것이다. `npm install socket.io-client` 명령어를 사용하면 socket.io 클라이언트를 설치할 수 있다.

예전에는 TypeScript용 type이 socket.io-client 내부에 정의되어있지 않아 `@types/socket.io-client`를 별도로 설치해주어야 했던 것 같지만 지금은 type이 패키지 내부에 포함되었기 때문에 **설치하지 말아야 한다.** 설치하면 충돌로 인해 문제가 발생할 수 있다고 한다.

## [Socket.io 클라이언트 설정](https://socket.io/docs/v4/client-initialization/)

socket을 만들 때, origin(scheme, host, port)이 동일한 서버와 통신할 경우 `const socket = io();` 로 사용하고, origin이 다른 서버와 통신할 경우 `const socket = io(URL);` 으로 사용해야 한다. 이 때 서버의 CORS를 활성화해야 한다. URL으로 namespace를 관리할 수도 있다.

## 자주 쓰이는 메소드

팀원들과 Socket.io가 프로젝트에서 당장 쓰일 부분은 아닌 것 같다는 이야기를 나눴다. 확실히 그런 것 같아서 일단 가장 자주 쓰이는 메서드들만 가볍게 살펴본 뒤 자세한 내용은 Socket.io를 사용하게 될 즈음에 다시 공부해보기로 했다. 작성된 것들 외의 메소드는 [Client method API 문서](https://socket.io/docs/v4/client-api/)에서 직접 확인할 수 있다.

### [io([url][, options])](https://socket.io/docs/v3/client-api/index.html#io-url-options)

주어진 URL으로 **새로운 Manager를 만들고, Socket 인스턴스를 반환**한다.

### [Manager](https://socket.io/docs/v3/client-api/index.html#Manager)

`Manager`는 Engine.io 클라이언트 인스턴스를 관리한다. Reconnection 로직을 다루며, 하나의 Manager가 여러 Socket을 관리할 수 있다.

### [Socket](https://socket.io/docs/v3/client-api/index.html#Socket)

`Socket`은 서버와 상호작용 하기 위해 필요한 클래스다. 특정한 Namespace(기본 /)에 종속되며 통신을 위해 underlying Manager를 사용한다. Socket은 기본적으로 네트워크를 통해 서버에 이벤트를 보내고/서버로부터 이벤트를 받는 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)다. socket.id, socket.connected, socket.disconnected 프로퍼티를 가지고 있다.

- **여기서 잠깐! EventEmitter란?**

    브라우저 API에서 `new Event()` 를 통해 커스텀 이벤트를 작성하는 것처럼, Node.js에서 [커스텀 이벤트를 다루기 위해 사용되는 것](https://dev.to/alexmercedcoder/javscript-events-in-the-browser-and-node-3978)이 EventEmitter다. ([간단한 사용 예시](https://www.w3schools.com/nodejs/nodejs_events.asp)) EventEmitter는 `.on` 메소드를 통해 이벤트 타입과 이벤트 핸들러를 받아 등록하고, `.emit` 메소드를 통해 이벤트를 실행시킨다. ([참고](https://www.huskyhoochu.com/nodejs-eventemitter/)) ([다른 메소드 설명](https://edu.goorm.io/learn/lecture/557/%ED%95%9C-%EB%88%88%EC%97%90-%EB%81%9D%EB%82%B4%EB%8A%94-node-js/lesson/174362/event-%EB%AA%A8%EB%93%88)) EventEmitter 객체로 이벤트를 호출할 때, 해당 이벤트에 붙어 있는 모든 함수는 동기적으로 호출된다. 호출을 받은 리스너가 반환하는 결과는 어떤 값이든 무시되고 폐기된다. ([출처](https://edykim.com/ko/post/events-eventemitter-translation-in-node.js/))

- **socket.connect()**

    소켓을 수동으로 연결한다. io 호출 시 autoConnect 옵션을 false로 설정한 경우나 disconnect 이벤트 발생 시 등에 사용할 수 있다. `socket.open()`과 같은 동작을 한다.

- **socket.disconnect()**

    소켓 연결을 수동으로 끊는다. 이 경우 소켓은 재연결을 시도하지 않는다. 이렇게 끊은 소켓이 Manager에 등록된 마지막 활성 소켓이었다면 low-level 연결 또한 끊긴다. `socket.close()`와 같은 동작을 한다.

- **socket.send([…args][, ack])**

    `message` 이벤트를 보낸다.

- **socket.on(eventName, callback)**

    eventName 이벤트와 이벤트 핸들러를 등록한다.

- **socket.emit(eventName[, …args][, ack])**

    socket에 eventName 이벤트를 발생시키고 `true`를 반환한다. [이쪽](https://socket.io/docs/v4/emit-cheatsheet/)에서 emit과 관련된 공식 cheatsheet를 확인할 수 있다.

- **socket.onAny(callback)**

    어떤 이벤트가 발생해도 실행되는 이벤트 핸들러를 등록한다.

<br>

# UI와 UX

## [생소한 UI 용어 정리](https://story.pxd.co.kr/616)

### 리스트 박스 vs 드롭다운 리스트 vs 콤보 박스

**리스트 박스**는 드롭다운 하지 않는 형태의 펼쳐진 리스트이다.

**드롭다운 리스트**는 화살표를 눌러 숨어있던 항목을 선택할 수 있는 리스트이다.

**콤보 박스**는 드롭다운 리스트+입력 필드의 결합(⇒ Combo)으로 사용자가 값을 직접 입력하거나 나열된 항목 중 하나를 선택할 수 있는 입력 요소이다.

### 버트콘, 툴바, 툴팁

**버트콘Butcon**은 버튼+아이콘의 조합으로 버튼 기능이 있는 아이콘이다.

**툴바**는 버트콘을 사용자의 기호와 필요에 따라 바 형태로 모아둔 것이다.

**툴팁**은 어떤 UI 요소에 마우스 오버 시 말풍선 형태로 제공되는 설명이다.

### [다양한 메뉴 양식](https://story.pxd.co.kr/638)

드롭다운 메뉴, 리본 메뉴, 컨텍스트 메뉴, MRU 리스트, 어댑티브 메뉴, 테어오프 메뉴, 메가 메뉴, 컨텍스트-센서티브 내비게이션 등이 있다. (위 제목 링크 참고)

## 프론트엔드 개발과 UI, UX

갑자기 프론트엔드 개발자는 어디부터 어디까지 커버되어야 하는 걸까 하는 생각이 들어 이것저것 검색해보았다. 아래 글들을 읽으며 다양한 의견을 확인해보았다.

- [프론트엔드 개발자에게 UX는? 연기에 몰입해서 나오는 배우들의 애드립](https://feel5ny.github.io/2021/01/10/UX%EC%99%80%20%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EA%B0%9C%EB%B0%9C%EC%9E%90/)
- [프론트엔드 개발, 뭐하는 직종이야?](https://seunghyun90.tistory.com/77)
- [[웹 퍼블리셔 성장기 1편] 웹 퍼블리셔, 뭐하는 직종이야?](https://seunghyun90.tistory.com/25)
- [프론트엔드의 본질은 UX가 아닐까](https://jbee.io/essay/about_frontend/)
- [UX가 궁금한 개발자에게 보내는 편지](https://story.pxd.co.kr/1446)
- [UX 디자이너는 뭐하는 사람일까](https://brunch.co.kr/@sooscape/11)
- [구글, 애플에 있는 UX 엔지니어가 토스에 있는 이유](https://blog.toss.im/article/ux-engineer-interview)
- [개발자가 UX를 향상 시키는 방법](https://velog.io/@ansrjsdn/%EA%B0%9C%EB%B0%9C%EC%9E%90%EA%B0%80-UX%EB%A5%BC-%ED%96%A5%EC%83%81-%EC%8B%9C%ED%82%A4%EB%8A%94-%EB%B0%A9%EB%B2%95)
- [프론트엔드 개발자도 디자인 감각이 중요할까요?](https://okky.kr/article/812850)
- [프론트엔드개발자를 위한 UX 책 이야기](https://wit.nts-corp.com/2014/01/16/686)
- [내가 UX디자이너와 협업 하는 방법](https://medium.com/@jimkimau/%EB%82%B4%EA%B0%80-ux%EB%94%94%EC%9E%90%EC%9D%B4%EB%84%88%EC%99%80-%ED%98%91%EC%97%85-%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-47535952872b)

프론트엔드 개발자가 디자인을 하지는 않고 회사 혹은 팀에 웹 퍼블리셔가 있다면 CSS조차 건드리지 않아도 될 수 있지만, 여전히 UX에 대해서는 기민하게 생각하고 많은 고민과 공부를 해야 할 것 같다. 그리고 이건 어느 글에서나 강조하는 부분인데, **디자이너의 영역과 개발자의 영역은 다르기 때문에 각자의 영역과 작업물을 존중할 것!** 다른 건 잊더라도(물론 안 잊어야겠지만…….) 이것만은 잘 새겨두어야겠다.

<br>

# 느낀 점

## Node.js와 브라우저 환경에 대해 공부하자

[Socket.io](http://socket.io) 파트에서 Socket이 EventEmitter라는 한 줄짜리 설명을 보고 살짝 당황했다. 나는 생전 처음 보는 용어인데 EventEmitter를 당연히 알고 있다는 전제 하에 설명하고 있었기 때문이다. 급하게 찾아보면서 이것이 Node.js 환경에서 사용하는 클래스임을 알게 되었다. 커스텀 이벤트 자체를 사용해본 적 없었고, 브라우저 환경의 이벤트 처리가 브라우저 API에 종속되어 있었다는 것도 제대로 파악하지 못하고 있었고, Node.js 환경에서 이벤트가 다르게 처리된다는 것도 모르고 있었기 때문에 생소할 수밖에 없었던 것이다. 이 부분을 보니 브라우저와 Node.js 환경이 어떻게 다른지, 또 브라우저 API에 속한 것은 무엇이고 JS에 속한 것은 무엇인지 제대로 공부해봐야겠다는 생각이 들었다.

## 서버 사이드 렌더링의 개념 정도는 알자

코드 스플리팅 공부를 하면서 서버 사이드 렌더링과 연관된 내용을 많이 보았다. 우리 프로젝트에 SSR을 도입할 생각이 아직 없기도 하고 지금 배우는 내용만으로도 머리가 복잡해서 잠시 미뤄두었는데, 이만하면 어느 정도 정리가 된 것 같기도 해서 자세히는 아니더라도 어느 정도는 개념을 알고 넘어가는게 맞겠다 싶다. 내일은 SSR에 대해 조금이라도 공부해봐야겠다.

<br>

# 다음에 공부할 것

- toastify
- HTML5 Canvas
- 서버 사이드 렌더링, Next.js, lazy loading

---

- [socket.io](http://socket.io) (필요할 때 마저 공부) ([참고](https://poiemaweb.com/nodejs-socketio))
    - [broadcasting](https://socket.io/docs/v4/broadcasting-events/)
    - [rooms](https://socket.io/docs/v4/rooms/)
- V8 엔진
- node.js 환경과 브라우저 환경
