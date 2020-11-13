---
title: "루미큐브 프로젝트 -4-"
categories:
  - Project
tags: 
  - Rummikub
header:
  image: /assets/images/rummikub_title.png
  teaser: /assets/images/rummikub_title.png

---  

# 등록  

>맨 처음에 패를 내려놓을 때는 "등록"이라고 해서 족보(들)를 이루는 패의 수의 합이 30 이상이어야 한다.  
(예를 들면 "10, 11, 12(총합 33)"나 "10, 10, 10(총합 30)"으로 이루어진 족보처럼) 등록은 반드시 자신의 패로만 해야 한다.  
일단 등록이 끝나면 이미 이루어진 족보가 깨지지 않게 패를 가져와서 내 족보로 이룰 수도 있다.  

눈여겨본 규칙중 마지막인 등록은 생각보다 신경써야 하는 것이 많았다.  

먼저 서버는 해당 플레이어가 등록을 했는지 아직 못했는지 알고있어야 한다.  

등록을 마치지 못한 플레이어가 총 합30 이하의 카드로 세트를 제출하려고 하거나, 테이블 위에 있는 카드 세트를 사용하려고 할 때 서버는 이것을 막아야 하기 때문이다.  

그래서 나는 등록을 진행하는 함수를 따로 만들어서 사용자가 등록을 하지 못했을 때 등록 절차를 반복하게 했고 총합 30 이상의 카드 세트를 제출해서 증록을 마쳤을 때 등록을 시켜주고 서버가 이 정보를 저장하고 있게 했다.  


~~~cpp

//void game() 중

	if (!player_regist[playernum])//등록을 못했다면 등록을진행한다
	{
		printf("plyaer %d registration\n", playernum + 1);
		player_regist[playernum] = registration(player[playernum], tablecard, playernum,playercardnum, sock, clientaddr[i]);
		if (!player_regist[playernum])//등록을 또 못했다면 카드한장 먹기
			distribution(playernum,playercardnum,player[playernum], c); // 카드 분배 함수
	}
	else//등록 했을때
	{
		printf("player %d make set \n", playernum + 1);
		
		player_set = makeset(player[playernum], tablecard, playernum,playercardnum, sock, clientaddr[i]);
		if(player_set && oldplayercardnum > playercardnum[playernum])
			printf("good set\n");
		else
		{
			printf("not good set\n");
			distribution(playernum, playercardnum,player[playernum], c);
		}
	}
~~~  

player_regist[playernum] 배열에 플레이어가 등록을 했을 때 1을 아직 못했다면 0을 저장해둔다.  

만약 플레이어가 등록에 실패하면 카드를 한장 분배해 준다.  

등록이 완료되어 player_regist[playernum]에 1이 들어가 있다면 원하는 대로 테이블에 있는 세트 카드를 사용할 수 있는 makeset 함수를 수행한다.  

이 함수는 나중에 다시 알아보겠다.  

