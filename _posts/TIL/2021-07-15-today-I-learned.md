---
layout: post
title: "[TIL] 제너레이터, 브라우저 환경에서의 JS"
categories: [TIL]
tags: [TIL, JavaScript, CORS]
comment: true
---

# Today I Learned

2021/07/15

<br>

# 제너레이터

[이곳](https://ko.javascript.info/generators)을 참고하여 공부하였다.

## 제너레이터

- 모던 자바스크립트에서는 제너레이터를 잘 사용하지 않는다. 그러나 제너레이터는 웹 프로그래밍에서 비동기 데이터 스트림을 다루는 데 유용하므로 공부하고 넘어가는 것이 좋다.
- 제너레이터 사용 순서는 **제너레이터 함수 선언 → 제너레이터 함수를 실행시켜 제너레이터 객체 할당 → 제너레이터 객체를 통해 산출값 이용**의 단계로 이루어져 있다. 이 때 산출값을 이용하지 않으면 함수 본문 코드는 실행되지 않는다.
- .next() 메서드는 늘 `{value, done}`으로 구성된 객체를 반환한다. 이 때 value는 산출값이고 done은 함수 실행 종료 여부를 의미하는 boolean 값이다.
- 제너레이터는 이터러블이므로 **for...of 반복문을 이용해 값**을 얻을 수 있다. 단 마지막에 return한 value의 경우 for...of에서 무시되므로 for...of를 이용하여 값을 순회하려면 yield로 값을 반환해야 한다.
- 커스텀 이터러블 객체를 작성할 때 Symbol.iterator 대신 제너레이터 함수를 사용할 수도 있다.
    - 단 Symbol.iterator는 next가 구현된 일반 객체를 반환하기 보다는 **제너레이터를 반환하도록 구현하는 경우가 더 많다.**
- 제너레이터 안에 제너레이터를 포함하여 산출값을 조합할 수 있다. 이를 제너레이터 컴포지션generator composition이라 한다.
- generator.next()에 인자를 넘겨주면 해당 인자를 yield의 결과값으로 사용할 수 있다. 즉, **yield를 양방향 길처럼 사용**하여 제너레이터 안팎으로 정보를 교환할 수 있다.
- generator.throw의 인자로 에러 객체를 전달하면 현재 yield가 위치한 라인에서 throw를 호출한 것과 같은 효과를 줄 수 있다.

<br>

## async 이터레이터/제너레이터

- 커스텀 이터러블 객체에서 Symbol.iterator 대신 **Symbol.asyncIterator**를 사용하면 순회 시 async/await를 사용할 수 있다. Symbol.asyncgenerator에 `async *` 를 추가하면 일반 객체 대신 async 제너레이터를 반환하도록 구현할 수 있다.
- 제너레이터 본문에서 await를 사용하고 싶다면 **제너레이터를 async로 선언**하면 된다. (예: `async function* f...`) async 제너레이터를 순회할 때에는 **generator.next() 앞에 await**를 붙여주어야 한다. (예: `result = await generator.next()`)
    - async 제너레이터는 `{value, done}`을 감싼 **프라미스를 반환**한다.
- async 제너레이터는 페이지네이션 같은 구현에 적합하다. 어떻게 사용하면 좋을지는 [본문의 예제](https://ko.javascript.info/async-iterators-generators#ref-529)를 참고하면 파악할 수 있다. 브라우저 등의 몇몇 호스트 환경은 데이터 스트림을 처리할 수 있게 해주는 Streams API를 제공하기도 한다.

<br>

# 브라우저 환경에서의 JS

[이곳](https://ko.javascript.info/document)을 참고하여 공부하였다.

<br>

### [요소 사이즈와 스크롤](https://ko.javascript.info/size-and-scroll)

- 요소에 스크롤바가 생기면 몇몇 브라우저는 콘텐츠 영역 너비(content width) 일부를 빌려 스크롤바를 위치시킨다. **요소를 다룰 때에는 늘 스크롤바가 차지하는 공간을 염두에 두어야 한다.**
- 요소 내 텍스트가 길어지면 브라우저가 텍스트를 padding-bottom에 표시한다. 이는 정상 동작이다.
- **기하 프로퍼티**에는 다음과 같은 것들이 포함된다.
    - offsetTop, offsetLeft, offsetWidth, offsetHeight
        - offsetParent: 해당 요소를 렌더링할 때 좌표 계산에 사용되는, 가장 가까운 조상 요소의 reference
        - offsetTop과 offsetLeft는 offsetParent를 기준으로 각각 요소가 오른쪽으로, 아래쪽으로 얼마나 떨어져 있는지를 나타낸다. 해당 요소의 border 왼쪽 위 모서리를 기준으로 한다.
        - offsetWidth, offsetHeight는 요소의 padding과 margin을 포함한 너비, 높이를 나타낸다.
    - clientTop, clientLeft, clientWidth, clientHeight
        - clientTop, clientLeft는 요소의 border 두께를 의미한다. 그러나 만약 요소의 스크롤바가 왼쪽에 나타나는 상황(아랍어, 히브리어로 세팅된 브라우저 등)에서는 clientLeft의 값이 스크롤바의 너비까지 포함한다.
        - clientWidth, clientHeight는 border 안 영역의 사이즈 정보이다. border 안에 포함되는 것(content width/height, padding, 스크롤바) 중 콘텐츠 너비/높이와 패딩의 너비/높이만을 합쳐 계산한다.
    - scrollTop, scrollLeft, scrollWidth, scrollHeight
        - scrollTop, scrollLeft는 가로-세로 스크롤이 움직임에 따라 가려진 영역의 너비와 높이를 나타낸다. **이 두 프로퍼티를 수정하여 요소의 스크롤 상태를 변경할 수 있다.**
        - scrollWidth, scrollHeight는 스크롤바에 감춰진 공간까지 포함한 client 너비와 높이(즉, 패딩 포함)를 나타낸다.
- `elem.scrollIntoView(top)`을 호출하면 전체 페이지의 스크롤이 움직여 elem이 보이는 상태로 변경된다. top이 true(디폴트)인 경우 elem이 창 가장 위에 위치하도록 스크롤이 조정되고, false인 경우 elem이 창 가장 아래에 위치하도록 스크롤이 조정된다.
- 화면에 표시되지 않는 요소의 기하 프로퍼티 값은 0 또는 null이다. 또, ``<body>``와 ``<html>``, position이 fixed인 요소는 offsetParent가 null일 수 있다.
- 기하 관련 프로퍼티는 HTML 문서에 `<!DOCTYPE HTML>`이 명시되어있지 않으면 이상하게 동작할 때가 있다. 따라서 **HTML에는 항상 DOCTYPE을 명시**해야 한다.

<br>

### [브라우저 창 사이즈와 스크롤](https://ko.javascript.info/size-and-scroll-window)

- 브라우저 창의 너비와 높이를 얻어내는 방법은 두 가지가 있다.
    1. document.documentElement.clientWidth/clientHeight
    2. window.innerWidth/innerHeight
    - 전자의 경우 **스크롤바 공간을 제외**한 너비/높이를 반환하고 후자의 경우 **스크롤바 공간을 포함**한 너비/높이를 반환하는데, 용도에 따라 구분하여 사용하면 된다.
- 문서 전체의 높이를 얻고 싶다면 다음과 같이 여섯 프로퍼티의 반환값 중 최댓값을 골라야 한다.

    ```jsx
    let scrollHeight = Math.max(
      document.body.scrollHeight, document.documentElement.scrollHeight,
      document.body.offsetHeight, document.documentElement.offsetHeight,
      document.body.clientHeight, document.documentElement.clientHeight
    );
    ```

- **문서의 스크롤 상태**를 얻기 위해서는 document.documentElement의 scrollTop, scrollLeft를 사용해야 하는데, 구버전 Webkit을 기반으로 하는 브라우저에서는 버그로 인해 document.body를 사용해야 원하는 값을 얻을 수 있다.
이를 위해 브라우저별 예외처리를 하기보다는 **window.pageXOffset/pageYoffset을 사용**하면 브라우저와 관계 없이 문서 전체의 스크롤 정보를 알 수 있다. 이 두 프로퍼티는 읽기 전용이다.
- 문서의 스크롤 상태를 변경하는 방법은 두 가지가 있다. 이 두 메서드는 브라우저 종류에 상관없이 동일하게 동작한다.
    1. window.scrollBy(x, y)

        페이지 스크롤 상태를 **현재 포지션 기준**으로 **상대적으로 조정**한다.

    2. window.scrollTo(pageX, pageY)

        **절대 좌표를 기준**으로 페이지 스크롤 상태를 변경한다.

- **스크롤바를 고정하려면** `document.body.style.overflow` 속성을 hidden으로 수정하면 된다. 단, 이 경우 스크롤바가 사라져 content width가 변경되고 콘텐츠가 움직이는 현상을 보일 수 있는데, 유저 입장에서 어색한 동작일 수 있으므로 이런 경우 적절하게 padding을 주어 **content width를 유지해주는 것이 좋다.**

<br>

### [좌표](https://ko.javascript.info/coordinates)

- 대부분의 JS 메서드는 두 좌표 체계 중 하나를 사용하는데, window의 왼쪽 위 모서리를 기준으로 좌표를 계산하는 clientX, clientY와 문서 전체의 왼쪽 위 모서리를 기준으로 좌표를 계산하는 pageX, pageY가 있다. 창 기준 좌표는 position: fixed와 사용하면 좋고 문서 기준 좌표는 position: absolute와 사용하면 좋다.
- `elem.getBoundingClientRect()`를 이용하면 elem을 감싸는 가장 작은 사각형의 창 기준 좌표를 얻을 수 있다. (반환값은 DOMRect 클래스의 객체)
- `document.elementFromPoint(x, y)`를 이용하면 창 기준 좌표 (x, y)에서 가장 가까운 중첩 요소를 얻을 수 있다.

<br>

### 이벤트

- 오직 버튼 역할만을 하는 버튼은 button 태그로 만들고, **다른 페이지로 이동하기 위한 버튼은 a 태그를 이용해 만드는 것이 좋다.** 사용자는 오른쪽 마우스 클릭-새 창에서 링크 열기와 같은 기능을 기대하기 때문이다. button만으로는 해당 동작을 볼 수 없다.

<br>

- 거의 모든 이벤트가 버블링되지만 focus와 blur는 버블링되지 않는다.
- `event.stopPropagation()`의 경우 상위로 향하는 버블링은 막을 수 있으나 같은 레벨에 등록된 다른 핸들러의 동작은 막을 수 없다. 버블링을 멈추고 다른 모든 핸들러의 동작을 막고 싶다면`event.stopImmediatePropagation()`을 이용해야 한다.
- **꼭 필요한 경우를 제외하면 버블링을 막지 말아야 한다.** 이벤트 버블링을 막아야 할 만한 경우는 거의 없다. 버블링을 막은 부분의 이벤트는 문서 레벨으로 전파되지 않기 때문에 분석 시스템 등에 캐치되지 않는 dead zone이 되어버린다.
- 문서 레벨에 등록한 이벤트와 요소에 등록한 이벤트가 중복되는데 둘 중 한 가지 이벤트만 확인하고 싶다면 `event.stopPropagation()` 대신 event.defaultPrevented가 true인 경우 바로 return해주는 방식을 쓰는 것이 좋다.

<br>

- 모든 사용자가 운영체제 종류에 상관없이 동일한 경험을 하게 하려면 `if (event.ctrlKey || event.metaKey)` 처럼 Ctrl과 Cmd를 모두 지원해야 한다.
- 키보드를 사용하지 않는 환경(모바일 등)에서의 UX를 해치지 않으려면 보조 키가 없는 사용자에 대한 지원도 고려해야 한다.

<br>

- form은 document.forms의 구성원이다. form의 이름이나 인덱스를 통해 폼에 접근할 수 있다.
- form.elements[name](혹은 name을 dot notation으로 조회)을 이용하면 form의 element를 얻을 수 있고, form의 element에서 element.form을 이용하면 역으로 폼에 접근할 수 있다.

<br>

- DOMContentLoaded 이벤트는 브라우저가 HTML을 전부 읽고 DOM 트리를 완성했을 때(``<img>``나 스타일시트 등 기타 자원은 기다리지 않음), load 이벤트는 외부 자원까지 모두 불러왔을 때 발생한다.
- beforeunload/unload 이벤트는 사용자가 페이지를 떠날 때 발생한다. unload는 딜레이가 없는 작업을 수행할 수 있다.
    - unload 이벤트 발생 시 navigator.sendBeacon() 메서드를 사용하면 백그라운드에서 서버에 데이터를 전송할 수 있다.
    - onbeforeunload 핸들러 사용 시 return에 문자열을 넘겨주는 방식은 구식 브라우저에서만 동작하고 근래의 명세서에서는 권장하지 않으므로 사용하지 않도록 한다.
- document.readyState를 이용하면 loading/interative/complete의 상태를 확인할 수 있다.
    - readystatechange 이벤트를 사용하면 상태가 변경될 때 원하는 작업을 할 수 있다. 다만 이 이벤트는 요즘에는 잘 사용되지 않는다.

<br>

### [SOP](https://evan-moon.github.io/2020/05/21/about-cors/#sopsame-origin-policy)와 [CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

- CORS는 브라우저의 구현 스펙에 포함되어 있기 때문에 브라우저를 통하지 않은 서버 간 통신에서는 이 정책이 적용되지 않는다. 서버가 CORS를 위반한 요청에 정상적으로 응답한 경우 이 응답의 파기 여부는 브라우저에서 결정된다.
- Scheme, Host, Port가 동일한 URL을 가지고 있다면 same-origin으로 인정한다.
- 요청과 응답이 이루어지는 과정과 그 내용은 [이쪽](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS#%EB%8B%A8%EC%88%9C_%EC%9A%94%EC%B2%ADsimple_requests)에서 자세히 확인할 수 있다.
- HTML에 포함하는 ``<script>``의 src를 다른 origin에서 가져오려면 `crossorigin` attribute에 값을 설정해주어야 한다.
    - crossorigin이 없는 경우: 리소스에 접근할 수 없다.
    - `crossorigin="anonymous"`인 경우: 리소스를 가진 서버(원격 서버)가 Access-Control-Allow-Origin: * 헤더를 포함하여 응답할 경우 리소스에 접근할 수 있다. 이 때 브라우저는 원격 서버에 인증 정보와 쿠키를 전송하지 않는다.
    - `crossorigin="use-credentials"`인 경우: 원격 서버가 Access-Control-Allow-Origin 헤더에 내 origin을 담아 보내고, Access-Control-Allow-Credentials: true인 경우 리소스에 접근할 수 있다. 이 때는 브라우저가 원격 서버에 인증 정보와 쿠키를 전송한다.
