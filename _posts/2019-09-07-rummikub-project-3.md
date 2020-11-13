---
title:  "루미큐브 프로젝트 -3-"
categories:
  - Project
tags: 
  - Rummikub
  
header:
  image: /assets/images/rummikub_title.png
  teaser: /assets/images/rummikub_title.png
---  

# 족보검사 (세트 검사)  

게임을 진행하면서 원활한 플레이가 이루어지려면 프로그램이 플레이어가 제출한 세트를 인식 할 수 있어야 한다.  

세트를 인식하려면 세트가 정렬된 상태여야 한다.  

## 정렬 (Bubble sorting)  

![shuffle]({{ site.baseurl }}/assets/images/rummikub_3/cardsort.png)  

카드 정렬방식은 간단한 [**버블정렬**](https://ko.wikipedia.org/wiki/거품_정렬) 방식을 사용했다.  
정렬할 카드의 수가 한 세트당 최소 3개에서 최대 12개뿐 이므로 단순하게 구현하기에 적합하다고 생각됐다.  

~~~cpp
void cardsort(card *a)
{
	int i, j;

	for (i = 0; i<13; i++)
	{

		for (j = 12; j>i; j--)
		{
			if (a[j - 1].num>a[j].num)
				swap(a + j - 1, a + j);
		}
	}

}
~~~  

여기서 문제가 생겼는데 

카드의 숫자를 기준으로 작은 숫자부터 큰 숫자 순서대로 정렬을 하려고 하자 비어있는 카드들이 앞에 정렬되는 일이 벌어졌다.  

이게 무슨말이냐 하면 루미큐브를 플레이 할 때는 자신이 완성시킨 세트를 테이블위에 올려놓는다. 

그래서 우리도 테이블 위에 공간을 만들고 그 공간을 정보가 없는 카드들로 채워놓은뒤 완성된 세트를 내려놓을때 그 정보를 복사하여 테이블 공간에 넣어주는 것이다.  

이때 빈 공간의 카드의 초기화 값을 0으로 해두었더니 카드 정렬시 빈 카드가 앞으로 정렬되었다.  

한 공간당 13칸의 공간을 (1~12, 조커)잡아 놨는데 3개 한세트의 카드를 테이블에 올리면 뒤로 10칸이나 밀린다고 보면 된다.  

![cardarray]({{ site.baseurl }}/assets/images/rummikub_3/cardarray.png)  

이런 느낌이다.  

그래서 카드의 초기화값을 14로 잡고 정렬을 해서 문제를 해결했다.  


## 족보 조건검사  

![setcheck1]({{ site.baseurl }}/assets/images/rummikub_3/setcheck1.png)  

그냥 보기에는 복잡해보이는 잘 만들지는 못한 순서도이다.  

하지만 별거 없고 조건문 여러개를 계속 돌려보는 과정일 뿐이다.  

![setcheck2]({{ site.baseurl }}/assets/images/rummikub_3/setcheck_1.JPG)  

처음 이부분은 조건검사를 하기 위해 받아온 카드 세트가 몇장인지 카운트 하는 과정이다.  

받아오는 카드세트는 모두 13장으로 이루어져있지만 플레이어가 제출한 카드를 제외한 카드들을 빈 카드로 체워져 있어 카드가 없는것으로 판단한다.  

카드수를 카운트 하면 카드 정렬함수를 이용하여 카드세트를 정렬시킨다. 이 과정을 거치면 조건문이 있는 부분으로 카드세트를 넘긴다.  

~~~cpp
int i = 0, x, y;
int set = 0;//세트검사변수
int howmanycard = 0;//세트에 몇장의 카드가 있는지 카운트하는 변수
int sum = 0;//세트숫자의 총합  

for (i = 0; setcard[i].num != 14; i++)//카드가 몇장있는지
howmanycard++;
cardsort(setcard);
~~~  

![setcheck3]({{ site.baseurl }}/assets/images/rummikub_3/setcheck_2.JPG)  

정렬된 카드세트를 받은뒤 가장 앞에있는 카드를 확인한다.  

그 카드의 숫자가 14 즉 비어있는 카드라면 받은 카드세트가 모두 비어있다는 뜻이므로 바로 리턴하고 함수를 종료한다.  

카드가 있다는게 확인되면 가장먼저 복잡한 경우부터 확인한다.  

첫번째는 카드가 4장이고 숫자가 모두 같으면서 색이 모두 다른경우이다.  

~~~cpp
else if (setcard[0].num == setcard[1].num && setcard[2].num == setcard[3].num &&setcard[0].num == setcard[3].num){//숫자 4개가 같을때
	
	for (x = 0; x < 4; x++)
	{
		for (y = 3; y > x; y--)
		{
			if (setcard[y - 1].color != setcard[y].color)//카드 4개가 색이 다를때
				set++;
		}
	}
	if (set == 6)//카드가 4개이고 색이모두다르면 경우의 수는 6
	{
		for (i = 0; i < 4; i++)
			sum += setcard[i].num;
		return sum;
	}
}
~~~  

먼저 조건문에서 4장의 카드가 모두 같은지 확인한다.  

그다음 색이 모두 다른지 확인해야 하는데 숫자 확인할때 처럼 인접한 카드의 숫자만 같은지 확인한다고 해서 끝나는 것이 아니다.  

만약 그렇게 확인 한다면 카드가 빨파빨파 이런식으로 있을때 조건문은 1번카드 & 2번카드 , 2번카드 & 3번카드를 묶어서 확인하기 때문에 하나의 카드와 나머지 3장의 카드를 비교해봐야 한다.  

![setcheck4]({{ site.baseurl }}/assets/images/rummikub_3/setcheck_3.JPG)  

비교가 끝났을 때 모든 카드가 색이 다르다면 set의 카운트 값이 6이 되어야 한다.  

set이 6이라면 세트카드의 숫자를 모두 합하여 리턴해준다.  

두번째는 카드 세장의 숫자가 같고 색이 다를 때 이다.  

~~~cpp  

else if ((setcard[0].num == setcard[1].num && setcard[1].num == setcard[2].num) &&
	(setcard[0].color != setcard[1].color && setcard[1].color != setcard[2].color && setcard[2].color != setcard[0].color))
	{
		for (i = 0; i < 3; i++)
			sum += setcard[i].num;//카드숫자의 합
			
		return sum;
	}

~~~  

첫번째 조건인 카드 네장에서는 색이 모두 다른 것을 검사해야되는 경우의 수가 6번이 되기 때문에 조건문 내부에서 반복문을 통해 조건을 검사 했지만  

이번에는 카드가 세장만 있어서 조건문의 조건안에서 모두 체크하였다.  

조건을 통과하면 해당 세트 카드들의 숫자를 합해서 리턴해준다.  

마지막으로 세번째는 세트 카드들의 색이 모두 같고 연속되는 숫자로 이루어질때 이다.  

~~~cpp  
else
{
	for (i = 0; i<howmanycard; i++)
	{
		if ((setcard[i].num + 1 == setcard[i + 1].num) && (setcard[i].color == setcard[i + 1].color))
			set++;//연속된 카드가 숫자가 연속되고 카드색이 같을때
	}

	if (set == howmanycard - 1)//카드의 숫자가 연속될때 두개씩 비교하면 카드갯수 -1 번 만큼 값이 TRUE
	{
		for (i = 0; i < howmanycard; i++)
			sum += setcard[i].num;
		return sum;
	}
}
~~~  

![setcheck_arraynum1]({{ site.baseurl }}/assets/images/rummikub_3/setcheck_arraynum1.PNG)  

setcheck 함수로 넘어온 카드 세트는 이런식으로 구성되어 있다.  

![setcheck_arraynum1]({{ site.baseurl }}/assets/images/rummikub_3/setcheck_arraynum2.PNG)  

![setcheck_arraynum1]({{ site.baseurl }}/assets/images/rummikub_3/setcheck_arraynum3.PNG)  

카드 두장을 비교하는 작업을 한칸씩 이동하면서 카드 장수만큼 반복한다.  

비교를 했을 때 숫자가 연속되고 카드색이 같다면 set 변수를 +1 해준다.  

![setcheck_arraynum1]({{ site.baseurl }}/assets/images/rummikub_3/setcheck_arraynum4.PNG)  

카드 세트의 마지막에서 비교를 할 때는 카드와 비어있는 카드 공간이 비교가 되므로 set 변수가 +1 되지 않는다.  

이때는 반복 횟수가 끝났을 것이다.  

반복문이 끝난 뒤 set의 값을 확인하고 세트 카드의 수보다 1 적은 숫자라면 이 카드 세트는 색이 같고 연속된 숫자로 이루어진 카드 세트라는 뜻이다.  

카드들의 숫자 합을 구한 뒤 리턴해준다.  

이 조건 검사 함수 setcheck는 나중에 테이블을 관리해주는 함수에서도 호출하여 사용할 것이다.  
 
루미큐브를 플레이 할 때 생각해야할 변수들이 많아서 걱정이 많았지만 세트 규칙을 그대로 코드로 바꾸니 수월했다.  

루미큐브를 자주 해보지 못했다면 많이 어려웠을 것 같다.  

보드게임을 만들고 싶다면 직접 많이 플레이 하는 것이 좋다고 생각한다.