~~~cpp
//Server registration
int registration(card player[30], card tablecard[36][13],int playernum,int playercardnum[4], SOCKET sock, SOCKADDR_IN clientaddr)
{
	int i, j, k, x, y;
	int choice = 1;//카드선택변수
	int joker[2];//조커에 넣은 정보를 받아오는 변수
	int setdata = 0;//만든 세트가 조건에 맞는지 클라이언트에게 알려준다
	int registerdata = 0;//등록이 됐는지 검사받는 변수
	int cardnum = 0;//카드를 몇장보냈는지 카운트 하는 변수
	int sum = 0; // 세트의 숫자합을 더할 변수

	card set[13];// 세트를 테이블로 보내기전 머무르는곳 
	card newplayer[30];//두개의 카드를 만들어 카드를 옮기는게 확실해지면 옮긴다
	card newtablecard[36][13] = {};//두개의 카드를 만들어 카드를 옮기는게 확실해지면 옮긴다
	card emty;//카드를 지울때 쓰는 카드

	int addrlen;
	addrlen = sizeof(clientaddr);

	for (i = 0; i < 30; i++)//카드복사
		newplayer[i] = player[i];
	for (i = 0; i<36; i++)//테이블 복사
	{

			for (k = 0; k < 13; k++)
				newtablecard[i][k] = tablecard[i][k];//카드 복사
		
	}

	print_table(newtablecard);

	while (choice)//등록 전체 루프  (세트만들기 + 세트조건검사)
	{

		while (choice)//세트 만들기 루프
		{
			recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);


			if (choice && newplayer[choice - 1].num == 14 && newplayer[choice - 1].joker != 1)
				printf("no card\n");
			else if (choice)
			{
				if (newplayer[choice - 1].joker == 1)
				{
					recvfrom(sock, (char *)joker, 8, 0, (SOCKADDR *)&clientaddr, &addrlen);
					newplayer[choice - 1].num = joker[0];
					newplayer[choice - 1].color = joker[1];
				}
				printf("recive card : num %d  color %d  joker %d\n"
					, newplayer[choice - 1].num, newplayer[choice - 1].color, newplayer[choice - 1].joker);
				set[12] = newplayer[choice - 1];//카드를 세트로 보내고 카드삭제
				newplayer[choice - 1] = emty;
				cardnum++;
				cardsort(set);
				sendto(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));

			}
		}

		if (setcheck(set) == 0 || setcheck(set) == -1)//세트가 조건이 틀리면
		{
			printf("player set wrong\n");
			for (i = 0; i < 30; i++)//카드복사
				newplayer[i] = player[i];
			for (i = 0; i < 36; i++)//테이블 복사
			{
					for (k = 0; k < 13; k++)
						newtablecard[i][k] = tablecard[i][k];
			}
			for (i = 0; i<13; i++)//세트카드비우기
				set[i] = emty;
			sum = 0;
			cardnum = 0;
			setdata = 0;//세트가 맞지 않다고 클라이언트에게 전송하기
			
			sendto(sock, (char *)&setdata, 4, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));
			sendto(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));
		}
		else//조건이 맞으면
		{
			printf("player set good   %d \n", setcheck(set));
			playercardnum[playernum] -= cardnum;//보낸 카드만큼 플레이어 카드 삭제
			cardnum = 0;
			sum += setcheck(set);

			for (x = 0; x < 36; x++)
			{

					if (newtablecard[x][0].num == 14)//비어있는 테이블 세트공간에 set를 넣는다.
					{
						for (i = 0; i < 13; i++)
						{
							newtablecard[x][12] = set[i];
							set[i] = emty;
							cardsort(newtablecard[x]);
						}
						break;
					}
				
			}
			setdata = 1;//세트가 맞다고 클라이언트에게 전송하기
			sendto(sock, (char *)&setdata, 4, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));
		}

		recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);



	}


	if (sum >= 30)//등록 성공
	{
		printf("player good register\n");
		registerdata = 1;
		sendto(sock, (char *)&registerdata, 4, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));
		cardnum = 0;
		for (i = 0; i < 30; i++)//카드복사
			player[i] = newplayer[i];
		for (i = 0; i < 36; i++)//테이블 복사
		{

				for (k = 0; k < 13; k++)
					tablecard[i][k] = newtablecard[i][k];
			
		}
		return 1;
	}
	else//등록실패
	{
		registerdata = 0;
		sendto(sock, (char *)&registerdata, 4, 0, (SOCKADDR *)&clientaddr, sizeof(clientaddr));

		printf("player not good register\n");
		return 0;
	}
}
~~~  

~~음... 지금 다시 작성한다면 더 잘 만들 수 있을까?~~  

코드 중간중간 클라이언트와 통신하는 코드들이 있다.  

이는 세트 검사와 테이블 카드의 관리는 서버가 담당하지만 카드의 선택과 세트를 조합하는 것은 플레이어인 클라이언트가 결정하기 때문이다.  

등록은 기능이 제한된 makeset 함수와 같기 때문에 지금부터 클라이언트와 통신을 진행해야 한다.  

