---
layout: post
title: "예외 처리"
date: 2018-11-05 00:00:00
author: luis lee
categories: c/c++뽀개기
tag: c/c++ 뽀개기
---

# C++ 에서의 예외처리

## 예외처리는 왜 할까?

아무리 천재적으로 코딩에 대한 이해도가 높더라도 인간이라면 실수를 하기 마련이다. 그리고 실수가 아니더라도
컴퓨터의 환경이나 기타 여건에 의해서 예외적인 상황이 발생할 수 있다. <br>
따라서 프로그램을 작성할 때 예외적인 상황에서의 처리가 중요하다.
<br>
만약 우리가 엄청나게 큰 프로젝트를 진행한다고 하자. 예를 들면 특정 파일을 읽어들여서 편집을 하는 프로그램을 만들었다고 하자. 프로그램상의 실수가 없어서 정말 잘 돌아가는 프로그램이라 하더라도 프로그램의 사용자가 지원하지 않는 파일을 입력할 수 도있고 파일 자체가 손상이 되어서 프로그램이 파일을 읽어들이지 못하는 경우에는 어떻게 될까? 또 파일이 너무 커서 메모리에 올릴수 없을정도라면 어떻게 될까? <br>
당연히 프로그램이 죽는다<br>
이런 경우가 예외적인 상황이 될 수 있으며 이러한 예외적인 처리를 하지 않고 프로그램을 퍼블리쉬하게 그건 사용자의 잘못이 아니라 프로그래머의 잘못이 되게된다.
<br>따라서 좋은 프로그램, stability 가 높은, 신뢰도가 높은 프로그램을 만들기 위해서는 좋은 예외처리가 필수적이라고 할 수 있다.

## 단순한 예외처리방법

그렇다면 특정 예외처리 코드를 사용하지 않고 예외를 처리하는 방법으로는 어떤것이 있을까?
<br> 예외가 발생했는지 아닌지를 판단해야하기 때문에 가장먼저 떠오르는 방법은 `if` 문을 이용한 방법이다.
<br> `divided by zero` 예외를 if문으로 해결한다고 생각해보자

```c
bool divide(float a, float b, float &result)
{
    if(b==0)
        return false;
    else
    {
        result = a / b;
        return true;
    }
}
```

즉 if 문을 사용해서 나누는 수 `b`가 0이라면 false 를 내뱉어주며 `b`가 0 이 아닌 경우에는 result를 수정하고 true를 내뱉어주도록 만들 수 있다.
<br> 지금은 간단한 하나의 함수를 예로 들었기 때문에 예외처리가 비교적 쉬워보인다. 하지만 함수가 복잡해지고 깊어질수록 예외처리가 까다로워진다.
<br>예를 들면,

```c
bool divide(float a, float b, float &result)
{
    if(b==0)
        return false;
    else
    {
        result = a / b;
        return true;
    }
}
bool function1(float a, float b , float & result)
{
    float temp;
    bool bTemp1 = divide(a,b-2,temp);
    if(bTemp1)
    {
        result = temp;
        return true;
    }
    else
    {
        return false;
    }

}
bool function2(float a, float b , float & result)
{
    a = 100*b +20;
    bool bFunc1 = function1(a,b,result);
    if(bFunc1)
    {
        return true;
    }
    else
    {
        return false;
    }
}
```

너무나 대충 짠 코드이지만 여기서 주목해야할 점은 devide 함수에서의 예외처리 결과를 devide를 사용하는 function 1, 2 모두에게
전달해야만 예외 처리가 가능하게 된다는 것이다. 따라서 함수가 깊어지면 깊어질수록 모든 함수에게 예외처리의 결과를 넘겨줘야하기 때문에
예외를 if문으로만 처리하기는 힘들다. 따라서 c++에서 어떤방식으로 이러한 예외를 쉽게 처리할 수 있는지 알아보도록 하자.

## c++ 에서의 예외처리

### throw, try & catch

#### throw

먼저 throw 란 예외가 발생했다는 사실을 말그대로 던져주는 것이다.

```c
void divide(float a, float b, float &result)
{
    if(b==0)
        throw std::overflow_error("divide by zero exception");
    result = a / b;
}
```

