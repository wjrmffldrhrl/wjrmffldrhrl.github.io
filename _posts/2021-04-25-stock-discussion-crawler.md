---
title:  "종목 토론방 크롤러"
categories:
  - Stock Analysis
tags:
  - Crawler
  - Stock
header:
  teaser: /assets/images/stock_discussion_crawler/1.png
  image: /assets/images/stock_discussion_crawler/1.png
---  

# 종목 토론방
주식 종목 토론방에는 정말 다양한 사람들이 몰려온다.  

![1]({{ site.baseurl }}/assets/images/stock_discussion_crawler/1.png) 

분노에 가득 찬 사람, 조롱하는 사람, 사기꾼, 광고인, 예언자, 욕쟁이 등등...

### 분노에 가득 찬 사람
![3]({{ site.baseurl }}/assets/images/stock_discussion_crawler/3.png) 

### 예언자
![4]({{ site.baseurl }}/assets/images/stock_discussion_crawler/4.png)  

이걸 지켜보는게 은근히 재밌어서 종종 구경하러 간다.  

주식 장이 열리는 시간, 대략 9시부터 3시까지 글이 올라오는 양이 상당히 많아진다. 또한 주식 가격이 변동되는 만큼 사람들의 감정 상태도 계속 변한다.  
흥미로운 점은 가격이 올라도 분노한 사람들이 있고, 가격이 내려가도 기뻐하는 사람들이 있다.  

이렇게 계속해서 생기는 대량의 데이터를 잘 활용한다면 재밌는 지표들 몇 가지를 만들 수 있을 것 같다.  


# Crawler

데이터를 이용해서 무언가 만들기 전 이러한 데이터들을 수집할 방법이 필요했고, 네이버에서 API를 따로 제공해주지 않기 때문에 크롤러를 만들어봤다.  

[Stock Discussion Crawler](https://github.com/wjrmffldrhrl/stock_discussion_crawler)  

코틀린으로 제작된 이 크롤러는 원하는 종목을 종목코드 리스트로 작성하면 해당 종목토론방에서 생성되는 글을 실시간으로 크롤링해 `YYYY_MM_DD.csv`파일 형태로 생성한다.  

### CSV
```csv
date,url,title,content
2021.04.25T13:20,https://finance.naver.com/item/board_read.nhn?code=005930&nid=173153368&st=&sw=&page=1,남자는 사병복무 여자는 출산,한국군 이등병으로 복무 안해본 남자와 한국 인의 아이를 출산을 하지 않은 여자는 이나라의 기생충이고 쓰레기임. 싫어도 해야 나라가 유지되고 존속되니 해야하는게 저것 남자애들이 싫다고 장교 부사관만 하고 이등병 사병안하면 나라는 망하듯이.. 여자애들이 싫다고 손해라고 애 안낳으면 나라는 망함.. 실건 좋건의 문제가 아니라 어느나라든 국가가 존속하기 위해서는 싫어도 반드시 해야할 일이 저 두가지. 여자는 한국인의 아이를 싫어도 출산해야할 의무가 잇는거고 남자는 한국군의 사병으로 싫어도 징집되어야할 의무가 있는것. 40전에 한국인의 애를 하나도 안낳은 여자는 그냥 이니라의 기생충 쓰레기일 뿐임
2021.04.25T13:21,https://finance.naver.com/item/board_read.nhn?code=005930&nid=173153396&st=&sw=&page=1,어영부영....공매도시작...,공매도 없어도 오르고 내리고 잘 하는데  굳이??
2021.04.25T13:23,https://finance.naver.com/item/board_read.nhn?code=005930&nid=173153440&st=&sw=&page=1,다음주 증시가 살짝 기대되기는 하네 ㅋㅋ,비트코인은 사실상 양털깍기 들어갔고 손턴사람들은 슬슬 주식시장으로 다시 들어오면 살짝 다음주 증시가 기대되기는 하네 ㅋㅋ
2021.04.25T13:23,https://finance.naver.com/item/board_read.nhn?code=005930&nid=173153449&st=&sw=&page=1,▶▷ 급등주 리딩방 추천 ◁◀,https://bit.ly/3vgVP5R 1000 -> 4700 ^^
```
*상당히 반 사회적인 사람들도 많다.*  

아무래도 데이터를 보기도 편하고 다루기 쉬운 형태가 csv라고 생각해 해당 형태로 결정했다.  
# Structure
![2]({{ site.baseurl }}/assets/images/stock_discussion_crawler/2.png) 

크롤러의 대략적인 구조는 위와 같다.  

크롤링과 csv파일 생성을 담당하는 역할을 나눠 의존성을 낮추고 파일 생성이 아닌 다른 처리 방법을 적용할 수 있도록 했다.  

이전에는 구글 드라이브에 업데이트 하는 형태로 작성했었지만 지금은 제거된 상태이다.  



# With Docker  
![docker]({{ site.baseurl }}/assets/images/stock_discussion_crawler/docker-logo.png) 

Repository 내부에 docker에서 실행시킬 수 있도록 스크립트 파일과 Dockerfile도 작성해두었다.  

다만, mac os 환경에서 동작하게 쉘을 작성해서 window 환경이라면 빌드된 `.jar`파일을 직접 실행하는게 좋을 것이다.  

이후에는 실시간으로 생성되는 이런 스트리밍 형태의 데이터를 elk와 연결하여 모니터링 하는 환경도 구축 할 것이다.
