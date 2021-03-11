---
layout: post
title: "[C++] 상수 멤버와 비상수 멤버"
categories: [STUDY, STUDY/CPP]
tags: [C++, Effective C++]
comment: true
---

💡 **기본적인 개념, 사용법에 대한 글이 아닙니다. Effective C++을 읽으며 기억해둘 만한 부분을 정리한 글입니다. 개념을 익힌 뒤 읽어보시면 좋습니다.**

<br>
-   **용어 정의**
    
    -   **비상수 멤버**: const 키워드가 없는 일반 멤버
    -   **상수 멤버**: (이 글 한정) 비상수 멤버와 기능상 차이 없이 const 키워드만 붙어 있는 멤버

-   함수의 선언에서 const 키워드가 있고 없고의 차이만 있는 함수들은 오버로딩이 된다.
    
-   예를 들어 operator\[\] 를 지원하는 클래스의 경우 상수 객체가 생성될 가능성이 있으므로 아래와 같이 두 종류의 operator\[\] 를 준비해두어야 한다. 상수 객체가 생기는 경우는 (1) 상수 객체에 대한 포인터 (2) 상수 객체에 대한 참조자로 객체가 전달될 때이다.
    

```cpp
class TextBlock {
public:
        ...
        char& operator[](std::size_t position) { ... }
        const char& operator[](std::size_t position) const { ... }
private:
        std::string text;
};

// 아래는 상수 객체가 사용되는 예

const TextBlock ctb("World");
void SomeFunction(const TextBlock& ctb) { ... }
```

-   상수 멤버와 비상수 멤버가 기능적으로 서로 똑같게 구현되어 있을 때에는 코드 중복을 피하는 것이 좋은데, 이때 비상수 버전이 상수 버전을 호출하도록 해야 한다.

```cpp
class TextBlock {
public:
        ...
        const char& operator[](std::size_t position) const { ... }
        char& operator[](std::size_t position)
        {
                return ( const_cast<char&>(
                                    static_cast<const TextBlock&>
                                                  (*this)[position])
                                );
            // (1) 비상수 객체를 상수 객체로 static_cast 한다.
            //     const를 붙이는 캐스팅은 안전한 타입 변환(비상수 -> 상수)을
            //         강제로 하는 것이므로 static_cast면 충분하다.
            // (2) cast된 상수 객체를 이용하여 const operator[]를 호출한다.
            // (3) return된 상수 참조자에서 const를 제거한다.
        }
        ...
};
```

-   중요한 것은, 코드 중복을 피하기 위해 위와 같은 방식을 고려할 때 **상수 멤버가 비상수 멤버를 호출하도록 해서는 안 된다**는 것이다. 비상수 함수 내부에서는 멤버의 내용을 수정하든 하지 않든 자유이므로 상수 함수를 불러 써도 아무런 문제가 되지 않지만, 상수 함수는 멤버의 내용을 수정하지 않기로 약속되어 있으므로 내부에서 비상수 함수를 불러 쓰는 것이 잘못된 방식인 것이다.

<br>
### Reference

-   스콧 마이어스 지음, 곽용재 옮김, "Effective C++," 제3판.
    -   항목 3: 낌새만 보이면 const를 들이대 보자!, 59.
