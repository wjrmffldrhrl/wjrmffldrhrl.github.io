---
title:  "우렁 케어 제작기 1"
categories:
  - Project
tags: 
  - IoT
  - UleungCare
header:
  image: /assets/images/UleungCare.png
  teaser: /assets/images/UleungCare.png
---  


교내 과목 중 하나인 통신공학실습 다음 학기부터는 이름이 IoT과목으로 변경되는 만큼 라즈베리파이를 사용하여 뭔가 제작하고 설계하는 과목이다. 그래서 이번에 나는 학교 팀원들과 함께 스마트 홈 케어 서비스를 제작해 보려고 한다.

# 뭘 만드나?  

![UleungCare]({{ site.baseurl }}/assets/images/UleungCare.png)  


**Smart HomeCare Device**  
일명 우렁케어를 제작하려고 한다.  

IoT 기술이 발전하고 있는 지금 기존에 사용하던 제품들을 교체할 수 없고 집 밖에서 집 안 온도를 제어하거나 집 안을 확인하고 싶을때 
우리가 개발중인 이 기기를 사용한다면 비싼 돈을 쓰지 않고 집 안에 IoT 환경을 구축 할 수 있다.  

# 시스템 구성  

![architecture]({{ site.baseurl }}/assets/images/UleungCare_1/architecture.png)  

개략적인 구성은 위와 같다.  
디바이스, 즉 우렁케어가 집안의 정보를 읽어 서버에 보내면 사용자는 해당 정보를 확인하고 원하는 조작을 하고 그에 맞는 데이터가 서버를 통해 다시
우렁케어에 전송되는 방식이다.  

Web통신 기반으로 제작 할 것이며 서버는 Django, 기기는 라즈베리파이와 아두이노, 그리고 사용자는 안드로이드로 정보를 확인하고 기기를 제어한다.  


# 주요기능  

## 홈 CCTV  

![picamera]({{ site.baseurl }}/assets/images/UleungCare_1/picamera.jpg)  

**focusable 5mp 적외선 야간 투시경 1080 p 라즈베리 파이 카메라 모듈**

적외선 야간 투시경 카메라를 이용하여 사용자가 집을 비울 때 집 안을 확인할 수 있고 반려동물과 함께 생활한다면 집을 장시간 비울 때 반려동물의 상태를 확인할 수 있다.
적외선 야간 투시경을 사용하였으므로 야간에도 사용 가능하다.  


## 적외선 리모컨 제어  

![IR]({{ site.baseurl }}/assets/images/UleungCare_1/IR.png)  
 
**적외선 IR센서 수신모듈**

![airconditioner]({{ site.baseurl }}/assets/images/UleungCare_1/airconditioner.png)  

**에어컨 제어 예상도**

집에서 적외선 리모콘으로 조작하는 기기인 에어컨과 TV를 하나의 리모컨으로 통합하고 제어할 수 있으며 온 습도 센서의 값을 받아서 에어컨을 자동으로 제어할 수 있다.
그 외 다른 적외선 리모콘을 사용하는 모든 기기들은 모두 제어 가능하다.  


## 음성제어  

![voice]({{ site.baseurl }}/assets/images/UleungCare_1/voice.jpg)  

**음성 제어 예상도**

음성 인식 API를 활용하여 디바이스의 모든 기능을 음성 제어 가능하며 Android Application에서도 음성 제어를 가능하게 한다.  



# 내 생각  

이번에 진행하는 프로젝트는 교내에서 듣고있는 설계 과목과 외부 경진대회인 공개SW 개발자 대회와 병행하여 진행중이다.  

![voice]({{ site.baseurl }}/assets/images/UleungCare_1/opensw.jpg)  

해당 대회가 마감일이 10/6 까지라서 한동안 바쁜 시간을 보냈다.  

교내 과목을 진행할 때 어차피 해야될 것들이라 미리 한다고 생각하지만 아무래도 시간이 많이 부족한 터라 원하는 결과물이 나오지는 않을 것 같다.  

이 글을 쓰기전 오리엔테이션과 멘토링 행사를 다녀왔는데 우리가 만드려는 것이 너무 초라해 보이고 흔한 아이디어라 자신감이 조금 떨어져있는 상태다. 
물론 학부단계에서는 적당한 프로젝트지만... 여기서 만족하고 싶지않고 욕심도 많이 생긴다.  

시간이 많이 부족해서 개발자 대회에서는 좋은 결과물을 만들어 내지 못하더라도 교내 설계과목에서는 많은 기능을 추가하고 고도화를 진행 할 것이다.
그리고 내년에 대회도 발전단계로 참여 할 수 있으니 지금은 마감일 지키는것에 집중할 생각이다.  

아무튼 잘 마무리 할 수 있으면 좋겠다.



