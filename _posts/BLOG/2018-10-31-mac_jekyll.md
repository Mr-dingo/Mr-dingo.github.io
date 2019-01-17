---
layout: post
title: "Mac OS Jekyll error"
date:   2018-10-31 00:00:00
categories: 블로그관련
author: luis lee
---

# Mac OS jekyll 에러
Mac 에서 현재 사용하던 jekyll 컴파일이 갑자기 안됬었다.<br/>

증상은 따로 스크린샷을 찍어놓지 않았기 때문에 사라졌다.<br/>
일단 잘되던 jekyll 컴파일이 안되었던 이유는 mac os 를 업데이트를 하고나서부터였다. 
인터넷 검색결과 종종 mac os 업데이트 후 ruby가 잘못되는 경우가 있다고하였다. 나와 같은 경우는 bundle install 후 에러 로그를 따라가다 보니
ruby 컴파일 에러가 나왔었고 ruby를 
> brew install ruby

를 통해서 다시 받았고 bundle install 을 진행한 뒤 다시 jekyll을 컴파일 했더니 되었다.

<br/>
혹시 같은 증상으로 고통받고 있는 사람이 있다면 이 글을 보고 시도해보기를 바라는 마음에서 글을 적는다.

