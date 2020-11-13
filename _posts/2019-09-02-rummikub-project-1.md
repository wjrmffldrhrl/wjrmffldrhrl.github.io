---
title:  "루미큐브 프로젝트 -1-"
categories:
  - Project
tags: 
  - Rummikub
  
header:
  image: /assets/images/rummikub_title.png
  teaser: /assets/images/rummikub_title.png
---  

# 주제선정  

군생활을 마치고 대학교 복학 후 열심히 해야된다는 생각으로 똘똘 뭉쳤을 그때  
전공 프로젝트 설계 과목의 주제로 보드게임중 하나인 루미큐브를 만들어 보기로 했다.  
~~그 당시 주제 선정에도 많은 시간이 걸렸다.~~  

![rum1]({{ site.baseurl }}/assets/images/rummikub_1/rummikub1.jpg)  


주제를 루미큐브로 선정하고 제작에 들어가기 전 블록도 & 순서도를 구상하는데  
**보드게임은 여러명이서 하지않나?** 라는 생각이 들어 소켓 통신을 적용하여  
멀티플레이 환경을 구성해보기로 했다.  
  
# 시장조사  
조원들 중 절반 이상은 루미큐브의 규칙을 완벽하게 숙지하지는 못하고 있었고  
제작중에 햇갈리는 일이 없으면 하는 마음에 다같이 보드게임방에 가서 직접 플레이 해봤다.  

![rum1]({{ site.baseurl }}/assets/images/rummikub_1/rummikub_rule.jpg)  
_직접 촬영한 루미큐브 설명서_  

우리가 숙지한 루미큐브 규칙은 다음과 같다.
~~출처 : 킹무위키~~
> 여기서는 루미큐브의 설명서에 적힌 Sabra 룰을 바탕으로 기록되었습니다.  
American 룰은 Sabra 룰과 같으나 12-13-1로도 할 수 있다는 차이점이 있다.  
루미큐브는 1부터 13까지 숫자가 적혀있는 4가지 색깔의 패 두 벌과 조커 2개 (총 106개)로 구성되어 있다.  
게임을 시작할 때 14장씩 패를 나누어 가져 시작하며, 족보에 맞는 순서대로 패를 테이블 중앙에 내거나  
혹은 패를 하나 가져오거나 하는 식으로 순서를 진행하여 제일 먼저 자신의 패를 모두 내려놓는 사람이 이기는 게임이다.  

>기본적으로 족보는 최소한 3개 이상의 루미큐브 패로 이루어져야 하며 족보는 같은 색깔로 연속되는 숫자/똑같은 숫자이되 서로 색깔이 달라야 한다.  

>올바른 예 = 1, 2, 3 / 5, 5, 5  
잘못된 예 = 1, 2, '3' / 5, '5', 5  
쉽게 말하면 포커의 스트레이트 플러시나 트리플, 포카드와 비슷하다고 볼 수 있다.  

>맨 처음에 패를 내려놓을 때는 "등록"이라고 해서 족보(들)를 이루는 패의 수의 합이 30 이상이어야 한다.  
(예를 들면 "10, 11, 12(총합 33)"나 "10, 10, 10(총합 30)"으로 이루어진 족보처럼) 등록은 반드시 자신의 패로만 해야 한다.  
일단 등록이 끝나면 이미 이루어진 족보가 깨지지 않게 패를 가져와서 내 족보로 이룰 수도 있다.  

여기서 우리가 눈여겨본 규칙들은 
1. 106개의 1부터 13까지 숫자가 적혀있는 4가지 색깔의 패 두벌  
2. 조커 2개  
3. 족보(같은색으로 연속되는 숫자 or 똑같은 숫자 다른 색깔)
4. 등록  

이렇게 4가지로 이부분에 중점을 두기로 했다.  

# 기능적 요구사항 명세서  
[**기능적 요구사항 명세서**](https://medium.com/@mklab.co/%EC%9E%91%EC%84%B1%EB%B2%95-%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD-%EB%AA%85%EC%84%B8%EC%84%9C-requirements-specification-ad3533d6d5b8)란?
>서비스를 구현하기 위해 다양한 요구사항이 거론되는데, 이를 명확하게 정리해야 할 필요가 있습니다.  
>요구사항 명세서는 요구사항을 분석하여 명확하고 완전하게 기록하는 것을 말합니다.  
>소프트웨어 시스템이 수행해야 할 모든 기능과 구현상의 제약 조건에 대해 개발자와 관련자  
>(클라이언트, 기획자, 경영진 등)가 합의한 스펙에 대한 사항을 명세합니다.  

당시 우리가 고려했던 요구사항은 다음과 같다.  

![table]({{ site.baseurl }}/assets/images/rummikub_1/table.JPG)  

요구사항 명세서의 내용은 모두 구현하겠다는 생각으로 프로그램 제작에 들어갔다.