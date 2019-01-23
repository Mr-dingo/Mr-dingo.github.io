---
layout: post
title: "VAO & VBO in OpenGL 4.3"
date: 2019-01-02 00:00:00
author: luis lee
categories: OpenGL
---

<!-- TOC -->

- [Welcome to OpenGL 4.3](#welcome-to-opengl-43)
- [VAO 와 VBO](#vao-와-vbo)
  - [VBO 생성](#vbo-생성)
  - [VAO & VBO 사용법](#vao--vbo-사용법)
  - [Draw VAO](#draw-vao)
- [끝](#끝)

<!-- /TOC -->

# Welcome to OpenGL 4.3

본 포스팅은 필자가 공부한 바를 바탕으로 재구성한 것이며 틀린부분이 있을수 있습니다.

- OpenGL 2.x
  - 2.0, 2.1 (2004~2006)
  - 여기부터 programmable shader 등장
- OpenGL 3.x
  - 3.0~3.3 (2008~2010)
  - 많은 feature 를 deprecated 했다. fixed pipeline 의 것들을.
  - Geometry shader 등장.
- OpenGL 4.3
  - 4.0~4.6 (2010~2018)
  - tesselation shader 등장
  - compute shader (atomic operation) 등장

일단 기존의 lagacy code 스타일 처럼 매트릭스 스택을 사용하거나 fixed render pipeline 을 사용하는 것이 불가능하다.(compatitibilty 때문에 가능은하지만 안하는것을 추천)

또한 OpenGL 에서 Profile 이라는 개념이 등장하면서 deprecated 된 함수를 쓸지말지 결정하는 방법이 있는데 가장 최신의 기능을 사용하고 호환성문제를 위해서는 새로 프로그램을 작성하는 중이라면 core profile 을 사용하는 것이 맞다고 생각한다.

먼저 기존의 lagacy code 에 익숙한 사람이라면 두가지 개념에 대해서 익혀야 할 것이다.

1. Buffer objects
   1. glBegin,glEnd 가 제거됨에 따라 사용해야할 것들.
   2. VAO,VBO 등...
2. Programmable shader
   1. fixed pipeline 이 제거됨에 따라 사용해야할 것들.
   2. Program vertex,fragment shaders(원한다면 geometry,tesselation shader까지)

# VAO 와 VBO

이번 챕터에서는 Buffer Object 중 VAO 와 VBO 에 대해서 다루겠다.

이 두가지 개념에 대한 설명은 다음과 같다.

VBO : buffer for vertex data

- Position,Normal,Vector,Color, etc.
- 즉 vertex data 를 표현하는 데이터를 직접적으로 저장하는 공간
- VBO 에는 vertex 를 표현하는 데이터가 들어간다. 포지션 뿐만아니라 다양한 부차적인 데이터를 저장할 수 있다.

VAO : Special type of object that encapsulates all the vertex data

- it holds references to the vertex buffers, index buffer
- 즉 vertex buffer 와 index buffer 의 래퍼런스를 저장하고 있는 공간.
- VBO 와 index buffer 를 포인팅해준다고 생각하면 된다.
- 여러개의 VBO 를 가질 수 있지만 Index buffer 는 하나만 가질 수 있다.

이러한 관계를 그림으로 나타내면 다음과 같다.

![]({/assets/Untitled-b4b56243-141e-41e7-9952-324001e3f21c.png)

하나의 VAO 는 element array buffer (index buffer) 하나를 가지고 있으며 16개의 VBO 의 래퍼런스를 담을 수 있는 공간을 가지고 있다. 꼭 16개를 모두사용할 필요는 없으며 각각의 자리에 VBO 가 맵핑된다고 생각하면된다.

Attribute pointer 의 갯수는 하드웨어에 따라 다른데

    glGetIntegerv(GL_MAX_VERTEX_ATTRIBS, &n).

이렇게 확인하면 된다. 그런데 최소 16개 이므로 걱정하지 말자.

---

우리가 VAO & VBO 를 사용하는 이유는 물체(vertex data)를 랜더하기 위함이다. 랜더링을 하기위해서는 GPU로 vertex data 를 보내줘야하며 이를 매 프레임마다 보내주는 것은 굉장히 비효율적이다. 만약에 한 물체가 더이상 움직이지 않는다는 보장이 있다면 매번 vertex data 를 보내는 것은 더욱더 비효율적이다. 따라서 VBO 를 이용해서 GPU 에 버퍼의 내용을 보낸다.

그리고 이러한 래퍼런스 정보를 VAO 에 담아두게되고 현재 바인드 되어있는 VAO 의 VBO 정보를 그리는 것이다.

이제는 VAO 와 VBO를 사용하는 법을 알아보자.

---

## VBO 생성

먼저 VAO 에 바인드할 VBO 를 생성하는 방법을 알아보자.

VBO 를 생성하는 방법은 다음과 같다.

    GLuint buffer;
    glGenBuffers(1,&buffer);
    glBindBuffer(GL_ARRAY_BUFFER,buffer);
    glBufferData(GL_ARRAY_BUFFER,1024*1024,NULL,GL_STATIC_DRAW);
    glBindBuffer(GL_ARRAY_BUFFER,0);

여기서 GL_ARRAY_BUFFER 는 vertex attribute 를 위한 버퍼이지만 아직은 VAO 에 바인드 되지 않았다는 것을 명심해야한다. 이전 2.x 버젼에서는 VAO 가 오로지 1개만 존재하였기 때문에 위의 코드를 통해 바인드가 되었다고하지만 이제는 VAO 를 여러개 선언할 수 있다. 따라서 실제로 VAO 의 state 의 변화는 VAO 를 직접 생성하고 이를 바인드한 상태에서 VBO 를 해당 VAO 에 바인드 시킬 때 일어난다.

    glBufferData(GLenum target,
        GLsizeiptr size,
        const GLvoid* data,
        GLenum usage);

이 코드는 VBO 를 생성하고 그 내부에 data 를 집어넣는 코드이다.

- target 은 Buffer object 타겟을 의미한다.
- size 는 buffer object 에 저장할 데이터의 크기를 바이트 단위로 지정해주어야한다. 만약 저장할 데이터가 integer 10개라면 4\*10 개가 될 것이다.
- data 는 우리가 저장한 데이터의 포인터이다.
- usage 는 주의해서 볼 필요가 있다.
  - 여기에는 GL_STREAM_DRAW, GL_STREAM_READ, GL_STATIC_DRAW,GL_STATIC_READ,GL_DYNAMIC_DRAW 등 우리가 해당 데이터를 어떤 방식으로 처리할 예정인지를 넣는 항목이다.
  - 따라서 우리가 넣을 데이터가 얼마나 자주 바뀔것인지, 랜더하는 용도인지, 단순히 읽히기 위해 저장하는 값인지, 등을 잘 지정해 주어야한다.
  - 이 부분은 나중에 하나의 독립적인 포스팅을 통해 자료를 조사하여 다루도록 하겠다.

그렇다면 VBO 를 생성하는 방법에 대해 익혔기 때문에 VAO 와 함께 VBO 를 어떻게 바인드 시킬것인지 생각해보자.

---

## VAO & VBO 사용법

    static const GLfloat pos[] = {...}
    static const GLfloat color[] = {...}

    GLuint vao;
    glGenVertexArrays(1,&vao);
    glBindVertexArray(vao);
    GLuint buffer[2];
    glGenBuffer(2,buffer);
    glBindBuffer(GL_ARRAY_BUFFER,buffer[0]);
    glBufferData(GL_ARRAY_BUFFER,sizeof(pos),pos,GL_STATIC_DRAW);
    glVertexAttribPointer(0,3,GL_FLOAT,GL_FALSE,0,NULL);
    glEnableVertexAttribArray(0);

    glBindBuffer(GL_ARRAY_BUFFER,buffer[1]);
    glBufferData(GL_ARRAY_BUFFER,sizeof(color),color,GL_STATIC_DRAW);
    glVertexAttribPointer(1,3,GL_FLOAT,GL_FALSE,0,NULL);
    glEnableVertexAttribArray(1);

    //unbind all
    glBindBuffer(GL_ARRAY_BUFFER,0);
    glBindVertexArray(0);

먼저 VAO 를 생성하는 코드는 다음과 같은 문법을 사용했다.

    GLuint vao;
    glGenVertexArrays(1,&vao);
    glBindVertexArray(vao);

그 후 VAO 에 바인딩 할 VBO 를 생성했으며 해당 VBO 를 glVertexAttributePointer 함수를 이용해서 VAO 의 자리에 맵핑시켰다. 그 코드는 다음과 같다.

```c
glVertexAttribPointer(GLuint index,
  GLint size,
  GLenum type,
  GLboolean normalized,
  GLsizei stride,
  const GLvoid* pointer);
```

- index 는 위치시킬 VAO 의 자리이다.
- size 는 하나의 vertex 에 해당하는 데이터의 단위 사이즈이다.
- type 은 데이터의 타입이다. 여기서는 GL_FLOAT.
- normalized 는 해당 데이터를 노멀라이즈 시킬것인지 물어보는것.
- stride 는 tight 하게 저장되어있는 경우는 0 이다. 아닌 경우는 그 다음 데이터는 몇칸 떨어진 위치에 있는지 설정하는 부분이다.
- pointer 는 offset 포지션을 의미한다.

그리고 주의해야할 다음 코드의 뜻을 알아보자.

    glEnableVertexAttribArray(index)

다음 코드의 명확한 뜻을 알기 위해 enable 하는경우와 disable 하는 경우를 생각해보자.

- enable 하는 경우

  glEnableVertexAttribArray(index)
  glVertexAttribPointer(loc, ...);

단지 vertex attribute 를 array type 으로 enable 시키는 것이다. 이를 통해 각각의 vertex 에 array 안의 값을 사용할 수 있게 된다.

- disable 하는 경우

  glDisableVertexAttribArray(index)
  glVertexAttrib4f(loc, colR, colG, colB, colA);

vertex attribute 를 array로 enable 시키지 않았기 때문에 vertex attribute 로 array가 아니라 Vec1~4 중의 하나인 하나의 값으로 설정한다는 뜻이다. 이렇게 되면 모든 vertex 에 대해서 하나의 값을 사용한다는 뜻.

즉 enable 이라는 뜻에 사로잡혀 마치 이 함수가 attribute slot 을 사용하겠다는 것으로 받아드릴 수 있지만 이는 사실과 다르다.

단순히 attribute 를 array 타입으로 활성화시켜서 각각의 array value 를 각각의 vertex 에 전달 할 수 있게 하겠다는 뜻이다.

---

## Draw VAO

이제 생성한 VAO 를 이용해서 직접 랜더링하는 방법에 대해 알아보자.

생성한 VAO 에 들어있는 VBO 를 랜더링 하는 방법에는 크게 두가지 방법이 있다.

- Index 없이 그리기 : glDrawArrays
- Index 를 이용해서 그리기 : glDrawElements

사실 개인적으로 index 없이 그리는 경우가 잘 없기도 하고 그려야할 물체가 복잡하고 클 수록 index 없이 그리는것은 굉장히 비효율적이라고 생각한다. 따라서 index 없이 그리는 것은 넘어가도록 하겠다.

그렇다면 index 를 이용해서 그리는 방법을 생각해보자.

index 를 이용한다고 했는데 지금까지 내용에서 Index 에 대한 내용은 거의 없었다. 앞의 그림에서는 살짝 찾아볼수는 있었지만 자세히 다루지는 않았다.

컴퓨터 그래픽스에서 물체는 보통 점과 선, 그리고 점과 선으로 이루어진 다각형의 집합이라고 할 수 있다. 이러한 물체에 대한 정보를 저장하고 표현하는 방법은 여러가지가 있지만 기본적으로는 다음과 같은 데이터를 저장하여 물체를 표현한다.

- vertex position
- 어떤 vertex 끼리 묶여서 다각형이 되는가에 대한 정보 (index)
- 다각형의 normal
- 등...

만약 직육면체를 그린다고 가정해보자.

![](/assets/Untitled-9b168c5b-4af9-4282-9ce3-aace4d083015.png)

다음 직윤면체에서

- 점 8개 의 정보
- 면 6개
- 4개의 점이 1개의 면을 이룬다. (index)
  - [ㄱ,ㄴ,ㄷ,ㄹ] : 1번 면
  - [ㄱ,ㄴ,ㅁ,ㅂ] : 2번 면
  - ...
  - [ㅂ,ㅁ,ㅇ,ㅅ]: 6번 면

라는 정보를 바탕으로 직육면체를 표현할 수 있다. 여기서 [ㄱ,ㄴ,...,ㅇ] 를 index 라고 부른다.

만약에 직육면체를 면에 대한 정보(index) 를 이용하지 않고 표현한다면 굉장히 비효율적으로 데이터를 저장해야한다.

- 총 6개의 면이 있고
- 각 면은 4개의 vertex 로 이루어져 있으며
- 각 vertex 는 3개의 float 형 위치정보를 가지고 있기 때문에
- 6*4*3\*sizeof(float) 만큼의 데이터를 가지고 있어야한다.

하지만 index 를 이용한다면

- 총 8개의 점의 정보 : 8*3*sizeof(float)
- index 정보 : 총 6개의 면, 각 면은 4개의 index(int) : 6*4*sizeof(float)
- 8*3*sizeof(float) + 6*4*sizeof(float) 만큼의 데이터를 가지면 된다.

물론 이는 직육면체이기 때문에 차이가 심하지는 않지만 vertex 의 갯수가 많아지는 경우에는 훨씬 많은 저장공간의 차이가 발생하게된다.

---

이제 index 를 이용해 VAO 를 그리는 방법을 알아보자.

- glDrawElements(GL_TRIANGLES, 6, GL_UNSIGED_SHORT, index_data);
  - GL_TRIANGLES : draw 할 primitive type 은 삼각형이며
  - 6 : 그려야할 element 의 갯수는 6개, 즉 6개의 삼각형을 그린다.
  - GL_UNSIGED_SHORT : index 의 타입이 unsigned_short 이다.
  - index_data : index 정보를 담은 변수의 포인터

를 이용해서 VAO 를 그린다.

index_data 에 담긴 정보를 바탕으로 삼각형을 그려나가게 된다.

그런데 VAO 에는 상단의 그림에서 보다시피 index pointer 라는 공간이 default 로 하나 마련되어 있다. 실제로 VAO 안에 있는 index pointer 를 이용하여 그리는 방법도 있는데 이를 이용하는 방법은 다음과 같다.

    static const unsigned short index_data[] = {...};


    glBindVertexArray(vao);
    //위에서 생성한 VAO 를 바인딩해준 상태에서 진행한다.
    GLuint indexId;
    glGenBuffers(1,&indexId);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER,indexId);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER,sizeof(index_data),index_data,GL_STATIC_DRAW);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER,0);

    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER,indexId);
    glDrawElements(GL_TRIANGLES,6,GL_UNSIGNED_SHORT,0);

따라서 이는 VAO 에 index pointer 공간에 index 정보를 담은 VBO 를 맵핑시켜주기 때문에 index 정보 또한 GPU 로 보내놓고 사용할 수 있다는 장점이 잇겠다.

# 끝
