---
title: "루미큐브 프로젝트 -5-"
categories:
  - Project
tags: 
  - Rummikub
header:
  image: /assets/images/rummikub_title.png
  teaser: /assets/images/rummikub_title.png

---  

# 게임 플레이  

>일단 등록이 끝나면 이미 이루어진 족보가 깨지지 않게 패를 가져와서 내 족보로 이룰 수도 있다.  

루미큐브 규칙 중 하나이다.  

등록을 마친 플레이어는 테이블에 있는 카드세트를 족보가 깨지지 않는 선에서 마음대로 사용할 수 있다.  

그럼 이것을 어떻게 구현해야 하는가?  

카드를 옮길때 마다 족보를 검사하면 안된다.  

카드를 빼는 순간 족보가 깨질 가능성이 높기 때문이다. (ex 11,12,13 에서 13 카드를 빼는 순간)  

그래서 나는 플레이어가 카드 세트를 만드는 것을 끝냈을 때 테이블 내에 있는 모든 세트를 검사하기로 했다.  

또한 플레이어가 테이블의 카드와 자신의 수중에 있는 카드를 조합할 수 있는 공간 또한 별도로 만들었다.  

![makeset_1]({{ site.baseurl }}/assets/images/rummikub_5/makeset_1.png)  

플레이어의 수중에 있는 카드가 있는곳을 player, 플레이어들이 제작한 세트가 있는 공간을 table, player와 table의 카드를 사용하여 세트를 만들 수 있는 공간을 set이라고 정했다.  

![makeset_2]({{ site.baseurl }}/assets/images/rummikub_5/makeset_2.png)  

수중에 있는 카드들 중 족보에 맞는 카드를 set공간으로 이동시켜 족보를 완성시킨다.  

![makeset_3]({{ site.baseurl }}/assets/images/rummikub_5/makeset_3.png)  

완성된 족보를 table로 이동시킨다.  

![makeset_4]({{ site.baseurl }}/assets/images/rummikub_5/makeset_4.png)  

기존에 있던 세트들을 검사하고  

![makeset_5]({{ site.baseurl }}/assets/images/rummikub_5/makeset_5.png)  

새로 생성한 세트도 검사한다. 이때 이상이 없다면 턴을 정상적으로 넘긴다.  

이런식으로 제작하였다.  

카드를 옮기고 세트를 조합할 때 마다 원하는 공간을 선택해야 되는 번거로움이 있지만 나에게는 이것이 최선이었다.  

코드를 살펴보자  

~~~cpp
//int makeset(card player[30], card tablecard[36][13], int playernum,int playercardnum[4], SOCKET sock, SOCKADDR_IN clientaddr)

for (i = 0; i < 30; i++)//카드복사
		newplayer[i] = player[i];
for (i = 0; i<36; i++)//테이블 복사
{
		for (k = 0; k < 13; k++)
			newtablecard[i][k] = tablecard[i][k];//카드 복사		
}
~~~  

먼저 플레이어 카드와 테이블에 있는 카드들을 복사한다.  

혹시나 플레이어가 세트를 만들다가 실패하면 원상복귀 하기 위해서이다.  

~~~cpp
while (choice)//세트 등록
{
		print_table(newtablecard);
		print_set(set);
		recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);
    :
    :    
~~~  

이제 사용자는 선택한다.  

1. player -> set
2. table -> set  
3. set -> table  

어떤 곳으로 카드를 옮길지  

table -> player는 안만들었다.  

사용자가 카드를 가져가서 안돌려주면 골치아파지기 때문이다.  

사용자가 세트만들기를 끝낼 때 까지 while문에서 나오지 않는다.  

~~~cpp

if (choice == 1)//핸드 -> 세트공간
{
	printf("hand -> set\n");
	while (choice)
	{
	
		recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);
		printf("%d",choice);
		if (newplayer[choice - 1].num == 14 && newplayer[choice - 1].joker != 1)//비어잇는 카드
			printf("no card\n");
		else if (choice)
		{
			printf("recive card : num %d  color %d  joker %d\n"
				, newplayer[choice - 1].num, newplayer[choice - 1].color, newplayer[choice - 1].joker);

			set[12] = newplayer[choice - 1];// 카드를 세트공간으로 보냄
			newplayer[choice - 1] = emty;
			cardnum++;//넘긴 카드 갯수
			cardsort(set);
			sendto(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));
		}
	}
}
else if (choice == 2)//테이블 -> 세트공간
{
	printf("table -> set\n");
	while (choice)
	{
		recvfrom(sock, (char *)&tablexydata, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);
		printf("x : %d \n", tablexydata);
		recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);

		if (newtablecard[tablexydata][choice - 1].num == 14 && newtablecard[tablexydata][choice - 1].joker != 1)
			printf("no card\n");
		else if (choice)
		{
			printf("recive card : num %d  color %d  joker %d\n"
				, newtablecard[tablexydata][choice - 1].num,
				newtablecard[tablexydata][choice - 1].color,
				newtablecard[tablexydata][choice - 1].joker);

			set[12] = newtablecard[tablexydata][choice - 1];//테이블 카드를 세트 맨 뒤로
			newtablecard[tablexydata][choice - 1] = emty; // 테이블 카드 삭제
			cardsort(set);//카드 정렬
			for (i = 0; i<36; i++)//테이블카드 정렬
			{

					for (k = 0; k < 13; k++)
						cardsort(newtablecard[i]);
						
			}
			sendto(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));
			sendto(sock, (char *)newtablecard, TABLESIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));

		}
		//recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);

	}
}
else if (choice == 3)//세트공간 -> 테이블
{
	printf("set -> table\n");
	while (choice)
	{
		recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);

		if (set[choice - 1].num == 14 && set[choice - 1].joker != 1)
			printf("no card\n");
		else if (choice)
		{
			if (set[choice - 1].joker == 1)//그게 조커라면
			{
				recvfrom(sock, (char *)joker, 8, 0, (SOCKADDR *)&clientaddr, &addrlen);
				set[choice - 1].num = joker[0];
				set[choice - 1].color = joker[1];
			}
			printf("recive card : num %d  color %d  joker %d\n"
				, set[choice - 1].num, set[choice - 1].color, set[choice - 1].joker);

			recvfrom(sock, (char *)&tablexydata, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);
			printf("x : %d \n", tablexydata);
			newtablecard[tablexydata][12] = set[choice - 1];
			set[choice - 1] = emty;
			cardsort(newtablecard[tablexydata]);
			sendto(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));
			sendto(sock, (char *)newtablecard, TABLESIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));

		}
	}
}
cardsort(set);
sendto(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));

recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);


}//while close

~~~












