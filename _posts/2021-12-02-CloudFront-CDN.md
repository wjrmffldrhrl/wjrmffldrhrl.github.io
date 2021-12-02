---
title:  "종목 토론방 크롤러"
categories:
  - Infrastructure
tags:
  - AWS
  - CloudFront
  - CDN
header:
  teaser: /assets/images/cloudfront/2021042810451468335ca61317807ec7691ec90c59750a0b2057e0.png
  image: /assets/images/cloudfront/2021042810451468335ca61317807ec7691ec90c59750a0b2057e0.png
---  


# CloudFront

# 배경

최근에 시각화 툴로 사용하고 있던 redash가 [서비스 종료](https://redash.io/help/faq/eol)되었습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled.png)

이를 대체하기 위해 다른 시각화 툴을 찾으면서 호스팅 서비스를 이용하지 않고 직접 구축하여 사용하기로 했습니다.

![2021042810451468335ca61317807ec7691ec90c59750a0b2057e0.png]({{ site.baseurl }}/assets/images/cloudfront/2021042810451468335ca61317807ec7691ec90c59750a0b2057e0.png)

그렇게 결정된 것이 [Apache Superset](https://superset.apache.org/)이며 직접 배포하기 위해  K8S에서 구성하였습니다.

Superset에서는 [helm chart](https://github.com/apache/superset/tree/master/helm/superset)를 제공해주고 있었기 때문에 K8S에서 배포하는 과정은 어렵지 않았습니다.

그러나 직접 배포하는만큼 몇 가지 문제들이 발생했고 그 중 하나는 페이지를 이동할 때 마다 발생하는 많은 static file requests와 이로인한 성능 저하와 오류들이었습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 1.png)

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 2.png)

이를 해결하기 위한 방법으로 CDN을 생각했습니다.

항상 고정적인 파일을 Superset 서버에 요청하지 않고 CDN 서버에 요청한다면 Superset 서버의 부하도 줄어들고 응답 받는 속도도 빨라질 것이라고 생각했기 때문입니다.

저희 회사의 인프라는 대부분 AWS로 구성되어 있기 때문에 CDN 또한 AWS의 CDN 서비스인 AWS CloudFront를 사용하기로 했습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 3.png)

# 구성

CDN 적용 전 Superset의 네트워크 구성은 다음과 같았습니다.

![_CDN.drawio (1).png]({{ site.baseurl }}/assets/images/cloudfront/_CDN.drawio_(1).png)

1. 사용자가 AWS Route 53에서 생성된 도메인 네임으로 요청합니다.
2. 해당 도메인은 AWS Elastic Load Balancer를 가리킵니다.
3. ELB의 Listener의 규칙에 의해 Superset이 존재하는 노드를 포함한 Target Group으로 요청이 전달됩니다.
    
    ![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 4.png)
    
4. Target Group은 superset으로 요청을 전달하고 사용자에게 요청 결과를 반환해줍니다.

이제 해당 구조에 CDN를 추가해야 합니다.

AWS CloudFront에서는 원본 서버의 도메인을 제공하면 자체적인 도메인을 생성합니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 5.png)

처음에 생각했던 방법은 원본 도메인을 Route 53에서 제공하는 도메인을 사용하고 사용자는 CloudFront에서 생성한 도메인으로 요청하는것이었습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 6.png)

그러나 이렇게 사용하면 몇 가지 문제점이 있습니다.

1. 원하는 도메인 명을 사용하지 못한다.
2. 사용자가 원본 도메인으로 접근하면 CDN의 효과를 얻을 수 없다.

1번 문제점은 CloudFront에서 대체 도메인을 적용하여 해결할 수 있지만, 원본 도메인과 같게 지정할 수 없고 여전히 사용자가 원본으로 직접 접근할 수 있기 때문에 다른 형태의 구성과 추가적인 조치가 필요했습니다.

먼저 ELB를 바라보고있던 Route 53의 레코드 값을 CloudFront를 바라보도록 수정했습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 7.png)

이때 레코드 값에서 CloudFront의 별칭을 사용하기 위해서는 CloudFront에 CNAME이 지정되어 있어야 합니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 8.png)

이후에 원본을 Route 53의 도메인이 아닌 Elastic Load Balancer의 값으로 지정해주면 됩니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 9.png)

이렇게 설정을 끝내면 아래와 같은 구조가 됩니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 10.png)

# 문제상황

구조적으로 문제가 없다고 판단하여 테스트를 진행해보았으나 몇가지 문제점이 발생하였습니다.

### 404 Not Found

이제 대체 도메인으로 지정된 superset.xxxxx.com으로 접속을 시도해봤습니다.

그러나 404 Error가 발생하고 Superset 페이지는 반환되지 않았습니다.

Response Headers를 살펴보니 CloudFront까지 요청은 잘 전달된 것으로 보입니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 11.png)

추가적으로 로그를 살펴보니 Superset web server에서는 요청 로그가 남지 않았고, ELB의 로그에서는 CloudFront의 요청이 확인되었습니다.

그렇다면 1. ELB에서 Target Group으로 전달되지 못했거나, 2. Target Group으로 전달된 요청이 Target을 찾지 못했다는 뜻이 됩니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 12.png)

위의 상황을 인지하고 정보를 찾던 중 아래의 글을 발견하였습니다.

[호스트 헤더를 오리진에 전달하도록 CloudFront 구성](https://aws.amazon.com/ko/premiumsupport/knowledge-center/configure-cloudfront-to-forward-headers/)

해당 글에서 다음과 같은 내용을 볼 수 있었습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 13.png)

AWS CloudFront는 가상 호스팅을 사용하기 때문에 호스트 헤더를 원본 서버로 전달해야 한다고합니다.

즉, ELB에서 target group까지 전달된 요청이 host 정보가 없어서 target을 찾지 못한 것이었습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 14.png)

CloudFront에서 헤더값을 전달해보겠습니다.

동작의 기본값의 헤더에서 Host를 포함시킵니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 15.png)

이제 Target group이 superset이 존재하는 target을 찾아 해당 위치로 요청을 전달하고 사용자에게 응답을 반환해 줄 수 있습니다. 

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 16.png)

### 403 Forbidden

정상적인 superset 로그인 화면을 확인하고 로그인을 하려고 하면 아래와 같은 오류가 발생합니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 17.png)

반환된 오류를 잘 읽어보면 우리가 요청한 HTTP의 메서드가 허가되지 않는다고 합니다.

로그인을 할 때 HTTP 메서드는 POST를 사용하고 CloudFront에서는 이것이 허가되지 않는 것으로 보입니다.

CloudFront 원본 동작 설정에서 이것을 지정해줍니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 18.png)

그러면 드디어 superset에 입성할 수 있습니다.

![Untitled]({{ site.baseurl }}/assets/images/cloudfront/Untitled 19.png)

그 외의 설정들

- 특정 파일만 지정하여 캐싱하기
- Hit 확률 높이기

는 다음에 해보도록 하겠습니다.

# Reference

### CDN이란?

[콘텐츠 전송 네트워크 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EC%BD%98%ED%85%90%EC%B8%A0_%EC%A0%84%EC%86%A1_%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)

### AWS CloudFront란?

[Amazon CloudFront란 무엇입니까?](https://docs.aws.amazon.com/ko_kr/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)
