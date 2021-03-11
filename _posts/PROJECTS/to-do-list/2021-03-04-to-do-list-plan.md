---
layout: post
title: "[to-do list] 계획"
categories: [PROJECTS, PROJECTS/to-do-list]
tags: [to-do-list]
comment: true
---

생활코딩에서 HTML과 CSS를 맛보기로 공부한 뒤 노마드 코더의 [바닐라 JS로 크롬 앱 만들기](https://nomadcoders.co/javascript-for-beginners/lobby)를 완강했다. ([완성본 github repo](https://github.com/hyo-choi/myMomontom)) ([완성본 페이지 링크](https://hyo-choi.github.io/myMomontom/)) 날씨 기능은 현재 상태에서 굳이 넣을 필요를 느끼지 못해서 구현을 생략하였고, to-do list를 리셋하는 기능과 이름을 재설정하는 기능은 추가로 넣어본 부분이다.

이 과정에서 HTML, CSS, JS를 어떻게 사용해야 하는지에 대한 감 정도는 잡은 것 같아 제대로 공부해보기 위해 작게 사이드 프로젝트를 진행해보려 한다. 거창한 건 아니고 **좀 더 많은 기능을 수행할 수 있는 to-do list**를 만들어보는 것을 목표로 잡았다.

프로젝트의 대략적인 구상은 다음과 같다.

-   **목표: 나만의 to-do list 사이트 만들어보기** ([github repo](https://github.com/hyo-choi/to-do-list))
    
-   **구현할 기능**
    
    -   **to-do list**  
        1.  추가 시
            -   to-do 중요도 설정
            -   to-do 마감기한 설정
        2.  진행 중
            -   to-do 달성 현황에 따른 레벨업/다운 기능
            -   각 to-do의 중요도에 따라 정렬
            -   마감기한이 설정된 경우  
                : 마감 기한과 등록일의 차이를 기준으로 일정 % 이상 가까워지면 to-do의 색 변경
            -   to-do 변경 (모든 정보)
            -   to-do 삭제
        3.  완료
            -   '진행 중' 탭으로 복귀
            -   영구 삭제
    -   **정보 제공**
        -   현재 시간
        -   현재 위치 관련
            -   일기예보
            -   미세먼지 정보
    -   **관리 메뉴**
        -   앱 리셋 (이름, 레벨, to-do list 전부)
        -   유저 이름 변경

-   **유저로부터 입력받을 정보**
    
    -   닉네임
    -   to-do list
        -   내용 (필수)
        -   등록일 (자동 수집)
        -   마감기한 (선택)
        -   중요도 (미선택시 보통으로)
    -   위치 정보 (자동 수집 허용/비허용)

거창한 건 아니라고는 했는데 이것저것 쓰고 보니 굉장히 거창해 보인다……. 프로젝트를 진행하면서 가능한 한 구현해보고, 덜어내거나 추가할 기능이 있으면 이 글을 수정하는 대신 새 글을 작성하고 링크를 추가하는 식으로 진행하려 한다.

디자인은 일단 이런 느낌으로 작성해봤다. 진행하면서 바뀔 수도 있는데 당장은 이 정도로 해두고 기능에 집중해보려 한다.

<p class="codepen" style="height: 531px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-height="531" data-theme-id="light" data-default-tab="html,result" data-user="hyo-choi" data-slug-hash="bGBjVWg" data-pen-title="ToDoList"><span>See the Pen <a href="https://codepen.io/hyo-choi/pen/bGBjVWg"> ToDoList</a> by Hyojeong Choi (<a href="https://codepen.io/hyo-choi">@hyo-choi</a>) on <a href="https://codepen.io">CodePen</a>.</span></p>
<script src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
