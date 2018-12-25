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

There are roughly 3 definitions I can think of for 'modern OpenGL':

The programmable shader pipeline (as opposed to the old Fixed Function Pipeline).

The shader pipeline isn't really all that modern. In fact it has been possible since 2004 when OpenGL 2.0 was released.

This is the stuff that newer OpenGL 3.x+ requires if you want to support the 'core profile' and don't want to rely on the compatibility profile. It's stuff that people should have been doing for quite a while but for the most part people where still taught the old way as it was generally easier. If you searched for "OpenGL tutorial" a while back that's what came up. The official OpenGL Programming Guide (the Red book) taught that all the way to 7th edition even after it became deprecated. There is now a newer 8th edition.

There are 2 major components. Containers for storing arrays of vertex information (normally Vertex Buffer Objects) and GLSL shaders.

It works by putting all your vertex data into an array data structure, uploading the data onto the graphics card in a buffer and telling OpenGL to draw it all at once. Once OpenGL receives the draw call it runs that data through your shader programs which process the data and turn it into pixels to put on the screen.

The new stuff that was added to OpenGL 3.x and 4.x. Firstly there are a whole heap of nice things that make stuff much easier on the programmer or more efficient. Much of it could have been done other ways with more difficulty or overhead. Some of it has been around for quite a while in extensions. Other things involve some entirely new stuff. It includes things like:

