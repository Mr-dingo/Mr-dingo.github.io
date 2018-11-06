---
layout: post
title: "std::function에 대해서..."
tag: [c++ stl]
---

# 오늘의 주제: `std::function`

오늘의 주제는 `std::function` 이다. <br/>
`std::function` 에 대해서 조사하기에 앞서 함수 포인터, 콜백에 대해서 조사하고 이와 연관지어 `std::function` 에 대해 설명하자.

## 함수 포인터란 무엇인가?
함수 포인터란 말 그대로 함수를 포인터로 한다는것이다. 예를 들자면
```cpp
int function(int a){}
int (*pFunction)(int) = function; // 함수 포인터
```
와 같은 것이다. 여기서 `pFunction`은 우리가 첫줄에서 정의한 function의 시작 번지를 가리키는 포인터라고 할 수 있다. 어셈블리를 생각해보면 이해가 쉽다. 그러면 우리가 `pFunction(1)` 을 수행하게 되면 실제로는 `function(1)` 이 수행되는 것이다.<br/>
포인터의 형식이 기존의 포인터와는 다른것은 함수의 시작 주소를 포인팅하기 이전에 파라미터와 리턴형식을 스택에 저장해야하기 때문에 위의 
`int (*pFunction)(int) ` 와 같은 형식이다. 첫번째 `int`는 return값이라고 할 수 있고 두번째 `(int)`는 parameter type 이라고 할 수 있다. <br/>
함수 포인터를 사용하면 다음과 같은 응용도 가능하게 된다.
```cpp
void myfunction(int(*pFunction)(int))
{
    pFunction(1);
}
```
즉, 새로운 함수 myfunction에서 함수 포인터를 인자로 받을 수 있게되며 함수 내부에서 pFunction을 사용할 수 있게된다. <br/>
더 응용하자면 캐스팅, 포인터 배열, 포인터의 포인터 까지 가능해진다.
```cpp
//type casting
int(*funcPtr1)(int), double(*funcPtr2)(float);
funcPtr1 = (int(*)(int))funcPtr2;

typedef int(*FUNCPTR)(int); //함수 포인터 타입을 typedef로 정의하고(정의 형식 주의)
FUNCPTR* funcPPtr; //이렇게 double pointer
FUNCPTR funcPtr[MAX_FUNC_NUM]; //array 형식
```
1. 함수 포인터를 사용하면 함수를 변수로 사용가능.
2. 조건에 따라서 함수를 여러개를 사용해야되면 함수 포인터 배열을 이용해서 가독성을 높일 수 있다.
3. 함수가 DLL(외부모듈)에 있는 경우 함수 포인터를 사용해야된다.
## 함수 콜백이란 무엇인가?
특정 구역에 정의한 함수를 다른 클래스나 DLL등에 있는 함수에 전달하여 특정 구역의 함수를 호출하는법이다. openGL에서 render, mouse input, keyboard intput 함수를 opengl 내부 함수에 전달하여 사용하는 callback 을 사용했던적이 있는데, 이런게 함수 콜백이다.
```cpp
typedef int(*FUNCPTR)(int);
FUNCPTR your_func // 함수를 받을 변수
void register_function(FUNCPTR p_func) //외부에서 정의된 함수를 여기에서 사용하기위해 등록하는 함수.
{

    your_func = p_func;
}
void exec_func()
{
    your_func(); //등록된 your_func()실행.
}
```

```cpp
int Function(int a){...}
register_function(Function); //여기서 정의된 Function을 위의 구역에 등록시킴.
```

## `std::function` 은 무엇인가?

### 일반 function pointer 의 문제점.
1. 반환 값이 명시적으로 같은 타입이여야 한다.
2. 오로지 함수끼리만 호환가능.

### std::function을 이용하는 것의 장점.
1. 함수 반환값이 형변환 가능한 타입이면 대입가능.
2. static 함수 포인터 말고도 functor,클래스 멤버함수 포인터, 람다함수, bind 반환값 등 과 호환가능.
3. 유연한 사용이 가능하다.

