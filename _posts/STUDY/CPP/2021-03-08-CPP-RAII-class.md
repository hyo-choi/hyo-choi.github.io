---
layout: post
title: "[C++] 자원 관리 클래스"
categories: [STUDY, STUDY/CPP]
tags: [C++, Effective C++]
---

💡 **기본적인 개념, 사용법에 대한 글이 아닙니다. Effective C++을 읽으며 기억해둘 만한 부분을 정리한 글입니다. 개념을 익힌 뒤 읽어보시면 좋습니다.**

<br>
- **읽기 전에**

    책에서 언급된 **auto_ptr는 C++11부터 deprecated 되었고 C++17 이후에는 아예 삭제**되었다. 대신 unique_ptr를 사용하자. unique_ptr에서는 auto_ptr에서는 해결할 수 없었던 부분(new[]로 할당한 포인터에 대한 delete 호출)도 해결할 수 있다.

<br>
새로 할당한 자원을 사용할 때에는 그 자원의 사용이 끝나면 메모리가 해제된다는 보장이 필요하다. 간단한 프로그램에서는 new에 짝이 되는 delete를 맞춰주기만 하면 되므로 필요성을 느끼기 어렵겠지만, 코드가 커지고 복잡해질수록 자원의 할당/해제가 까다로워지고 사용자가 실수할 가능성이 커진다. 블록 혹은 함수 내부에서 분기가 생길 때, 특정 함수에서 할당한 포인터를 반환받을 때 등 실수할 가능성은 무궁무진하다.

그런 이유로 생성자와 소멸자가 자동 호출된다는 특징을 이용한 자원 관리 클래스가 등장하였다. C++의 스마트 포인터는 이러한 자원 관리 클래스의 일종으로, unique_ptr, shared_ptr, weak_ptr가 이에 해당된다. (상기하였듯 auto_ptr는 C++11 이후로 사용할 수 없다.)

아래처럼 자원 획득과 자원 관리 객체의 초기화가 한 문장에서 이루어지는 것을 **자원 획득 즉 초기화**(Resource Acquisition is Initialization: **RAII**)라고 한다.

```cpp
// 사용 예
std::unique_ptr<int> ptr_a(new int(5));
std::unique_ptr<int[]> ptr_b(new int[10]);
std::shared_ptr<int> ptr_c(new int[10], std::default_delete<int[]>());
```

RAII 클래스에 구현하는 일반적인 복사 동작은 복사를 금지하거나 참조 카운팅을 해주는 선에서 마무리된다. 따라서 자원의 깊은 복사를 수행하는 '진짜 복사' 동작 등이 필요하다면 별도의 RAII 클래스를 작성하여 사용하도록 한다. (또 다른 복사 동작 중 하나인 소유권 이전 동작은 unique_ptr에도 구현되어 있다.)

<br>
## 스마트 포인터의 이해

### **unique_ptr (C++11~)**