FrameBuffer Objects (OpenGL 3.0). Let you choose a 'surface' on which to to render the pixels rather than just directly to your window.
Uniform Buffer Objects (OpenGL 3.1). Lets you put all your shader uniform variables into one block. As they are buffers these can be uploaded to the card all at once. They can also be shared between shaders so commonly used uniforms such as matrices can be uploaded only once rather than once to every shader. They do require a little more work.
GLSL Subroutines (OpenGL 4.0). Similar to having function pointers. Lets you swap out a function in a shader with another. Better than using if/elseif/else as shaders are run in bulk, all conditional branches in a shader will execute rather than just the true branch, meaning quite a bit of overhead (often it's actually better to just add the results and multiply by 0.0 or 1.0 to enabled and disable features than use if, the subroutines provides an alternative solution but only works on all shaders rather than specific vertices or pixels).
Tessellation shaders (OpenGL 4.0). Most notably can be used for level of detail (LOD).
Geometry shaders (OpenGL 3.2). Geometry shaders are a bit less notable compared to Tessellation ones but have their uses. Can be used for things like point sprites.
Transform Feedback. (OpenGL 3.0, more in 4.0 and 4.3). Geometry and Tessellation shaders allow you to take work off the CPU and thanks to the parallel processing power of modern GPUs do it much faster and don't require you to upload as much data to the GPU. But by themselves they have to be run every frame giving you an overall decrease in performance. However using Transform feedback you can capture the output and put it into a buffer meaning you only have to run the tessellation/geometry shaders once.
Less binding. GL_ARB_separate_shader_objects gives you the option of using glProgramUniform* instead of glUniform*. The second one requires you to bind a shader program in advance, the first allows you to specify the id of the shader program in the function letting you skip that step. This saves a function call and makes object orientated programming much nicer. Hopefully in future we will see a full Direct State Access (DSA) extension, t's been one of the most wanted features in OpenGL for a while now however it's been passed over quite a few times. There is an extension that has been around for a while and is apparently well supported on nVidia and ATI. It's not likely to end up in core as it has lots of stuff for deprecated functions. There is also NV_bindless_texture, but nVidia have apparently patented it. (see also NV_GL_shader_buffer_load
Improved debugging. (OpenGL 4.3). A new debugging extension (KHR_debug) was added making debugging OpenGL much easier. Adds in things like debug callbacks rather then having to continuously poll for errors. It's Probably useful for development even if you plan on only using an older, more compatible set of extensions. Just turn it off on the release build.
OpenGL ES compatibility. Thanks to some recent core extensions, OpenGL 4.1 is a superset of OpenGL ES 2.0 and OpenGL 4.3 is one for OpenGL ES 3.0, this means you can write an OpenGL ES program and have it run on the desktop with little porting. Of course as OpenGL ES 2.0 and 3.0 are subsets of desktop OpenGL if you write a OpenGL ES 2.0 program you will be fairly desktop OpenGL compatible with a little work. But there are some things like shader precision that you will need to take into account. Unfortunately ES 2.0 compatibility was only added recently to OpenGL 4.1 making it hard to support legacy desktop systems, but it should be useful for development.
If your just learning for your own sake or you are able to guarantee/require support for the new OpenGL versions you should take advantage of the new functionality.
If you expect your project to take a long time and think you will see enough adoption of the new version of OpenGL by the time your finished.
Maybe in the future if your developing for a Next-Gen console. Current generation consoles have their own graphics libraries, that was because they where developed befoure there was a OpenGL core. They are similar to a stripped down OpenGL in the same way OpenGL ES is. Now there is OpenGL core maybe we will see that used to save developers work. Then again they might want the vendor lock in, legacy support and ability to keep the same coding style. (This doesn't apply to XBox, being Microsoft they use DirectX). Valve are also releasing a Steam Box that is based on Linux that is likely to support newer versions of OpenGL. In a talk given by Valve and nVidia they recommend using OpenGL for new projects as it works everywhere.
It should even be possible to use OpenGL ES on XBox thanks to Google's Project ANGLE.
Unfortunately quite a few systems don't yet support the newer OpenGL versions. You will have to decide if you need to support those systems.

For example OSX only supports core OpenGL 3.2, and only lists the core profile (and some extensions) as of OSX 10.8/10.7.4 also mixed legacy support for OpenGL 2.1/2.0 and even as far back as OpenGL 1.4 for 10.7.5 on some hardware). Apple appear to specify what OpenGL versions are supported based on the OS version rather than doing it through driver updates so people buying the latest Macs today won't see anything beyond 3.2 unless they fork over money for a full system update. So if you want to support Apple your going to have to stick with Older versions only.

Older netbooks are unlikely to support OpenGL 3. Newer Intel drivers are currently only just at 3.x depending on the OS (some are 3.3, but Linux support lags behind at 3.0 currently). Intel do seem to be making rapid progress in the area and have made quite a few opensource releases.

However some chipsets are unlikely to ever see support due to the hardware being licensed from a 3rd party. Intel's GMA500 (poulsbo) for example is based on Imagination Technologies' PowerVR's SGX535 and has notoriously bad Linux support, 3 differnt actual drivers most of which are closed (psb & EMGD) and only work with specific versions of Xorg and the Linux kernel, have poor performance, require specific settings for different platforms using the same chipset, require patched video acceleration libs for things like mplayer, and don't support many features (rotation on EMGD). There is an opensource one (gma500_gfx) in the kernel but it's 2D only). More anoyingly it is apparently only due to licensing issues, there where quite a few rumors of new better drivers (and even opensource ones) and even a video being released showing what is apparently a fully working Gallim 3D based driver running at a good FPS. This was back around 2009 and none of which ever came to anything. There is a 'high priority'FSF project to reverse engineer the PowerVR chipset but it seems to be close to dead (although there is some recent activity it's unlikely to see any real progress in the time period that the chipset's will remain relevant in).

OpenGL ES 2.0 of course doesn't support the new features, although it does benefit from the legacy compatibility features present in GL_ARB_ES2_compatibility. As that extention only shipped in OpenGL 4.1 it means you can support newer desktop systems and older embedded systems, but not older desktop systems with the same codebase. It does make OpenGL ES development easier though. OpenGL ES 3.0 supports some new stuff but it was only released recently and so there is very little hardware support. Nothing like Geometry/Tessellation Shaders without using an extension. Similarly there is a compatibility extension (GL_ARB_ES3_compatibilityfor OpenGL 4.3 (which was released at the same time as OpenGL ES 3.0).

WebGL doesn't support it either as it's only against OpenGL ES 2.0. Perhaps a newer WebGL will be released in future against the OpenGL ES 3.0 spec. It is possible to enable full desktop OpenGL in WebGL but requires tweaking.

Support is of course hardware dependant. OpenGL 4.x requires Shader Model 4.0 support and 3.x requires Shader Model 3. There are features that could be supported on older hardware, they are often exposed through extensions but you can't guarantee it and some things will never work (at least without a total software fallback, which if combined with transform feedback does make geometry/tessellation shaders a possibility, but seeing drivers support that kind of thing isn't too likely).