`std::function` 을 사용하면
* 일반함수
* lambda 함수
* bind return 함수
* 함수 객체
* class 내의 맴버함수 및 맴버 변수

를 호출할 수 있다고 한다.
<br/>

```c
#include <functional>
std::function<리턴타입(입력 파라미터들)> 변수
void print(int a, int b);
//ex) std::function<void(int, int)> fp = print;
```
이런 식으로 사용가능하다.
<br/>
즉 위의 function pointer 와 유사하게 parameter type 지정, return type 지정을 한다. `std::function` 의 사용법은 다음과 같다.
```cpp
#include <iostream>     // std::cout
#include <functional>   // std::function, std::negate

// a function:
int half(int x) {return x/2;}

// a function object class:
struct third_t {
  int operator()(int x) {return x/3;}
};

// a class with data members:
class MyValue {
public:
    int value;
    MyValue(){}
    MyValue(int value){this->value = value;}
    int fifth() {return value/5;}
    void changeValue(int new_value){this->value = new_value;}
};

int main () {
    std::function<int(int)> fn1 = half;                    // function
    std::function<int(int)> fn2 = &half;                   // function pointer
    std::function<int(int)> fn3 = third_t();               // function object
    std::function<int(int)> fn4 = [](int x){return x/4;};  // lambda expression
    std::function<int(int)> fn5 = std::negate<int>();      // standard function object

    std::cout << "fn1(60): " << fn1(60) << '\n';
    std::cout << "fn2(60): " << fn2(60) << '\n';
    std::cout << "fn3(60): " << fn3(60) << '\n';
    std::cout << "fn4(60): " << fn4(60) << '\n';
    std::cout << "fn5(60): " << fn5(60) << '\n';

    // stuff with members:
    std::function<int(MyValue&)> value = &MyValue::value;  // pointer to data member
    std::function<int(MyValue&)> fifth = &MyValue::fifth;  // pointer to member function
    std::function<void(MyValue&,int)> changeValue = &MyValue::changeValue;
    MyValue sixty(60);

    std::cout << "value(sixty): " << value(sixty) << '\n';
    std::cout << "fifth(sixty): " << fifth(sixty) << '\n';
    changeValue(sixty,30);
    std::cout << "new_fifth(sixty): " << fifth(sixty) << '\n';

    //use bind
    MyValue ninety(90);
    std::cout << "value(ninety): " << value(ninety) << '\n';
    std::function<void(int)> changeValue_bind = std::bind(&MyValue::changeValue , &ninety, std::placeholders::_1);
    std::function<void()> changeValue_bind_ = std::bind(&MyValue::changeValue , &ninety, 120);
    std::function<void()> changeValue_bind__ = std::bind(&MyValue::changeValue , ninety, 120);
    changeValue_bind(60);
    std::cout << "value(ninety): " << value(ninety) << '\n';
    changeValue_bind_();
    std::cout << "value(ninety): " << value(ninety) << '\n';
    changeValue_bind__();
    std::cout << "value(ninety): " << value(ninety) << '\n';

  return 0;
}
```
>fn1(60): 30 <br/>
fn2(60): 30<br/>
fn3(60): 20<br/>
fn4(60): 15<br/>
fn5(60): -60<br/>
value(sixty): 60<br/>
fifth(sixty): 12<br/>
new_fifth(sixty): 6<br/>
value(ninety): 90<br/>
value(ninety): 60<br/>
value(ninety): 120<br/>
value(ninety): 120<br/>

다음과 같은 결과가 나온다. 위의 예제는 cplusplus.com 의 reference를 참고하여 만들었으며 std::bind 를 직접 사용하여보았다. 실제 DCS프로젝트에서 bind를 다루어본적이 있었고 그때 당시에는 여차저차하여 해결했지만 정확이 bind의 의미에 대해 이해하지는 못했지만 이번 공부를 통해 이해하게 되었다.
<br/>
사실 포스팅의 목적 상 더욱 자세하게 써야하지만 일단 포스팅이 익숙하지 않기 때문에 익숙해지는것을 1차적 목표로하고 나중에 점점 발전시키도록 하겠다.