---
layout: post
title: "Move semantics in C++"
date: 2018-12-22 00:00:00
author: luis lee
categories: c/c++뽀개기
tag: c/c++ 뽀개기
---

# Move semantics

c++ 11 부터 move semantics 라는 개념이 도입되었다.<br>
이는 r-value reference 를 기반으로 동작한다. r-value reference (우측값 참조)는
웹상에 굉장히 많은 자료가 있으며 이를 이해하는것은 비교적 쉽다.
<br>
하지만 필자는 move semantics 에 대해서

- 이 기능이 왜필요할까?

에 대한 의문점이 계속해서 생기게 되었고 많은 자료를 참고했지만 이해가 쉽지 않았다.
<br>때문에 이 포스팅에서는 위의 두 의문에 대해 필자가 생각하는 바를 이야기하려고 한다.

## Move semantics 는 왜 필요한가?

다음과 같은 예를 생각해보자.

```c++
class MyClass{
public:
    int * m_data;   //저장할 데이터
    int m_size;     //데이터 갯수

    MyClass(){/*Default 생성자*/}
    MyClass(int i)
    :m_data(new int[i]), m_size(i)
    {
        memset(m_data,0,m_size);
    }
    MyClass(const MyClass & src)
    :m_data(new int[src.m_size]), m_size(src.m_size)
    {
        //복사 생성자
        memcpy(this->data, src.data, sizeof(src.data);
    }
    MyClass(MyClass && src)
    :m_data(src.m_data), m_size(src.m_size)
    {
        //이동 생성자
        src.m_data=nullptr;
    }
    ~MyClass(){
        delete [] m_data;
    }
}
```

굉장히 간단한 예제이다.
`MyClass`에는 m_data 를 저장하고 있으며 구현되어있는것은 복사생성자, 이동생성자, 소멸자이다.
<br>

```c++
//in main
    MyClass m1(100000000);
    { ... m1 에 랜덤한 data 1억개를 저장 ... }
    MyClass m2(m1);                 //복사 생성자
    MyClass m3(MyClass(100000000)); //이동 생성자

    //std::move 사용하기...
    //include <utility> 해줘야함.
    MyClass m4(std::move(m1));
    //이러한 표현도 있음. 강제적으로 m1을 r-value로 캐스팅.
    //다만 이후 m1을 사용하지 않아야함.

```

다음과 같이 메인함수 안에서 복사 생성자와 이동생성자를 부른다고 하자.
복사생성자의 경우는

```c++
MyClass(const MyClass & src)
    :m_data(new int[src.m_size]), m_size(src.m_size)
    {
        //복사 생성자
        memcpy(this->data, src.data, sizeof(src.data);
    }
```

다음과 같이 소스의 데이터를이용해서 복사하는 연산이다.
즉 타겟의 데이터를 초기화시키고 그안에 소스 데이터를 복사하는 식으로 이루어진다.
이 경우네는 1억개의 int 타입을 카피하는 과정이 일어나게 되는 것이다.
<br>
그런데 이동생성자를 호출하는경우, 즉 `m3`을 생성하는 경우는

```c++
MyClass(MyClass && src)
    :m_data(src.m_data), m_size(src.m_size)
    {
        //이동 생성자
        src.m_data=nullptr;
    }
```

다음과 같이 복사의 연산이 아니라 소스의 데이터주소를 바로 참조해버리는 이동의 과정이 일어난다.
즉, 위에서 m3는 r-value값이 인자(argument)로 들어왔기 때문에 이 인자를 다시 복사할 필요가 없다.
들어온 인자는 어차피 재사용되지 않을것이 명료하기 때문이다. <br>
따라서 r-value를 이용하는 생성자의 경우에 복사 생성자를 이용하는 것은 매우 비효율적이다.
그렇기 때문에 이동생성자의 개념이 나오게 된 것이다.
그런데 이동생성자 내부에서 m_data 를 null 으로 만들어주는 과정이 있는데 이는
어차피 src 는 r-value 이므로 인자로서의 역할이 끝나게 된다면 , 제거되는 값이다.
그런데 src.m_data 를 nullptr로 만들어주지 않는다면 this 의 데이터와 같은 장소를 가리키고 있기 때문에
소멸될때 데이터를 같이 없애버리게된다.
그렇기 때문에 nullptr으로 만들어줘야하는 것이다.
<br>
그 결과로

- this->m_data 는 1억개의 정수가 저장된 주소에 포인팅을 하게되고
- src->m_data는 nullptr을 가지게된다.
- 따라서 src가 소멸될때는 m_data 가 가리키고있는 1억개의 정수는 안전하게 된다.

## 정리

- r-value가 인자로 사용되는 경우, 그 인자는 재사용되지 못한다.
- 명료하게 재사용하지 않을 r-value를 복사하는 것보다 r-value를 직접 이용해보자는 것이 이동연산의 핵심인듯하다.
- 이동연산을 통해 r-value 와 타겟 value 를 교환(여기서는 data pointer 를 교환)하자.
- 이 예제에서 교환 후 r-value의 포인터를 null포인터로 만들어진것을 주목하자.
- r-value 는 소멸될텐데 이때 r-value를 이용해 이동생성한 인스턴스의 데이터도 같이 소멸하면 안된다.
- 현재 stl에는 이동생성자가 잘 정의되어있으니 적극활용하자.