~~~cpp  
//Client registration
void registration(card player[30], SOCKET sock, SOCKADDR_IN serveraddr)
{
	int i;
	int choice = 1;//선택변수
	int joker[2] = {};//조커 정보를 넣어서 서버에게 전송하는 변수
	int setdata = 0;//세트가 맞는지 서버에게 확인받는 변수
	int registerdata = 0;//등록이 됐는지 검사받는 변수
	int addrlen;
	card emty;//카드 삭제용 변수
	card newplayer[30];//플레이어 카드 복사본
	card set[13];
	addrlen = sizeof(serveraddr);

	for (i = 0; i < 30; i++)
		newplayer[i] = player[i];//카드복사

	while (choice)//등록 전체 루프  (세트만들기 + 세트조건검사)
	{



		while (choice)//세트 만들기 루프
		{
			print_playercard(newplayer);
			print_set(set);
			choice = choice_playercard(newplayer);
			//printf("카드 선택 : ");
			//scanf_s("%d", &choice);

			sendto(sock, (char *)&choice, 4, 0, (SOCKADDR *)&serveraddr, sizeof(serveraddr));//선택한 카드 서버에 보내기

			if (newplayer[choice - 1].num == 14 && newplayer[choice - 1].joker != 1)//비어있는 카드
				printf("카드 없습니다.\n");
			else if (choice)
			{

				if (newplayer[choice - 1].joker == 1)
				{
					print_window();
					joker[0] = choice_jokernum();

					print_window();
					joker[1] = choice_jokercolor(joker[0]);

					sendto(sock, (char *)joker, 8, 0, (SOCKADDR *)&serveraddr, sizeof(serveraddr));
				}
				newplayer[choice - 1] = emty;
				recvfrom(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&serveraddr, &addrlen);//수정한 set공간을 받아온다


			}
		}
		recvfrom(sock, (char *)&setdata, 4, 0, (SOCKADDR *)&serveraddr, &addrlen);//보낸카드의 세트가 조건에 맞는지 서버에게 확인받는다
		print_window();
		if (setdata == 0)//세트가 틀리면
		{

			gotoxy(PTWINDOWX + 35, PTWINDOWY + 2);
			printf("세트가 조건에 맞지 않습니다.");
			for (i = 0; i < 30; i++)//카드복사
				newplayer[i] = player[i];
			recvfrom(sock, (char *)set, SETSIZE, 0, (SOCKADDR *)&serveraddr, &addrlen);//수정한 set공간을 받아온다

		}
		else if (setdata == 1)
		{
			gotoxy(PTWINDOWX + 35, PTWINDOWY + 2);
			printf("세트가 조건에 맞습니다.");
		}

		choice = choice_more();

		sendto(sock, (char *)&choice, 4, 0, (SOCKADDR *)&serveraddr, sizeof(serveraddr));//세트를 더 추가할지 결정여부 서버에 보내기
	}

	recvfrom(sock, (char *)&registerdata, 4, 0, (SOCKADDR *)&serveraddr, &addrlen);//등록을 마쳤는지 검사받는다

	if (registerdata == 1)//등록 성공
	{

		for (i = 0; i < 30; i++)//카드복사
			player[i] = newplayer[i];

	}

}
~~~  

카드 정보는 구조체로 이루어져 있으며 이 정보를 플레이어가 카드 세트를 만들때 마다 전송하는 것은 불편하다고 생각했다.  

어차피 서버는 클라이언트가 어떤 카드를 어떤 순서로 들고 있는지 알고 있으므로 클라이언트가 몇번째 카드를 선택했는지만 서버에 알려주면 서버는 클라이언트가 어떤 카드를 선택했는지 알 수 있다고 생각했다.  

그래서 등록을 할 때나 카드 세트를 만들 때 모두 몇번째 카드를 선택했는지만 전송하게 했다.  

~~~cpp
//client
sendto(sock, (char *)&choice, 4, 0, (SOCKADDR *)&serveraddr, sizeof(serveraddr));

//server
recvfrom(sock, (char *)&choice, 4, 0, (SOCKADDR *)&clientaddr, &addrlen);

~~~  

그럼 서버는 그 카드들을 모아서 세트를 만들어 보고 족보가 맞지 않으면 거절, 족보가 맞으면 카드 숫자들의 합을 계산해서 30이상이면 1을 리턴해주고 30미만이면 0을 리턴한다.  

~~~cpp
//void game()
player_regist[playernum] = registration(player[playernum], tablecard, playernum,playercardnum, sock, clientaddr[i]);
~~~  

registration 함수는 전체 게임이 진행되는 game함수에서 호출하여 사용하며 반환값을 player_regist[playernum]에 전달해준다.  

등록이 성공하면 해당 플레이어의 등록여부 확인변수에 1을 바로 전달해 주는 것이다.  

등록을 마친 플레이어는 다음 턴부터 카드 세트의 숫자 합이 30 미만이어도 세트를 제출할 수 있으며 테이블 위에 있는 카드들을 족보가 깨지지 않는 선에서 마음껏 사용할 수 있다.








