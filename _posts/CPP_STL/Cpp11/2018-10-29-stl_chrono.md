---
layout: post
title: "chrono 에 대해서..."
date:   2018-10-29 00:00:00
categories: C++Stl
author: luis lee
---
# 오늘의 주제: `chrono`
c++11 에서 새로 도입된 ctime 을 대체할 수 있는 stl 인 chrono에 대해서 알아보도록 하자.
chrono는 ctime 의 진화 버젼이라고 필자는 생각한다. 이는 더욱 최적화 되었으며 nano초 단위까지 측정할 수 있고 사용법이 굉장히 간단하다.
<br/> 사용법이 굉장히 간단하기 때문에 여기서는 굳이 모든 이야기를 구구절절 하지 않고 예제를 통해서 알아보도록 하겠다.
```cpp
#include <chrono>
#include <iostream>
#include <cmath>
```
chrono 와 이번 예제에서 사용할 헤더파일을 include하는 부분이다.
```cpp
void Test()
{
    for ( long i = 0; i < 10000000; ++i )
    {
        std::sqrt( 123.456L );
    }
}
```
지금 예제에서는 cmath의 sqrt 함수의 수행 시간을 재려고 한다.<br/>
```cpp
int main(int argc, char const *argv[])
{
    std::chrono::system_clock::time_point start = std::chrono::system_clock::now();
    Test();
    std::chrono::system_clock::time_point end = std::chrono::system_clock::now();
    std::chrono::duration<double> sec = end - start;
    std::cout << "Test() function operation time (sec) : " << sec.count() << " seconds" << std::endl;
    std::cout << "Test() function operation time (milisec) : " << std::chrono::duration_cast<std::chrono::milliseconds>(sec).count() << " millisec" << std::endl;
    std::cout << "Test() function operation time (nanosec) : " << std::chrono::duration_cast<std::chrono::nanoseconds>(sec).count() << " nanosec" << std::endl;

    return 0;
}
```
위의 예제를 하나씩 살표보자 <br/>
`std::chrono::system_clock::time_point` 에서 time_point 가 의미하는 것은 시간의 한 지점을 가리키는 변수라고 할 수 있다.<br/>
따라서 `time_point`를 `start` 와 `end` 지점을 설정하고 `start`와 `end`의 시간의 지점을 비교하여 수행시간을 측정 할 수 있다.<br/>
다음과 같이 수행 시간을 잴 수 있다. `start`와 `end`라는 시간 지점을 지정하고 그 사이에 `Test함수`를 수행시킨다면 `start`와 `end`의 차이가 `Test함수`가 수행된 시간이라고 할 수 있다.<br/>
그후 `chrono::duration<double>`에 end와 start의 차이를 저장한다. 레퍼런스에 의하면 duration은 time interval을 저장하는 class template이라고 한다. 여기서는 double 타입으로 tick 을 저장하며 이 의미는 tick 의 소숫점 까지도 저장이 가능하다는 의미라고 한다.duration.count()함수를 실행 시키면 이는 측정된 tick을 계산한다고 한다. 기본적으로는 sec단위로 나오게 되며 이를 mili sec 이나 nano sec 단위 까지 쪼갤 수 있다. <br/>
위의 예에서는 milisec 과 nano sec으로 타입 캐스팅을 통해서 interval을 나타내었다. `chrono::duration_cast<>`를 이용하면 된다.<br/>

## 정리
* ctime보다 더 정확하다.
* duration_cast를 이용해서 시간의 단위를 바꿔서 표현할 수 있다.
* time_point 가 의미하는 것은 현재 시스템 시간에 지점을 나타내는 것이다.
* duration은 시간의 interval을 나타내며 time_point의 interval을 저장한다.
* 사용이 용이하다.
