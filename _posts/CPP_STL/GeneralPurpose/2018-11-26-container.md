---
layout: post
title: "STL Sequence 컨테이너 [array,list,forward-list,vector,deque]"
date: 2018-11-26 00:00:00
author: luis lee
categories: c/c++뽀개기
tag: c/c++ 뽀개기
---

# c++ 순차 컨테이너

c++ stl의 컨테이너 중에서 순차 컨테이너에 대해 알아보자.<br>
순차 컨테이너의 사용법은 개인이 알아서 익히면 될것같다. 사용하는데에는 큰 어려움은 없을것이지만 이 포스팅에서는
어떠한 경우에 어떤 컨테이너를 쓰면 좋은지에 대해서 간략하게 다루도록 하겠다.<br>
순차 컨테이너는 원소들을 선형적인 순차열으로 저장하는 컨테이너 이다.
여기서 선형적이란건 정렬하지 않는다는 얘기이다. 총 5개의 STL container 가 이에 속하며

- array<T,N>
- vector<T>
- deque<T>
- list<T>
- forward_list<T>

가 있다.

## array<T,N>

## vector<T>

push_back을 하면 capacity 가 늘어나는데 log 로 늘어남.
이 오버헤드를 줄이는법
reserve() 와 resize 의 차이점.

## deque<T>

## list<T>

## forward_list<T>