The Steam Hardware survey does show 94.23% coverage for combined DX10/11 most of which should support OpenGL 3.0+ (although that is driver dependant). This is only representative of Steam users though but for some game developers that is their main platform. Steam are also releasing their own Linux based console.

Depending on what your building you may be able to just require whatever OpenGL version you want. Less doable for mass market but possible if your developing some specialized niche software on a contract (maybe imaging medical data, making simulators for the military, etc...). You could always charge more for legacy support due to the extra development overhead.

Much of this stuff can be worked around by providing alternative ways to support the same functionality but that's going to make more work for you so it will only be useful if you need to added efficiency of something specific but not for the syntactical candy. If your doing enough of it you could look at making a library the provides transparent fallbacks for legacy systems some simple things like providing fallback functions so if glProgramUniform isn't supported then it will use the older glUniform functions after calling glProgramUse. It might even be possible to provide support for more advanced things like geometry/tessellation shaders by using a software renderer like Mesa provided the performance is good enough (not sure if Mesa support tessellation yet, but does Geometry seems to be there).

The ultra modern next-gen stuff.

OpenGL 4.3 added compute shaders and storage buffers. These can apparently be combined to allow you to use compute shaders on the graphics card for programmable vertex pulling.

This is fairly new so I don't have any learning resources for that. Have a look at the OpenGL 4.3 review for a better description, also according to that there is a GPU Pro article in the works.

Realtime global illumination. Get raytraced like graphics.

Voxel rendering.

There are likely other nifty things such as culling objects on the card and so on.

Your also going to be further limiting your supported platforms beyond the 3.x stuff, although it shouldn't be as bad. OpenGL 2.x was around for quite a while. However 3.0 went to 4.3 fairly quickly, 7 versions in 4 years. OSX is the main problem here.

Resources
The Official Specifications - Gives you the complete guide to everything.

The official OpenGL man pages - A nice big list of functions and their documentation.

opengl-tutorial.org - I mainly recommend this one as it breaks up things into small bits and doesn't use deprecated functionality it's also simple to understand. OpenGL 3.3+. C++. GLM, Cmake, Windows/Linux/Mac,
ogldev - Seems fairly complete and doesn't seem to use use deprecated stuff. Mostly OpenGL 3.3 and some 4.x later for tessellation. C++
open.gl - Modern tutorials, still under development. C++/GLM.
OpenGLBook.com - Only a few beginning tutorials but they do following the bit by bit method of learning. Good long section of OpenGL history in the Preface. C.
Learning Modern 3D Graphics Programming (arcsynthesis) - Probably the one most commonly recommended by people. This is a bit harder that the one above but more in-depth. Also check out the source code repositry. Been around a bit longer. C++
wikibooks - The modern OpenGL wikibooks tutorial. If something is lacking, add it! :)
wikibooks - GLSL Programming - Specific for shaders.

Lazy Foo - I don't recommend using this as an overall tutorial as it starts of using OpenGL 2.1 until lesson 29 when it introduces OpenGL 3.0 stuff. Some of the later tutorials may be useful for specific concepts.

NeHe - It should probably be avoided as it is almost completely based on deprecated functionality. They have classified the tutorials under 'legacy' now. Worth knowing about as for quite a while this was the best source for OpenGL tutorials and it's still the first result returned for a search of "OpenGL Tutorials". There will be some parts that are useful for modern techniques such as VBOs.

G-Trac OpenGL reviews - These are a great way to learn all the new features of OpenGL 3.x/4.x and how they fit it. Written by the GLM author.
G-Trac OpenGL Samples - Sample code.
Udacity Interactive Rendering course - Free, online video lectures. Problemsets and stuff all done in the browser. JavaScript with WebGL.
Porting Source to Linux - An interesting talk given by a Valve employee about porting from DirectX to OpenGL using a wrapper layer. Interestingly enough the final version is %20 than the native DirectX one.
ANGLE - An OpenGL ES 2.0 to DirectX 9 wrapping layer.

## 끝
