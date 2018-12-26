---
layout: post
title: "Modern OpenGL이란?"
date: 2018-12-25 01:00:00
author: luis lee
categories: OpenGL
---

# Modern OpenGL 이란?

본 포스팅은 [이 사이트](http://www.davidbishop.org/oglmeta)을 참고했다.
목적은 기존의 전통적인 방식의 OpenGL 과 현재의 OpenGL 의 차이점이 무엇이며, 어떤 방식으로 공부를 해
나아가야하는지에 대한 설명이다. 앞으로 OpenGL 스터디 노트를 작성해 나아가는데 어떤 목차로 나아갈지에 대한 내용이
담길 예정이다.

## Introduction

OpenGL 3.xx 이후의 배포에서부터 많은 함수나 기능들이 삭제되고 제거되었다.
필자는 과거 OpenGL 2.xx 의 코딩 컨벤션과 함수들에 익숙했었다. 하지만 OpenGL 3/4 의 방식도
익히고 프로젝트를 진행해본 경험이 있다. 최신 OpenGL을 스터디함에 있어서 첫 시작으로 Modern OpenGL 이란 무엇인가에 대해
다루는것이 좋다고 판단하였다.

## Old vs. 'Modern'

어떤것이 'old' OpenGL 인지 알아보도록하자. 그리고 어떤것들을 기피해야하는지, 그리고 왜 그런지를 알아보도록 하자.

deprecate 된 함수들을 알아보자.

Deprecated functions :

- glBegin(...)
- glEnd()
- glCallList(...)
- glColor\*
- glMaterial\*
- glVertexPointer
- glBegin과 glEnd 사이에 위치할 수 있었던 함수들

  - glVertex\*
  - glNormal\*
  - glTexCoord\*
  - glMultiTexCoord\*

- 모든 matrix operation

  - glRotate\*
  - glTranslate\*
  - glScale\*
  - glMatrixMode()
  - glLoadIdentity()
  - glPushMatrix()
  - glPopMatrix()
  - glFrustum()
  - gluPerspective(...)
  - gluLookAt(..)

물론 위의 함수들이 deprecated 되어있지만 새로운 버젼의 opengl 에서도 compatibility profile 이 켜져있다면
사용이 가능하다. 그러므로 위의 함수들을 사용하지 못하는것은 아니다. 하지만

- webGL.
- OpenGL ES 2.0 or 3.0.
- OpenGL core profile 을 사용하고 싶은 경우.
- 효과적으로 프로그래밍 하고 싶은 경우.
- 다양한 Shader 를 사용하고 싶은 경우.

에는 새로운 modern OpenGL 을 익힐 필요가 있다.

위의 함수들이 사라진 이유는 비효율성에 있다.
우선 glBegin 과 glEnd 의 경우에는 랜더할 물체의 데이터를 매번 CPU 에서 GPU 로 넘겨줘야한다.
익히 알겠지만 CPU - GPU 간의 통신은 매우 비싼 작업이며 굳이 매번 통신을 할 필요가 없는 경우가 굉장히 많다.
따라서 modern openGL 에서는 이를 삭제시켰다.
또한 이는 병렬화처리가 안되기 때문이라고 들었다.
그리고 모든 matrix operation 을 삭제한 이유도 다른 3rd party 라이브러리를 통해서 관리하는것을 추천하고 있다.
OpenGL 자체에서 처리하는 것 보다 효율적인 측면이 있으며 OpenGL 의 존재이유가 matrix operation 과는 거리가 멀다고 할 수 있다.

## Methods of learning

[이 사이트](http://www.davidbishop.org/oglmeta)에서 추천하는 방식은 총 3가지가 있다. 필자는 처음이 아니기고 본 포스팅의 목적은
modern OpenGL 의 공부이기 때문에 두번째 방법을 선택하였다. 이는 한번에 새롭게 추가된 것에대해 배우는 것이다.
물론 이 방식은 첫 시작에는 좋지 않다. 간단한 quad mesh 를 랜더링하는것 조차 많은 배경지식이 필요하게 된다. 하지만 필자는 이전에 배웠던 내용을 복기하는 식으로 포스팅을 작성할 목적이기 때문에 이 방식을 택하여 진행하고자 한다.

## What exactly is 'modern' OpenGL

'modern OpenGL'을 대략적으로 3개의 정의로 나누어보면 다음과 같다.

The programmable shader pipeline (old Fixed Function Pipeline).

사실 shader pipeline 은 엄연히 말하자면 modern 은 아니다. 실제로 2004년 OpenGL 2.0 부터 사용이 가능했다.
이는 OpenGL 3.xx 이상부터 'core profile'을 사용하기 위해서 사용되야한다.
여기에는 두가지 주요 성분들이 있다.
vertex 정보를 담는 컨테이너(vertex buffer objects) 와 GLSL shader 이다.

이는 유저의 vertex data 를 vertex buffer object 에 집어 넣고, 이 데이터를 그래픽카드에 업로드 시킨다.
그 후 OpenGL 로 하여금 이를 그리라고 말해주는 것으로 이루어진다. OpenGL 이 draw call 을 받게되면 유저의 데이터와
유저의 shader program을 실행시킨다. 유저의 shader 프로그램은 데이터를 이용하여 screen 에 픽셀으로 뿌려주게 된다.

새로운 것들이 OpenGL 3.xx & 4.xx 에 추가되었다. 첫번째로 프로그래머가 조금더 쉽고 효과적으로 코딩하는게 도움을 주는것들이 있다.
새로 추가된 것 중 몇몇은 extension에서 지원하던 것들이 있다. 그리고 아주 새로운 것들 또한 추가되었는데 이는 다음과 같다.

- FrameBuffer Ojbects (OpenGL 3.0)
  - 어떤 surface 를 랜더할지에 대한 buffer
  - 직접적으로 유저의 윈도우에 뿌려주는것과 다름.
- Uniform Buffer Objects (OpenGL 3.1)
  - graphic card 에 한꺼번에 업로드할 수 있는 장점이있다.
  - shader 끼리 공유할 수 있는 버퍼의 종류.
  - 보통 matrix 같이 모든 shader 에서 공통으로 사용하는 경우 사용.
  - 모든 shader 간에 공유가 되기 때문에 모든 shader 에 각각 넘겨줄 필요가 없음.
- GLSL subroutines (OpenGL 4.0)
  - 이 부분은 잘 모르는 내용이기 때문에 자세히 적지는 않겠다.
- Tessellation shader (OpenGL 4.0)
  - Level of Detail (LOD)
- Geometry shaders (OpenGL 3.2)
- Transform Feedback (OpenGL 3.0, more in 4.0 and 4.3)
  - tesselation 과 geometry shader 의 결과로 나오는 fine 한 결과를 매 프레임마다 계산해야됬다.
  - 그런데 tes/geo shader의 output 결과를 저장해 놓는 기능(Transform feedback)을 통해 한번만 tes/geo shader를 사용하게 끔 만듬.
  - 이 부분또한 자세히 모르는 부분이기 때문에 추후 공부예정.
- Less binding
  - > GL_ARB_separate_shader_objects gives you the option of using glProgramUniform* instead of glUniform*. The second one requires you to bind a shader program in advance, the first allows you to specify the id of the shader program in the function letting you skip that step. This saves a function call and makes object orientated programming much nicer. Hopefully in future we will see a full Direct State Access (DSA) extension, t's been one of the most wanted features in OpenGL for a while now however it's been passed over quite a few times. There is an extension that has been around for a while and is apparently well supported on nVidia and ATI. It's not likely to end up in core as it has lots of stuff for deprecated functions. There is also NV_bindless_texture, but nVidia have apparently patented it. (see also NV_GL_shader_buffer_load)
- Improved debugging. (OpenGL 4.3).
  - 새로운 디버깅 extension 이 추가됨.

위의 다양한 feature 들이 새로운 OpenGL 에 추가되었으며 이에 따라 OpenGL pipeline 이 변화되었다고 할 수 있다.
새로운 OpenGL 의 기술을 익히는 것을 통해 자신의 프로젝트를 길게 바라보고 작성할 수 있으며 새로운 버젼이 나왔을 때 손쉽게 적용할 수 있을 것이다.
참조한 블로그에 따르면 XBox 에서 linux 기반의 Steam Box 를 release 하고 있으며 Valve 와 Nvidia 의 말에 따르면 새로운 프로젝트를 OpenGL 을 기반으로 하기를 추천한다고 얘기하고 있다. 따라서 new openGL 을 익히는 것은 매우 중요하다고 할 수 있다.

물론 논외로 Mac 은 현재 OpenGL support 를 중단한다고 얘기하고 있고 OpenGL version 은 하드웨어에 종속적이라고 할 수 있다.
그래서 실제 game 개발 같은 부분에서는 사용자의 graphic card 성능, openGL version을 맞추기 위해 3.xx 대를 사용하는 것이 보편적이라고 할 수 있으나 미래 지향적으로 바라보자면 당연히 새로운 기술을 받아들이고 공부해야한다고 생각한다.

---

지금까지 'Old OpenGL' 과 'Modern OpenGL' 의 차이에 대해서 간략하게 알아보았다.
대부분의 내용은 위에서 언급한 블로그의 포스팅을 기반으로 번역을 하였고 개인적인 스터디를 위함이기 때문에 나중에 깊이 공부할 부분에 대한 노트형식으로 적어놓았다. 앞으로 본 블로그를 통해 필자가 개인적으로 공부하고 실습하는 내용에 대해 포스팅을 진행할 예정이다.

## 끝
