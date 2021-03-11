---
layout: post
title: "[CSS] linear-gradient와 body 적용시 문제 해결"
categories: [STUDY, STUDY/CSS]
tags: [CSS]
comment: true
---

사이드 프로젝트를 진행하려는데 배경에 이미지를 넣기는 싫고, 그렇다고 단색을 깔면 너무 밋밋해서 배경에 그라데이션을 넣을 수 있는 방법을 찾아보았다. 그런데 body에 linear-gradient를 적용하니 의도한대로 적용되지 않았다. 아래에 linear-gradient에 대한 간단한 소개와 body 적용시의 문제 해결법을 작성해보았다.

## CSS로 배경에 그라데이션 넣기

### linear-gradient

기초  
[https://developer.mozilla.org/en-US/docs/Web/CSS/linear-gradient()](https://developer.mozilla.org/en-US/docs/Web/CSS/linear-gradient())
    
-   기본 사용법
    
    ``` css
    background: linear-gradient((opt)direction, color1, color2, ...);
    ```
    
    direction(optional): 생략하면 to bottom으로 설정되며 아래와 같은 옵션으로 설정할 수 있다.
        
    -   to bottom(기본), to top, to left, to right
    -   to top left, to bottom right 처럼 조합해서 사용할 수도 있다.
    -   to (방향) 기재 방식 대신에 [CSS에서 제공하는 각도 표현 방식](https://developer.mozilla.org/en-US/docs/Web/CSS/angle)을 사용해도 반영된다.
        
    ``` css
    body {
    background: linear-gradient(to right, red 20%,
	orange 20% 40%, yellow 40% 60%, green 60% 80%, blue 80%);
    }
    ```
            
    이런 식으로 설정하면 5가지 색이 블록 형태로 나타나도록 할 수도 있다. (그라데이션 아님!)
        
-   그 외 사용법은 잘 정리해둔 블로그가 많으니 생략….
        
응용  
[https://developer.mozilla.org/en-US/docs/Web/CSS/CSS\_Images/Using\_CSS\_gradient](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Images/Using_CSS_gradients)
    
<br>
## body에 linear-gradient를 적용하였을 때 반복되는 문제 해결법

div 등에 linear-gradient를 적용하면 정상 반영되는데, body에 linear-gradient를 적용하면 아래와 같이 의도치 않게 배경 그라데이션이 반복되는 현상이 발생했다. 반복 그라데이션을 원하는 경우에는 [repeating-linear-gradient](https://developer.mozilla.org/en-US/docs/Web/CSS/repeating-linear-gradient())를 사용하므로 이러한 동작은 불필요하다.

<p class="codepen" style="height: 258px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-height="258" data-theme-id="light" data-default-tab="css,result" data-user="hyo-choi" data-slug-hash="mdOjbbj" data-pen-title="linearGradientInBodyProblem"><span>See the Pen <a href="https://codepen.io/hyo-choi/pen/mdOjbbj"> linearGradientInBodyProblem</a> by Hyojeong Choi (<a href="https://codepen.io/hyo-choi">@hyo-choi</a>) on <a href="https://codepen.io">CodePen</a>.</span></p>
<p>
<script src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
</p>

해결법은 이쪽([https://stackoverflow.com/questions/2869212/css3-gradient-background-set-on-body-doesnt-stretch-but-instead-repeats](https://stackoverflow.com/questions/2869212/css3-gradient-background-set-on-body-doesnt-stretch-but-instead-repeats))을 참고하였고, 아래와 같은 속성을 추가해주면 된다.

``` css
/* (1) */
background-attachment: fixed;

/* (2) */
html {
	height: 100%;
}

body {
	height: 100%;
	margin: 0;
}
```

body에 기본적으로 height값이 설정되어있지 않고, background-attachment의 기본값이 scroll이기 때문에 발생하는 현상으로 보인다. 실제로 오류 케이스에서 내부에 포함된 div의 height를 조정하면 linear-gradient의 높이도 그만큼 증가하는 것을 확인할 수 있었다. 2번 솔루션의 경우 스크롤바가 제대로 표시되지 않는 이슈가 발생하기도 한다고 하여, 나는 1번 속성을 추가하기로 했다.

<p class="codepen" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="hyo-choi" data-slug-hash="KKNBPOW" data-pen-title="linearGradientInBodyProblemSolved"><span>See the Pen <a href="https://codepen.io/hyo-choi/pen/KKNBPOW"> linearGradientInBodyProblemSolved</a> by Hyojeong Choi (<a href="https://codepen.io/hyo-choi">@hyo-choi</a>) on <a href="https://codepen.io">CodePen</a>.</span></p>
<p>
<script src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
</p>

이제 그라데이션이 정상적으로 페이지 전체를 덮는 것을 확인할 수 있다.
