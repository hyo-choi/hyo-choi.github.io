---
layout: post
title: "[C] calloc의 c는 어떤 의미일까?"
categories: [STUDY, STUDY/CPP]
tags: [C]
comment: true
---

사**소**한 궁**금**증 해결: 소금 시리즈

## calloc의 c는 어떤 의미일까?

malloc의 m이 memory라는 것은 알고 있었는데, 문득 calloc은 무슨 뜻인가 하는 궁금증이 생겨 검색해보았다.

- **[번역]**  
(원문: [https://stackoverflow.com/questions/31888422/what-does-the-first-c-stand-for-in-calloc](https://stackoverflow.com/questions/31888422/what-does-the-first-c-stand-for-in-calloc))

    Linux System Programming(by Robert Love)에 따르면, calloc의 어원에 대한 공식 소스는 없습니다.

    ———

    몇 가지 그럴듯한 후보들이 있는데:

    1. Count or Counted
    `calloc`이 별도의 카운트 인자를 가지고 있기 때문에.
    2. Clear
    `calloc`이 반환된 메모리 덩어리가 깔끔해진 것을 보장하기 때문에.
        - [Brian Kernighan](https://ko.wikipedia.org/wiki/%EB%B8%8C%EB%9D%BC%EC%9D%B4%EC%96%B8_%EC%BB%A4%EB%8B%88%ED%95%B8)는 "c"가 clear의 약자라 생각한다고 알려져 있습니다(그 스스로 확신할 수 없다고는 인정했음에도).
        - (원문 댓글 참조) 초기의 [`calloc.c`](https://github.com/dspinellis/unix-history-repo/blob/Research-V7/usr/src/libc/gen/calloc.c) 는 소스 코드 주석에 clear라는 명확한 단어를 포함하고 있습니다(count나 다른 후보들에 대한 언급은 없습니다). [`malloc.c`](https://github.com/dspinellis/unix-history-repo/blob/Research-V7/usr/src/libc/gen/malloc.c) 의 소스 코드 주석을 보면, `calloc` 에 대해 언급하며 clear라는 단어가 다시 사용되고 있습니다.
    3. C 언어의 C
        - ([alk의 답변](https://stackoverflow.com/questions/31888422/what-does-the-first-c-stand-for-in-calloc/31906184#31906184) 및 댓글 참조) 같은 시기에 소개된 함수들에 대한 명명 규칙일 가능성이 있습니다.

라고 한다. 다른 의견들도 해당 글에 많이 달려있으니 다른 의견이나 추가 정보가 필요하면 읽어볼 수 있겠다. 내 생각에는 clear나 C 언어 그 자체를 의미한다는 것이 꽤 신빙성 있는 것 같다.
