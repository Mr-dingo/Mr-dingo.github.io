---
layout: post
title: "OpenGL(vulkan) vs DirectX"
subtitle: "markdown & computer architecture"
date: 2018-11-26 01:00:00
author: luis lee
categories: Graphics
---

# OpenGL vs DirectX

OpenGL , DirectX 에 대한 자료는 굉장히 쉽게 찾아볼 수 있으며 무슨일을 하는지 잘 설명되어있다.
일단 이 두개가 무엇인지는 안다고 가정해보자.
만약 자신이 두개의 graphic libarary 중에서 하나를 선택해야한다면 굉장히 고민이 될 것이다.
이 글에서는 도대체 어떤 graphic library 를 선택해야되는지 알아보자.

## openGL 의 가장 큰 특징은 cross platform 이라는것!

제목에 `OpenGL(vulkan)` 이라고 괄호치고 vulkan 을 적어 놓았는데, 사실 Vulkan 은 openGL 을 관리하고 있는
크로노스 그룹에서 openGL 보다 low-level language 스럽게 내놓은 새로운 Grpahics library라고 한다.
따라서 두개의 pipeline은 거의 동일하다고 한다.(사실 아직 openGL만 해봐서 직접 경험해보고 다시 얘기하겠다)<br>

openGL 과 openGL 의 업그레이드판(?, 정확히는 아니지만)인 Vulkan 은 cross platform 이다.<br>
즉, 각종 OS 에서 동작이 가능하다. Mac,Windows,Linux 등 ...<br>
그렇기 때문에 cross platform 에서 오는 이점을 얻을 수 있다.

## DirectX 은 게임업계에서 인기가 많다.

게임 업계에서는 대부분 DirectX 를 이용한다고 한다. 그 이유는 Vulkan 에 비해서 Nvidia graphic 카드에 최적화가
잘 되어있기 때문이라고 한다. 사실 Vulkan 이 더 low-level 스럽기 때문에 잘만 작성하면 성능이 좋게 나와야된다.
그런데 Nvidia 그래픽카드의 경우 같은 동작을 수행했을때 많게는 50% 만큼 성능의 차이가 나온다고 한다. <br>
그렇게 때문에 게임 특성상 nvidia 그래픽카드가 우세한 점유율을 보이고 있고 게다가 오래전부터 DirectX 로 개발을 해왔기 때문에
굳이 게임 업계에서는 DirectX 에서 Vulkan으로 갈아탈 이유가 없는것이라고 한다.
<br>
<br>
그렇지만 DirectX 는 `Windows` 플랫폼 위에서만 작동한다. ㅠㅠ
그렇기 때문에 자신이 cross platform graphic programming 을 목적으로 하고 있다면 선택의 여지 없이
OpenGL 이나 Vulkan 을 선택해야된다.

## 정리해보면

어쨋든 게임계에선 DirectX 가 강세를 보이는 모양이다. 그 이유는 Nvidia graphic 카드에 Vulkan 이 최적화가
거지같기(까지는아니지만) 때문이고 이미 게임 개발을 위해 DirectX 를 써온 역사가 길기 때문에 굳이 Vulkan 이나 OpenGL로
넘어갈 생각을 안하는 것이다.<br>
따라서 자신이 게임업계에 종사하고 싶고 graphics 라이브러리를 공부하고 싶다면
DirectX 를 선택하면 될 것 같다. 물론 모든 게임이 DirectX 만 지원하는건 아니고 Vulkan, OpenGL 도 쓴다.<br>
하지만 일단 DirectX 가 훨씬 많이 쓰이는 듯 하다.<br>
**따라서**,게임업계에 종사하고싶다 면 DirectX 를 공부하는게 좋다고 생각한다.<br>
<br>
대신에 자신이 게임업계에 종사하고 싶지도 않고 '난 다양한 플랫폼에서 돌릴수 있어야되는게 중요하다고 생각해' 라고 한다면
그리고 혹시나 '모바일 게임에 관심도 많고 모바일에서 엄청난 그래픽을 뽑아내고 싶다!' 라고 한다면 DirectX 대신 OpenGL 이나 Vulkan 을 공부하면된다.

<br><br><br>
우선 필자는 게임업계에 관심은 가지고 있는데 OpenGL을 이미 많이 공부해봤고 GLSL 까지 조금은 익혀놓은 상태이고
cross platform 이 그래도 중요하지 않나 하는 마음에 오늘부터 다시 OpenGL 과 나아가 Vulkan 까지 익히고 개인
프로젝트를 한번 진행해보려 한다.

## 끝
