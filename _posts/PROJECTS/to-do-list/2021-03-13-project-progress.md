---
layout: post
title: "[to-do list] 3/13 진행상황"
categories: [PROJECTS, PROJECTS/to-do-list]
tags: [to-do-list]
comment: true
---

## [to-do list]

1. [계획](https://hyo-choi.github.io/projects/projects/to-do-list/2021/03/04/to-do-list-plan/)
2. [3/13 진행상황](https://hyo-choi.github.io/projects/projects/to-do-list/2021/03/13/project-progress/)

**github page:** [https://hyo-choi.github.io/to-do-list/](https://hyo-choi.github.io/to-do-list/)

<br>
  
## **그동안 구현한 내용**

### **전체적인 레이아웃 구성**

스마트폰 어플리케이션 같은 느낌을 컨셉으로 잡고 화면을 구성하였다.

- 새로 공부한 것
    - [[CSS] linear-gradient와 body 적용시 문제 해결](https://hyo-choi.github.io/study/study/css/2021/03/04/linear-gradient-and-body-problem/)
    - div의 수직, 수평 가운데 정렬법

### **modal window를 이용한 설정 창, to-do 추가 및 변경 창**

모달 창을 이용하여 설정 창, to-do 추가 창, to-do 변경 창을 구현하였다.

처음에는 각 모달 창마다 각기 다른 클래스명을 주었는데, 결과적으로는 같은 형태가 반복되므로 불필요한 클래스 분리라는 것을 깨달아 [하나의 클래스명과 스타일으로 통일](https://github.com/hyo-choi/to-do-list/commit/e79cc05df1286a13f4dcd64114e67cfc7cfff9d6#diff-0eb547304658805aad788d320f10bf1f292797b5e6d745a3bf617584da017051R49)하여 코드의 가독성과 일관성을 높였다.

js가 적용되는 요소에는 기존 선택자 외에 'js-'를 접두사로 가지는 선택자를 추가로 사용해줘야 한다는 것 자체는 알고 있었는데, 그 이유가 크게 와닿지 않았었다. 그런데 이번에 윗 문단과 같은 클래스(선택자) 정리를 하게 되더라도 javascript에는 영향이 가지 않아야 하므로 'js-'를 사용해주는 것이 백 번 옳다는 것을 체감하게 되었다.

- 새로 공부한 것
    - modal window 만드는 방법
- 해결하지 못한 것
    - div 내부에서만 modal background 적용하기
        - **목적**: 프로젝트의 레이아웃이 스마트폰과 유사한 형태이므로 모달창도 프레임(?) 내부에서만 보여주고 싶음
        - 대부분의 modal window가 화면 전체를 대상으로 하고 있어 적절한 예시를 찾지 못했다. html의 구조를 정확히 이해하지 못했기 때문에 실패한 것일 수도 있으니 좀 더 공부한 뒤에 이 부분을 다시 고려해보아야겠다.

### **to-do 추가 기능**

[Mymomontom](https://hyo-choi.github.io/myMomontom/)보다 디벨롭된 기능을 구현하기 위해 to-do의 description을 작성할 수 있는 기능을 추가하였다. 전부 표시하기에는 너무 긴 input일 때, 혹은 개행이 있는 description일 때는 일정 글자 수까지만 출력하고 뒷부분은 생략하도록 [처리](https://github.com/hyo-choi/to-do-list/commit/e79cc05df1286a13f4dcd64114e67cfc7cfff9d6#diff-b10ab244467aaef1ac50aac2514c746deb412517a55c515251e6fb9a01afafa3R35)해주었다.

각 to-do item을 html에 추가해줄 때는 일단 [이런 식](https://github.com/hyo-choi/to-do-list/commit/5005d1cb14e2bcffd923504b1ea8cdc01f53948c#diff-b10ab244467aaef1ac50aac2514c746deb412517a55c515251e6fb9a01afafa3R22)으로 모든 element를 직접 만들어 appendChild로 붙여주는 방법을 사용하기는 했는데, 코드의 가독성이 너무 떨어지고 구조를 이해하기 어렵지 않나 싶다. 따로 주석을 붙여놓는 정도가 최선인 것인지, 아니면 객체 등을 이용해서 구조 자체를 한 번에 만들 수 있는 것인지는 좀 더 알아봐야겠다.

- 새로 공부한 것
    - input과 textarea의 차이점
    - form이란? / form으로 감싸진 input과 아닌 input의 기능 차이

### **to-do 내용 변경 기능**

to-do를 변경할 때에는 해당 메뉴를 호출한 li와 일치하는 id를 가진 [to-do item을 찾아왔다](https://github.com/hyo-choi/to-do-list/commit/e79cc05df1286a13f4dcd64114e67cfc7cfff9d6#diff-857b16b468964e8bde45fbab444414ca7f07d39db9a0a31da3a9b2c90805785eR32).

이 과정에서 querySelector를 너무 빈번하게 사용하게 된다는 점이 마음에 걸렸는데, [이런 글](https://hashcode.co.kr/questions/5692/%EA%B0%95%EC%9D%98-4-11-queryselector%EC%97%90-%EC%84%B1%EB%8A%A5%EB%AC%B8%EC%A0%9C%EC%97%90-%EB%8C%80%ED%95%B4-%EC%A7%88%EB%AC%B8-%EB%93%9C%EB%A6%BD%EB%8B%88%EB%8B%A4)이나 [이런 글](https://gomakethings.com/javascript-selector-performance/)을 보니 getElementById(), getElementByClassName()과의 성능 차이를 비교할 뿐 querySelector(All) 자체에 성능 저하를 일으킬 만한 문제는 없다는 것 같아 안심했다. 

getElement…들이 더 빠르기는 하지만 querySelector도 1ms당 7000번의 연산을 수행할 수 있는 엄청나게 빠른 함수라고 하니 부담갖지 않아도 될 것 같다. 다만 같은 내용을 또 불러오고 불러오게 되는 반복은 피할 수 있도록 코드 구조를 잘 만들어줘야겠다.

- 새로 공부한 것
    - css animation, transition
    : 일단 깊은 이해 없이 필요한 부분만 적용했지만, 공식 문서를 읽으며 더 자세히 공부해야 할 것 같다.
- 해결하지 못한 것
    - div의 배경 색상이 오른쪽에서 왼쪽으로 채워지는 효과 주기
        - **목적**: 각 to-do item의 하위 메뉴를 클릭하였을 때 해당 효과가 적용되게 하기
        - div 전체가 슬라이드인 되는 효과에 대한 설명은 자주 찾아볼 수 있었는데, 해당 효과의 이름을 모르는 것이 문제인지 다른 방법으로 구현할 수 있는 부분이라 그런 건지 적당한 설명과 예시를 찾을 수 없었다.

<br>
  
## 앞으로 구현할 내용

지금까지 기본적인 레이아웃을 구현하였으니 이제 본격적으로 내부 기능을 구현할 차례다.

### 메인 페이지

- 진행 중 / 완료 탭 전환 기능
- **진행 중 탭**: 중요도, 마감기한에 따른 자동 정렬 기능
- **헤더 오른쪽 버튼**: 날씨와 미세먼지 수치 출력 ([API](https://data.kma.go.kr/data/rmt/rmtList.do?code=400&pgmNo=570) 이용)

### to-do 추가시

- 중요도 선택 기능 (미선택 시 보통 중요도로)
- 마감기한 설정 기능 (선택)

### to-do submenu

- to-do 변경시 중요도 / 마감 기한도 함께 변경하도록
- to-do 완료 기능
- to-do 삭제 기능

<br>
<br>
  
여기까지 진행하면서 느낀 점이 몇 가지 있다.

1. C나 C++ 코드는 자주 봐왔기 때문에 어느 정도 코드 보는 눈이 생겼는데, 웹 분야 코드는 많이 보지 못해서 코드를 어떻게 작성해야 좋은지 감이 잘 오지 않는다. 본보기가 될 만한 코드를 더 많이 봐야 할 것 같다.
2. 필요한 부분만 검색하면 나오니 그때그때 사용하기는 하는데, 이렇게 구현에만 치중하게 되니 수박 겉핥기 하는 기분이 많이 들었다. 생활코딩은 정말 입문을 도와주는 정도의 강의였으니 이제는 내가 알아서 기초를 탄탄히 쌓아야겠다.
3. 뭘 하긴 하는데 이렇다 할 피드백 과정이 없으니 '내가 제대로 하고 있는 게 맞나…….' 라는 생각이 굉장히 많이 든다. 프론트엔드 쪽 공부는 난생 처음이라서 더 그런 것 같다. 42에서 코드 리뷰를 많이 했던 것이 알게 모르게 C/C++ 공부에 엄청난 도움이 되고 있었구나 하는 생각도 들었다. 프로젝트 내용도 코드 리뷰를 받아볼 방법이 없을지 잘 궁리해봐야겠다.

공부한 내용에 대해서는 포스팅으로 정리하여 추가할 계획이다. 새로 공부한 것이 생길 때마다 정리를 바로 하면 좋겠는데 아직 글 쓰는 데 익숙하질 않아서 그런지 잘 손이 가지 않는다. 그래도 글을 쓰면 잘못된 정보를 기록으로 남기고 싶지 않다는 생각에 제대로 공부하게 되고, 그렇게 공부한 것이 이해도 잘 되고 기억에도 오래 남아 훨씬 좋은 것 같다. 어느 분야나 그렇겠지만 그냥 열심히 하는 것만이 답인 듯…….

결론: ***부지런히 깊게 공부하자!!***
