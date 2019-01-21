---
layout: post
title: "lambda 함수에 대해서..."
date:   2018-10-21 00:00:00
categories: C++Stl
author: luis lee
---
<!-- TOC -->

- [오늘의 주제: `lambda`](#%EC%98%A4%EB%8A%98%EC%9D%98-%EC%A3%BC%EC%A0%9C-lambda)
  - [함수 포인터, 함수 객체란?](#%ED%95%A8%EC%88%98-%ED%8F%AC%EC%9D%B8%ED%84%B0-%ED%95%A8%EC%88%98-%EA%B0%9D%EC%B2%B4%EB%9E%80)
  - [`lambda` 란?](#lambda-%EB%9E%80)
  - [`lambda` 의 사용법.](#lambda-%EC%9D%98-%EC%82%AC%EC%9A%A9%EB%B2%95)
  - [결론](#%EA%B2%B0%EB%A1%A0)

<!-- /TOC -->
# 오늘의 주제: `lambda`
lambda 를 공부하던 도중 검색을 통해 아주 괜찮은 설명을 하고있는 [블로그](http://carstart.tistory.com/183)를 찾았다.
본 포스팅은 위의 블로그를 참고하였으며 lambda 가 나오게 된 발전의 역사를 간략하게 서술하고 있으며 내가 공부한 내용을 다시한번 정리해보고자 한다.
## 함수 포인터, 함수 객체란?
함수 객체가 하는 역할은 범용성을 높이기 위해 사용된다. 예를들면 stl 의 qsort 함수를 이용하고자 하는데 만약 내가 정렬하려고 하는 데이터 타입이 내가 만들어 놓은 특정 클래스라고 생각해보자. stl 의 qsort 함수는 내가 정렬하고자 하는 데이터 타입의 클래스에 대한 정보를 알지 못하며 내 클래스 자체를 qsort가 처리해 줄 수 없다. 따라서 이때 사용되는 것이 함수 객체가 된다. 즉 qsort 에서 마지막 인자인 comparitor 에 해당하는 함수를 함수 객체를 이용해서 넘겨준다면 내가 원하고자하는 데이터 타입에 대해서 sorting이 가능하게 된다. 예시 코드는 다음과 같다.
```c
#include <iostream>
using namespace std;
class random_generator{
public:
    int n;
    random_generator(){n = rand()%40;}
};
int compare(const void* lhs, const void* rhs)
{
    int a = ((random_generator*)lhs)->n;
    int b = ((random_generator*)rhs)->n;
    if (a > b)
        return 1;
    else if (a < b)
        return -1;
    else
        return 0;
}
int main()
{
    random_generator random_array[40];
    qsort(random_array, 40 , sizeof(random_generator), compare); 
    for (int i = 0; i < 40; i++)
        cout << random_array[i].n << endl;
}
```
## `lambda` 란?
우리가 위에서 함수 객체를 사용하는 이유에 대해서 알아보았다. 그렇다면 lambda 는 무엇인가? <br/>
우리가 qsort 의 콜백 함수를 사용하기 위해서 새로운 형태의 compare 라는 함수 객체를 정의하였다. 이렇게 되면 필요할 때마다 새로운 함수 객체를 정의하여 사용해야하지만 람다는 다르다. 람다를 사용하게 되면 위의 compare함수를 정의하는 대신 qsort의 마지막 인자에 lambda function을 바로 정의함으로 써 콜백 함수를 넘겨줄 수 있게된다. 따라서 이제부터 람다의 사용법에 대해서 익혀보자.
## `lambda` 의 사용법.
람다의 문법은 capture , parameter , return type , body 로 이루어져있다. 
```c
[capture](parameter)->return type {body}
```
* parameter
* return type
* body 

위의 세가지의 경우는 크게 설명하지 않아도 이해가 간다. 우리가 기본적으로 함수를 구현하기 위해서는 인자, 리턴의 종류, 그리고 함수가 어떤일을 하는지에 대한 바디로 이루어진다. 그렇다면 람다에서 특이하게 capture라는 것이 있는데 이에 대해 따로 알아보자.
```c
[](const void* lhs,const void* rhs) //parameter
        ->int   //return type
        {   //body
            int a = ((random_generator*)lhs)->n;
            int b = ((random_generator*)rhs)->n;
            
            if (a > b)
                return 1;
            else if (a < b)
                return -1;
            else
                return 0;
        };
```
와 같이 사용할 수 있다.
* capture

람다 외부에서 정의되어있는 변수나 상수를 람다 내부에서 사용하기 위해 캡쳐를 해야한다. 이때 두가지 방법이 있는데 Copy 와 Reference 이다. <br/>
* Reference 로 캡쳐하는 경우
```c
[&(참조하고 싶은 변수)](const void* lhs,const void* rhs) //parameter
        ->int   //return type
        {   //body
            ...
        };
```
* Copy 로 캡쳐하는 경우
```c
[(복사하고 싶은 변수)](const void* lhs,const void* rhs) //parameter
        ->int   //return type
        {   //body
            ...
        };
```
그런데 복사의 경우 람다 함수 내부에서는 변경이 불가능하다. 캡쳐를 할 때 람다가 정의되어 있는 구간 모든 변수를 캡쳐 할 수 있는데 이 문법은 다음과 같다.
```c
[&](const void* lhs,const void* rhs) //parameter
        ->int   //return type
        {   //body
            ...
        };
```
```c
[=](const void* lhs,const void* rhs) //parameter
        ->int   //return type
        {   //body
            ...
        };
```
여기서 `&` 은 바디에서 쓰이는 모든 변수나 상수를 참조로 캡쳐하고 현재 람다 객체를 참조로 캡쳐한다.
`=`은 바디에서 쓰이는 변수나 상수를 복사로 캡쳐하며 현재 람다 객체를 참조로 캡쳐한다. 
<br/>
이외의 다양한 룰이 있지만 여기서는 다루지 않겠다.

## 결론
결론적으로 람다는 함수 객체를 생성하는데 함수 포인터와 함수 객체의 장점을 모두 가지고 있다고 할 수 있다. 일반 함수와 함수 객체의 차이점은 인라인화에 있으며 콜백 함수 사용시 일반 함수의 경우 주소로 접근하기 때문에 인라인 될 수 없지만 함수 객체의 경우는 인라인 될 수 있어 컴파일러에서 최적화가 될 수 있다. 따라서 특정 상황에서 일반 함수보다 빨리 작동할 수 있다. 따라서 람다함수는 이와 같은 함수 객체의 장점을 따왔으며 함수 포인터의 기능을 수행할 수 있고 함수 객체를 따로 만들지 않아도 익명의(anonymus) 함수로도 만들 수 있다. 따라서 간단한 함수 객체를 따로 선언하지 않고도 람다함를 통해 인라인 최적화가 되는 함수 객체의 역할을 대체 할 수 있는 것이다.