[unique_ptr](https://en.cppreference.com/w/cpp/memory/unique_ptr)는 단어의 뜻 그대로 하나의 unique한 포인터를 관리하는 클래스이다. 복사 생성자는 정의되어있지 않으며 소유권을 이전하기 위한 [이동 연산자](https://en.cppreference.com/w/cpp/memory/unique_ptr/operator%3D)는 존재한다. (이 연산자는 오직 [std::move](https://en.cppreference.com/w/cpp/utility/move)를 이용한 rvalue에 대해서만 사용할 수 있다.) 소유권이 이전된 unique_pointer는 nullptr을 가리키는 댕글링 포인터가 되므로 이후 재참조하지 않도록 주의해야 한다. 자세한 사용법과 설명은 [이쪽](https://en.cppreference.com/w/cpp/memory/unique_ptr)을 참고.

unique_ptr는 두 개의 템플릿 인자를 가지며 두 번째 템플릿 인자는 디폴트로 [std::default_delete](https://en.cppreference.com/w/cpp/memory/default_delete)를 가진다. default_delete는 배열에 대응하는 템플릿도 가지고 있기 때문에 배열으로 객체를 생성했을 경우 해당되는 Deleter는 new[]에 대응하는 delete[] 형태로 맞춰진다. 그러니 auto_ptr에서 발생하던 문제에 대해 특별히 고려하지 않아도 된다. 특정 객체에 대한 Deleter를 따로 만들어 넣고 싶다면 두 번째 템플릿 인자에 functor를 전달해주면 된다.

```cpp
// (1)
template<
    class T,
    class Deleter = std::default_delete<T>
> class unique_ptr;

// (2)
template <
    class T,
    class Deleter
> class unique_ptr<T[], Deleter>;

// default_delete
template< class T > struct default_delete;
template< class T > struct default_delete<T[]>;
```

C++14부터는 [std::make_unique 함수](https://en.cppreference.com/w/cpp/memory/unique_ptr/make_unique)를 제공하므로 해당 함수를 사용하면 좀 더 간편하게 unique_ptr를 만들 수 있고, C++11부터 도입된 [auto 키워드](https://boycoding.tistory.com/184)(타입 추론)를 사용하면 더 간편하게 쓸 수 있다.

```cpp
// 그냥 만들면
std::unique_ptr<Vec3> ptr(new Vec3());

// 별로 안 간편해보이지만 make_unique를 사용한 예
std::unique_ptr<Vec3> v1 = std::make_unique<Vec3>();
// auto를 이용해 간편해보이는 make_unique
auto v2 = std::make_unique<Vec3>();

// 초기화하여 생성하기
std::unique_ptr<Vec3> v3 = std::make_unique<Vec3>(0,1,2);
// Vec3[5]의 unique_ptr 생성하기
std::unique_ptr<Vec3[]> v4 = std::make_unique<Vec3[]>(5);
```

<br>
### shared_ptr (C++11~)

[shared_ptr](https://en.cppreference.com/w/cpp/memory/shared_ptr)는 대표적인 **참조 카운팅 방식 스마트 포인터**(Reference-Counting Smart Pointer: **RSCP**)다. RCSP는 특정 자원을 참조하는 외부 객체의 수를 유지하고 있다가 그 개수가 0이 되면 해당 자원을 자동으로 삭제한다. 이러한 특징은 가비지 컬렉션과 상당히 흡사하지만 참조 상태가 순환되는 현상을 없앨 수 없다는 점에서는 가비지 컬렉션과 다르다. 자세한 사용법과 설명은 [이쪽](https://modoocode.com/252)을 참고.

shared_ptr의 경우 unique_ptr과 다르게 디폴트 템플릿 인자 Deleter를 지원하지 않기 때문에, 배열을 생성자 오버로딩을 통해 생성자의 매개변수로 std::default_delete<T[]> 혹은 별도의 functor를 넘겨주어야 한다.

```cpp
// 잘못된 생성자 호출의 예.
std::shared_ptr<int> shared_bad(new int[10]);

// 바른 생성자 호출 예시 (1)
std::shared_ptr<int> shared_good(new int[10], std::default_delete<int[]>());

// 바른 생성자 호출 예시 (2)
struct D { void operator()(Foo* p) const { delete p; }};
...
std::shared_ptr<Foo> sh4(new Foo, D());

// 바른 생성자 호출 예시 (3)
std::shared_ptr<Foo> sh5(new Foo, [](auto p) { delete p; });
```

그런데 위의 방법도 아주 완벽한 것은 아니라고 한다. shared_ptr은 참조 카운팅을 위한 제어 블록을 가지고 있는데, 이로 인해 위처럼 shared_ptr를 할당하면 동적 할당이 두 번 일어나야 한다. 동적 할당은 비용이 큰 동작이므로 한 번의 할당만 일어날 수 있도록 하기 위해 [std::make_shared](https://en.cppreference.com/w/cpp/memory/shared_ptr/make_shared) 함수를 사용하자. 단 배열형 shared_ptr에 대한 커스텀 Deleter 추가는 C++20에서나 추가된 기능이라고 하니 사용에 주의해야 할 것이다.

- ……까지 쓰고 보니 make_unique와 좀 헷갈리기 시작하는데, make_unique는 템플릿 단위에서 기본 Deleter를 설정해주기 때문에 make_unique에서도 크게 신경써줄 것이 없는 반면 make_shared는 기본 Deleter 개념이 없기 때문에 그런 것 같다. 굳이 왜 다른 구조로 만든 걸까 싶지만 우리는 남이 잘 만들어둔 것을 받아쓰는 입장이니 특성을 잘 이해하고 사용하는 것이 최선이겠다.

**그 외의 사용 시 주의사항은 [이쪽](https://modoocode.com/252#page-heading-2)에 설명이 정말 잘 되어있으니 꼭 읽어보고 사용하자.**
이유를 생략한 한 줄씩 요약:

- shared_ptr가 shared_ptr 외의 주소값(예를 들자면 int *a = new int[5]; 처럼 할당된 포인터)을 통해 생성되는 상황을 지양하자.
- 객체에서 자기 자신(this)을 가리키는 shared_ptr를 사용하고 싶다면std::enable_shared_from_this을 public 상속하고 기반 클래스의 shared_from_this() 멤버 함수를 이용하여 해당 객체에 대한 shared_ptr을 얻자.
- 구조상 shared_ptr간의 상호 참조가 필요할 때에는 weak_ptr를 활용하자.

<br>
## 자원 관리 클래스를 사용할 때 고려할 점

### 뮤텍스 관리를 위한 클래스

블록이 끝날 때 뮤텍스가 자동으로 해제되도록 해주는 자원 관리 클래스를 만들고 싶다면 어떻게 해야 할까?

```cpp
// (1)번 방법
class Lock {
public:
        explicit Lock(Mutex *pm) : mutexPtr(pm)
        { lock(mutexPtr); }
        ~Lock() { unlock(mutexPtr); }
private:
        explicit Lock(const Lock& rhs);// 복사를
        Lock& operator=(const Lock& rhs);// 막는다.
        Mutex *mutexPtr;
};

...
Mutex m;// 뮤텍스 정의
...
{// 임계 영역 지정을 위한 블록 설정
Lock ml(&m);// Lock 객체 생성과 동시에 Mutex lock
        ...
}// Lock의 소멸자 호출 -> 자동 unlock
...
```

이런 식으로 설정해주면 자동으로 lock, unlock 되는 자원 관리 객체를 만들고 사용할 수 있다. 책에서는 shared_ptr을 사용한 예시 코드도 소개하고 있다.

```cpp
// (2)번 방법
class Lock {
public:
        explicit Lock(Mutex *pm) : mutexPtr(pm, unlock)
        { lock(mutexPtr.get()); }
        explicit getPtr()
// 소멸자가 따로 없는 것에 주목.
private:
        std::tr1::shared_ptr<Mutex> mutexPtr;
};

...
Mutex m;// 뮤텍스 정의
...
{// 임계 영역 지정을 위한 블록 설정
Lock ml(&m);// Lock 객체 생성과 동시에 Mutex lock
        ...
}// Lock의 소멸자 호출 -> shared_ptr의 ref count가 0이므로 unlock
...
```

이 경우 클래스에 소멸자를 따로 선언하지 않았는데, 소멸자를 따로 선언하지 않으면 기본 소멸자가 비정적 데이터 멤버의 소멸자를 호출해주기 때문이다. 이때 shared_ptr는 참조 카운터가 0이 되었을 때 Deleter를 이용해 소멸되므로 해당 mutexPtr이 다른 곳에서 참조되고 있지 않으면 소멸되며 unlock 될 것이고, 다른 곳에서 참조되고 있다면 소멸되지 않아 lock 상태를 유지할 것이다.

❓ 그런데 나는 (2)가 실용성 있는 방법인지 잘 모르겠다. 일단 getter 등을 이용해 mutexPtr을 객체 외부에서 참조하게 된다면 ref count로 인해 unlock이 정상적으로 되지 않을 것이고(물론 그렇게 하지 않는 것이 의도된 동작인 것 같다.), 멀티스레딩 환경에서 하나의 뮤텍스를 여러 Lock 객체에서 참조하여 사용하는 상황을 생각해도 unlock은 ref count가 0이 될 때만 실행되므로 의도한 대로 동작하지 않을 것 같다. 단순히 뮤텍스의 락/언락 관리에는 (1)번 방법이 훨씬 효율적이고 이해하기에도 좋은 것 같다. 검색해봐도 뾰족한 답이 안 나와서 찝찝하지만 이 부분은 그냥 의문으로 남겨두고 다른 사람들과 상의해보거나 해야겠다.

<br>
### RAII 객체 생성은 별도의 한 문장으로 만들자

(= RAII 객체 생성 시 예외가 끼어들 틈을 주지 말자)

```cpp
int priority() { ... }
void processWidget(std::tr1::shared_ptr<Widget> pw, int priority)
            { ... }
...
// processWidget 함수를 호출
processWidget(std::tr1::shared_ptr<Widget>(new Widget), priority());
```

위의 코드를 생각해보자. 아무 문제 없어 보이지만, 사실 이 코드는 컴파일러에 따라 메모리 누수를 일으킬 수도 있는 코드이다. 컴파일러는 processWidget 호출 코드를 만들기 전에 함수의 인자를 평가하는 순서를 밟는다. 두 번째 인자의 경우 priority 함수를 호출할 뿐이지만, 첫 번째 인자는 "new Widget"을 실행하는 부분과 std::tr1::shared_ptr의 생성자를 호출하는 두 부분으로 나뉜다. 그러므로 컴파일러는 processWidget 함수를 호출하기 전 다음의 세 가지 연산을 위한 코드를 만들어야 한다.

- priority() 호출
- "new Widget"의 실행
- std::tr1::shared_ptr의 생성자 호출

이 중 "new Widget" 다음에 std::tr1::shared_ptr의 생성자가 호출되어야 한다는 것은 명백하다. 그러나 priority()의 호출 시점은 컴파일러에 따라 다르다. (이것은 C++ 컴파일러의 특징이다. 자바와 C#은 매개변수의 평가 순서가 특정하게 고정되어 있다.) 예를 들어 각 코드가

- "new Widget"의 실행
- priority() 호출
- std::tr1::shared_ptr의 생성자 호출

이런 순서로 실행될 수도 있다. (컴파일러가 이렇게 하는 것이 효율 면에서 더 좋을 수 있다고 하는데 정확한 이유는 모르겠다.) 그런데 이런 순서에서 priority()가 예외를 던진다면? RAII 객체가 생성조차 되지 않았기 때문에 새로 할당한 포인터는 유실되고, 이는 그대로 누수로 이어질 것이다. 즉, 실행 순서 상 자원이 생성되는 시점과 자원 관리 객체의 생성 시점 사이에 예외가 끼어들 수 있는 상황은 늘 누수의 위험을 안고 있는 것이다.

컴파일러가 어떻게 동작할지 알고 우리가 이걸 방지하나! 라는 생각이 들 수도 있지만, 이 항목의 제목을 보면 아주 간단하게 해결할 수 있다. RAII 객체 생성을 항상 별도의 한 문장으로 만들면 된다. new로 메모리를 할당하는 시점과 RAII 객체의 생성 사이에 예외가 끼어들 틈을 주지 말자. 이런 점을 고려하지 않으면 예외 발생 시 디버깅하기 힘든 자원 누출이 초래될 수 있다.

```cpp
std::tr1::shared_ptr<Widget> pw(new Widget);// 예외가 끼어들 틈이 없다.
processWidget(pw, priority());
// 이제 priority()에서 예외가 발생해도 스마트 포인터가 메모리를 잘 해제해줄 것이다.
```

<br>
### 자원 관리 클래스가 관리하는 자원은 외부에서 접근할 수 있도록 하자

```cpp
std::tr1::shared_ptr<whateverClass> ptr
        = std::make_shared<whateverClass>(); // (1)
int whateverFunction(const whateverClass *p) { ... } // (2)
int result = whateverFunction(ptr); // (3)
```

위의 코드를 보고 생각해보자. (3)번 라인은 제대로 컴파일될까?

정답은 NO다. 당연하다. whateverFunction의 인자로는 whateverClass에 대한 포인터가 요구되는데, ptr은 std::tr1::shared_ptr 타입이니 안 되는 게 맞고 컴파일러는 옳은 일을 했다. 그러니 우리는 컴파일러가 제대로 작동할 수 있는 코드를 만들어줘야 한다. RAII 객체에서 발생하는 이러한 문제는 일반적으로 두 가지 방법을 통해 해소될 수 있는데, 하나는 **명시적 변환**이고 다른 하나는 **암시적 변환**이다.

- **명시적 변환**

    명시적 변환은 간단하다. RAII 객체가 내부에 가지고 있는 실제 포인터를 반환하는 함수를 이용하는 것이다. C++에서 제공하는 스마트 포인터의 경우 get이라는 멤버 함수를 가지고 있는데, 별도 처리 없이 해당 함수를 호출하는 것만으로도 원래 자원의 포인터를 얻어올 수 있다.

    스마트 포인터를 사용하는 경우 이뿐만 아니라 오버로딩된 opeator→, operator* 연산자를 통해 get()을 이용하지 않고도 클래스 객체로 된 자원의 멤버에 바로 접근할 수 있다.

    ```cpp
    class Foo {
    public:
        bool bar();
    };

    std::tr1::shared_ptr<Foo> ptr = std::make_shared<Foo>();

    Foo* origin = ptr.get();// 명시적 변환 함수를 통해 할당된 자원의 포인터를 얻어온다.
    bool a = ptr->bar();// 와! 할당된 자원 없이 스마트 포인터만으로 멤버에 접근할 수 있다.
    bool b = (*ptr).bar();// 역참조의 경우에도 마찬가지로 정상 작동한다.
    ```

- **암시적 변환**

    명시적 변환(주로 get())을 통해 포인터를 얻어오는 것이 너무 싫고 번거로운 사람도 있을 수 있다. 이런 경우 [캐스팅 연산자 오버로딩](https://www.qaupot.com/wordpress/?p=2253)을 통해 암시적 변환 함수를 제공하면 된다.

    ```cpp
    class Font {// RAII 클래스
    public:
        ...
        operator FontHandle() const { return f; }// 암시적 변환 함수
    private:
        FontHandle f;// 실제 관리되는 자원(객체)
    };
    ...
    void changeFontSize(FontHandle f, int newsize) { ... }
    ...
    Font f(getFont());// Font형으로 선언한 RAII 객체
    int newFontSize;

    changeFontSize(f, newFontSize);
    // 암시적 변환 함수로 인해 FontHandle으로 자동 형변환된다.
    // 좋아 보이지만...

    FontHandle f2 = f;// 오잉?
    ```

    좋아 보이지만…...? 암시적 형변환이 있을 때 마지막 라인처럼 사용하는 경우 (1) Font 객체를 복사할 의도였더라도 FontHandle형으로 자동 형변환되어 복사된다. (2) 따라서 Font라는 RAII 객체가 관리하고 있는 FontHandle 객체가 f2를 이용해서도 직접 사용할 수 있는 상태가 된다. (3) Font형 객체 f가 소멸되면 f2는 해제된 자원에 매달려있게 된다. ……는 문제들이 생긴다.

RAII 객체로부터 실제 자원을 얻어오는 방법으로 명시적 변환을 제공할 것인지 암시적 변환을 허용할 것인지에 대한 결정은 해당 클래스의 용도와 사용 환경에 따라 달라진다. 둘 중 무엇을 사용하든 잘 설계한 클래스라면 "맞게 쓰기는 쉽게, 틀리게 쓰기에는 어렵게" 만들어져 있어야 할 것이다.

늘 그런 것은 아니지만 암시적 변환보다는 명시적 변환을 사용하는 쪽이 나을 때가 많은데, 명시적 변환을 이용하면 원치 않는 타입 변환의 여지가 줄어들기 때문이다. 다만 사용 시 자연스럽게 일어나는 암시적 타입 변환이 빛을 발하는 경우가 있다는 것도 잊지 않고 염두에 두어야 하겠다.

또, RAII 클래스에서 자원 접근 함수를 제공하는 설계가 캡슐화에 위배되지 않는가 하는 생각이 들 수 있는데, 캡슐화 측면에서 보면 그렇지만 RAII 클래스는 애초부터 데이터 은닉을 목적으로 하는 클래스가 아니므로 자원 할당과 해제가 제대로 일어나기만 하면 목적에 맞게 잘 설계된 것이라 볼 수 있다. 결론적으로는 사용자가 볼 필요가 없는 데이터에 대한 접근은 막아주고, 목적상 접근이 필요한 데이터를 향한 통로는 열어주는 것이 RAII 클래스의 올바른 설계라고 볼 수 있다.

<br>
### Reference

- 스콧 마이어스 지음, 곽용재 옮김, "Effective C++," 제3판.
    - 항목 13: 자원 관리에는 객체가 그만!, 118. ~ 항목 17: new로 생성한 객체를 스마트 포인터에 저장하는 코드는 별도의 한 문장으로 만들자, 135.
