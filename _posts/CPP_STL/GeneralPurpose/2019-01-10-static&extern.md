---
layout: post
title: "C++ Volatile 키워드"
date: 2019-01-10 00:00:00
author: luis lee
categories: c/c++뽀개기
tag: c/c++ 뽀개기
---

# static global 변수 (feat. extern)

보통의 경우 static 변수는 함수 내에서 지역변수 선언시 프로그램 종료까지 살아있길 바랄때 사용한다. 예를들어 특정 함수가 몇번 call 됬는지 알고 싶을때 전역변수를 사용하기보다는 static local 변수를 사용하는 것이다.

```cpp
void myFunction(){
    static int numberOfCall = 0;
    //do something
    ++numberOfCall;
}
```

이런식으로 사용하게되면 첫 call 에서만 numberOfCall 변수를 initialize 하고 그 후에 다시 call 될 때는 initialize 하지 않는다. 따라서 myFunction 이 몇번 콜 됬는지 알 수 있다.

하지만 다양한 프로젝트의 소스코드를 확인해보면 static 을 사용한 global variable 을 볼 수 있다.

이러한 global 변수에 static 키워드를 붙이는 경우에는 지역변수에서의 static 과 다른 역할을 한다고 볼 수 있다.

- static 의 역할 : 현재 파일 내에서의 지역변수로 바뀐다. 
즉, 현재 파일 내에서만 사용가능한 변수가 되버린다.
- extern 의 역할 : 다른 파일에서 이미 이름이 같은 전역변수가 선언이 되었다는 의미.
즉, 다른 파일간의 변수를 공유하고 있다라는 뜻이다.
```cpp
// in main.cpp file
int externVariable = 1;
static int staticGlobalVariable = 0;
```
```cpp
// in header.cpp file
extern int externVaribale;
static int staticGlobalVariable = 1;
```
다음과 같이 구성된 파일이 있다고 할때

- **static 이 붙어있는 변수의 경우**
    - 현재 파일 내에서만 존재하는 파일의 지역변수 취급을 받기 때문에 이름이 같더라도 서로다른 파일에서 각각 다른 변수로 취급이 된다.
    - 따라서 static 변수를 통해 혹시 모를 전역변수의 이름이 같게되는 문제를 조금이나마 완화할 수 있다.
- **extern 이 붙어있는 변수의 경우**
    - 정말 global 변수라는 뜻으로 사용되며 다른 파일에 같은 이름의 전역변수가 존재한다는 것이기 때문에 이 정보를 컴파일러에 전달하고 main.o 와 header.o 를 링킹해준다.
    - 진정한 global 변수라고 볼 수 있겠다.
    - extern 키워드는 전역변수가 외부에 있다는 것을 표시만 할 뿐이고 선언을 하지는않는다. 따라서 어디엔가 정의되어있지 않으면 링크에러가 발생할 것이다.
    - extern 은 다른 파일의 함수나 변수를 가져오는것 이외에도 현재 파일내에서도 작동이 가능하다.
        - 현재 파일에서 작성중인 코드에, 현재 스코프까지 사용하고자하는 함수가 없는 경우 extern 키워드를 적절히 사용하면 된다.
            ```cpp
            extern int myFunction();
            int main(){
            	myFunction();
            }
            int myFunction(){
            	//do something
            }
            ```
        - 즉 이런 식으로 사용이 가능하다. myFunction 이 어디엔가 정의되어있다는 것을 알려주는 키워드이다.

메모리 상에서의 자세한 이해는 다음 사이트 참고는 [여기](http://chfhrqnfrhc.tistory.com/entry/%EC%A0%84%EC%97%AD%EB%B3%80%EC%88%98%EC%99%80-%EC%A0%84%EC%A0%81%EB%B3%80%EC%88%98) 에 있다.