나누는 수 b가 0 인 경우에 overflow_error 라는 예외가 발생했음을 알려주며 함수 자체를 빠져나오게된다.
iso standard로 있는 예외의 종류는

```c
namespace std {
    class logic_error;
    class domain_error;
    class invalid_argument;
    class length_error;
    class out_of_range;
    class runtime_error;
    class range_error;
    class overflow_error;
    class underflow_error;
}
```

다음과 같은 종류가 있다.
throw로 던져줄수 있는 예외의 종류는 다음과 같은 예외종류 뿐만아니라 int, float, string 등 다양한 타입을 던져줄 수도 있다.
이에 대해서는 try & catch section에서 다루도록 하겠다.

#### try & catch

그렇다면 던져준 예외를 어떻게 처리를 하는지에 대해서 설명하도록 하겠다.<br>

```c
#include <iostream>
#include <stdexcept>
#include <vector>
using namespace std;
void function1(int i)
{
  if( i == 0 )
    throw std::underflow_error("under flow error at function1");
  else
    cout<<"function1 complete!!!"<<endl;
}
void function2(int i)
{
  if ( i == 0 )
    throw string("string error at function2");
  else
    cout<<"function2 complete!!!"<<endl;
}
void function3(int i)
{
  if ( i == 0 )
    throw 1;
  else
    cout<<"function3 complete!!!"<<endl;
}
void function4(int i, int j, int k)
{
  function1(i);
  function2(j);
  function3(k);
  cout<<"function4 complete!!!"<<endl;
}

int main() {
  int i, j ,k= 0;
  cin >> i >> j >> k;
  try {
    function4( i , j ,k);
  } catch (std::exception& e) {
    cout << e.what() << endl;
  } catch (string s){
    cout<< s <<endl;
  } catch (...){
    cout<<"default exception"<<endl;
  }

  return 0;
}

```

다음은 try & catch 문을 사용하는 방법에 대한 전체 예제 소스코드이다.
`function4`에서 function1, 2, 3 을 모두 수행시키는 예제이다. 코드를 보면 알겠지만 `function4`를 try 안에서 수행시키면
try안에서 수행되는 명령 중에 예외가 throw 된 경우 던져진 예외를 catch에서 처리한다.

```c
catch( 특정타입 )
```

을 통해서 특정 타입으로 throw된 예제를 각각 받아서 처리할 수 있으며

```c
catch(...)
```

같은 경우는 모든 예외에 대해 catch하게 된다. 만약 이를 가장 처음 위치로 옮긴다면 특정 타입에 의한 catch문까지 가지못하고
`catch(...)`에서 처리가 되기 때문에 마지막에 위치해야한다.
<br>
각 cin의 인풋에 대한 수행결과는 다음과 같다.<br/>

<img src="{{ site.baseurl }}/assets/img/cpp_exception/111error.png">
인풋이 1 1 1 인 경우에는 모든 함수가 수행이 된다.<br><br>
 <img src="{{ site.baseurl }}/assets/img/cpp_exception/110error.png">
function3 에서 throw가 발생하며 int 를 캐치하는 부분은 `catch(...)` 이기 때문에 default exception으로 캐치하게 된다.<br><br>
 <img src="{{ site.baseurl }}/assets/img/cpp_exception/100error.png">
function2 에서 throw가 발생하며 string 을 캐치하여 exception을 처리한다.<br><br>
 <img src="{{ site.baseurl }}/assets/img/cpp_exception/000error.png">
function1 에서 throw가 발생하며 `std::exception` 을 캐치하여 exception을 처리한다.<br><br>

# 결론

- c++에서 지원하는 예외처리 방식을 사용하자.
- 대충 예외처리를 하게되면 (if문으로) 함수가 깊어지거나 복잡해 지는 경우 처리가 어렵다.
- c++ 에서 지원하는 예외의 종류를 익히고 적재적소에 사용하자.
- 예외처리에서 catch하는 throw타입의 종류를 각각 따로 처리할 수 있다.
- catch의 타입에 따른 순서를 주의할 필요가 있다.(child,parent member 함수의 경우)
- 따로 처리되지 않은 throw 타입은 catch(...)으로 catch 가능하다.
