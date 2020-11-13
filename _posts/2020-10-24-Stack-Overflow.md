---
title:  "Stack Overflow에 질문 올리기"
categories:
  - Study
header:
  teaser: /assets/images/stackoverflow.png
  image: /assets/images/stackoverflow.png
---  

# Questions
궁금한 내용이 생기고 빠르게 피드백을 받고싶어서 난생 처음으로 아는 영어 모르는 영어 다 써서 스택오버플로우에 [질문](https://stackoverflow.com/questions/64493166/how-can-i-generate-pattern-array-from-string-array-in-java)을 올렸다.

아래는 내가 올린 질문 전문이다.

---
# How can i generate Pattern array from String array in java
I want generate Pattern array from String array.

So i write code like this
```
public class LawDataPersonNameFinder implements PersonNameFinder{
    String[] backConditionRegxArray = {"\\s외\\s\\d\\s인"};
    Pattern[] backConditionPatterns;

    public LawDataPersonNameFinder() {
        backConditionPatterns = new Pattern[backConditionRegxArray.length];
        int index = 0;
        for (String str : backConditionRegxArray) {
            backConditionPatterns[index++] = Pattern.compile(str);
        }
    }

    @Override
    public Area[] findPersonNAme(String text) {
        return new Area[0];
    }
}

```
`backConditionRegxArray` can be added at any time

But i want to use Stream... how can i do that?  

---  

한 10분정도 지나고 답글이 달리고 좋아라 할 때  
![image](https://user-images.githubusercontent.com/49122299/97025873-9b036980-1593-11eb-9b2b-2ff2fb66f403.png)
질문글에 대해서 지적이 들어오더니  

![image](https://user-images.githubusercontent.com/49122299/97025617-48c24880-1593-11eb-8ae3-63854b931b05.png)  
비추천을 받고   

![image](https://user-images.githubusercontent.com/49122299/97025818-89ba5d00-1593-11eb-8b60-1c1d957d69c3.png)
심지어는 어떤 사람이 와서 문법 교정을 해주고 갔다.

![image](https://user-images.githubusercontent.com/49122299/97025948-b5d5de00-1593-11eb-8dff-67577a132905.png)  
이후에는 질문이 닫혔다.  

스택오버플로우에서 글을 보기만 했지 직접 써보는건 처음이라 조심스럽게 접근하긴 했는데 조금 속상했다.  

찾아보니 다른 사람들도 비슷한 경험을 하는 것 같다.  

- [https://stackoverflow.com/posts/44539729/revisions](https://stackoverflow.com/posts/44539729/revisions)
- [https://meaownworld.tistory.com/113](https://meaownworld.tistory.com/113)

# Why?  
왜 나는 이렇게 비추천을 받고 지적당하고 질문까지 닫혔을까?  

이에 대한 대답은 아래의 Question Guidline에서 확인할 수 있다.

[https://stackoverflow.com/help/how-to-ask](https://stackoverflow.com/help/how-to-ask)

이곳에서 말하는 것을 요약하자면 다음과 같다.  

## 찾아보고 계속 찾아봐라  
중복되는 질문을 올리지 말고 지금 겪고있는 문제와 관련된 질문을 먼저 찾아보고 해결해볼 생각을 해야된다.  

## 문제를 요약해서 제목을 지어라
사람들이 처음으로 보게 될 제목이 흥미가 떨어지고 이해가 가지 않는다면 사람들은 쳐다도 보지 않을 것이다.  

예를 들어  
- **Bad**: C# Math Confusion
- **Good**: Why does using float instead of int give me different results when all of my inputs are integers?
- **Bad**: [php] session doubt
- **Good**: How can I redirect users to different pages based on session data in PHP?
- **Bad**: android if else problems
- **Good**: Why does str == "value" evaluate to false when str is set to "value"?

제목 안에 문제를 함축적으로 명확하게 표현해야 사람들이 관심을 가지고 들어올 것이다.  

## 코드를 올리기 전에 문제를 설명하라  
코드를 포함 시키는 것이 모든 문제에 도움이 되지는 않는다. 물론 직접 작성한 코드로 인해 발생하는 문제라면 첨부하는 것이 도움이 되겠지만 **전체 프로그램의 코드를 모두 복사해서 올리지 마라.**  

## 관련된 모든 테그를 추가해라
언어, 라이브러리, 특정한 API등 질문과 관련된 것들을 모두 테그에 추가시켜라  

## 질문을 올리기 전에 교정하라
문제를 다시 살펴보고 환경을 새로 고쳐보고 문제의 정보를 더 추가할 수 없는지 확인해봐라

## 질문을 올리고 나서 피드백을 수용하라
누군가가 답을 남겼다면 읽어보고 만약 빠트린 정보가 있다면 수정하고 추가해라

## 도움 찾기
질문들이 좋은 평가를 받지 못해도 절망하지 말고 좋은 질문을 하는 방법을 배워나가라

# Now...
처음에는 당황스럽고 짜증도 났지만, 다시 보면 내가 너무 안일하게 질문을 올렸다고 생각된다.  
아마도 내가 올린 질문의 내용은 조금 더 찾아봤으면 정보가 나왔을 것이다. 또한 질문의 목적 또한 명확히 밝히지 않았으니 이렇게 된건 당연한 결과였다.  

오기도 생기고 좋은 질문을 올리고 싶은 욕심도 난다. 또한 다른 사람들이 어떤 방식으로 질문하는지도 궁금하다.  

코딩 공부와 영어 공부를 동시에 할 수 있는 좋은 환경이라고 생각한다. 꾸준하고 천천히 접근해봐야겠다.  

시간이 지나면 분명 도움이 될 것이다.