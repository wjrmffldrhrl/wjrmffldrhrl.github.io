---
title:  "[Spring Boot]Web Socket STOMP"
categories:
  - Spring
tags:
  - spring
  - spring-boot
  - java
  - websocket
header:
  teaser: /assets/images/STOMP.png
  image: /assets/images/STOMP.png
---  
STOMP를 활용한 채팅 기능을 완성해보자

# STOMP
STOMP는 Simple Text Oriented Messaging Protocol의 약자이며, 텍스트 기반의 프로토콜이다.

![flowchart]({{ site.baseurl }}/assets/images/STOMP/flowchart.png)  

WebSocket을 지원하며 TCP나 WebSocket과 같은 신뢰성있는 양방향 Protocol상에서 사용할 수 있다.  

## WebSocket
WebSocket은 서버와 클라이언트 사이에 양방향 통신 채널을 구축할 수 있는 통신 프로토콜이다.  

기존의 웹 통신 방식은 클라이언트의 요청이 오면 서버에서 응답을 해주는 방식인데 이러한 방식은 채팅과 같은 지속적인 확인이 필요한 곳에서는 부적합하다.  

![polling]({{ site.baseurl }}/assets/images/STOMP/pooling.jpg)  

이를 해결하기 위해 HTTP Polling등 몇가지 대안을 만들었지만 이러한 방법은 지속적인 요청이 필요하기 때문에 리소스 낭비가 크다는 단점을 해결하지 못했다.  

![websocket]({{ site.baseurl }}/assets/images/STOMP/websocket.png)  

그래서 TCP 통신처럼 지속적인 양방향 연결이 되어있는 프로토콜이 필요했고 webSocket이 탄생했다.  

## Spring Boot에서의 STOMP

### 1. pom.xml

먼저 필요한 의존성 등록을 해준다.
~~~xml
 <!-- web socket STOMP dependency -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-websocket</artifactId>
      <scope>compile</scope>
    </dependency>
        <dependency>
      <groupId>org.webjars</groupId>
      <artifactId>webjars-locator-core</artifactId>
    </dependency>
    <dependency>
      <groupId>org.webjars</groupId>
      <artifactId>sockjs-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    <dependency>
      <groupId>org.webjars</groupId>
      <artifactId>stomp-websocket</artifactId>
      <version>2.3.3</version>
    </dependency>
~~~

### 2. WebSocketConfig.java

이후에 JavaScript에서 생성할 웹소켓의 연결과 메세지 송수신 위치를 설정한다.

~~~java 
@Configuration // Spring Configuration class
@EnableWebSocketMessageBroker // WebScoket message handling을 허용해준다 
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

  @Override // MessageBroker는 송신자에게 수신자의 이전 메세지 프로토콜로 변환해주는 모듈 중 하나
            // 요청이 오면 그에 해당하는 통신 채널로 전송, 응답 또한 같은 경로로 가서 응답한다.
  public void configureMessageBroker(MessageBrokerRegistry config) {
    config.enableSimpleBroker("/receive"); // 메세지 응답 preifx
    config.setApplicationDestinationPrefixes("/send"); // 클라이언트에서 메세지 송신 시 붙여줄 prefix
  }

  @Override
  public void registerStompEndpoints(StompEndpointRegistry registry) {// 최초 소켓 연결 시 endpoint
    registry.addEndpoint("/teamhash-websocket").withSockJS(); // javascript에서 SockJS생성자를 통해 연결
    
  
  }

}
~~~

### Chat.java
지금 진행중인 프로젝트에서는 메세지를 실시간으로 저장하고 이후에 다시 접속했을 때 주고받은 메세지를 보여주기 위해 DB에 저장한다.

~~~java
@Entity
@Getter @Setter @EqualsAndHashCode(of = "id")
@Builder @AllArgsConstructor @NoArgsConstructor
public class Chat {
    @Id @GeneratedValue
    private Long id;
    
    private Long projectId;
   
 // @ManyToOne
 // @JoinColumn(name ="account_id")
 // private Account sender;
 
    @Column(columnDefinition = "TEXT", nullable = false)
    private String sender;

    @Column(columnDefinition = "TEXT", nullable = false)
	private String message;

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime sendDateTime;
}
~~~  
송신한 사람의 정보를 담을 sender는 기존에 Account DB와 다대일 관계로 연결시키려고 했으나 JSON형태로 데이터를 전송하는 STOMP에서 정상적인 작동이 이루어지지 않아서 지금은 송신한 유저의 nickName만 담아 저장하기로 했다.  

### ChatController.java

메세지를 주고받을 페이지를 맵핑해주고 WebSocketConfig.java에서 설정한 송수신 위치에 메세지를 보내주는 컨트롤러를 생성한다.  
~~~java

@Controller
@RequiredArgsConstructor
public class ChatController {

    private final ChatService chatservice;
    private final ProjectService projectService;
    
  @GetMapping("/project/{nickname}/{title}/chatting") // 첫 화면 매핑
  public String index(Model model,@PathVariable("nickname") String nickname, 
                                    @PathVariable("title") String title , @CurrentUser Account account ){

    // nickname과 projectTitle로 projectId 찾기
    Long projectId = projectService.getProjectId(nickname, title);

    System.out.println("ProjectId : "+projectId);
    
    List<Chat> chatList = chatservice.getChatList(projectId); // 채팅 리스트 추출
 
    
    model.addAttribute("chatList",chatList);// 추출된 채팅 리스트 전달
    model.addAttribute("account",account);
    model.addAttribute("projectId", projectId);
    return "project/chatting";
  }



  @MessageMapping("/chat/{projectId}")// 메세지가 목적지(/chat)로 전송되면 chat()메서드를 호출한다.
  @SendTo("/receive/chat/{projectId}")// 결과를 리턴시키는 목적지
  public Chat chat(@DestinationVariable Long projectId, Chat chat) throws Exception{
  

    chat.setSendDateTime(LocalDateTime.now()); // DB의 채팅에는 시간이 자동으로 저장 되지만
                                                  // JavaScript에서 출력을 해주기 위해 값을 한번 넣어준다.
   
    chat.setProjectId(projectId);                                        
    chatservice.saveChat(chat); // 전송 전 DB에 저장

    return chat;
  }

}
~~~  

먼저 진행중인 프로젝트는 유저가 진행중인 과제마다 하나의 방이 생성되어 있으며 그 방에는 고유의 id값이 부여된다.  

여러 유저가 사용하는 만큼 메세지의 송수신은 id값에 맞는 위치에 전송되어야 한다.

~~~java
  @MessageMapping("/chat/{projectId}")// 메세지가 목적지(/chat)로 전송되면 chat()메서드를 호출한다.
  @SendTo("/receive/chat/{projectId}")// 결과를 리턴시키는 목적지
  public Chat chat(@DestinationVariable Long projectId, Chat chat) throws Exception{}
~~~
@MessageMapping은 해당 url로 메세지가 전송되면 메서드를 호출해주며  

@SendTo는 chat객체를 반환시켜줄 목적지를 설정한다.  

앞서 말한 것처럼 DB에 저장하기 위해 chat()메서드에서 미리 구현해둔 chatservice를 호출하여 DB에 메세지 정보를 저장한다.  

### chatting.html

~~~html
컨트롤러에서 반환하는 템플릿인 chatting.html에서는 소켓을 생성하여 연결하고 메세지를 송수신 하는 기능과 지금까지 저장된 메세지 내용을 가져와 사용자에게 보여주는 역할을 한다.

<script th:inline="javascript">
    var stompClient = null;

    // How to Thymeleaf send data to JavaScript

    /*<![CDATA[*/
    var projectId = /*[[${projectId}]]*/ 'default';
    /*]]>*/

    console.log('project info : ' + projectId); 

    function connect() { // 생성된 소켓과 연결
        var socket = new SockJS('/teamhash-websocket');
        stompClient = Stomp.over(socket);
        stompClient.connect({}, function (frame) {

            console.log('Connected: ' + frame);

                 
            stompClient.subscribe('/receive/chat/'+projectId, function(chat){ // 구독 기능을 통해 해당 주소에 메세지가 오면 showChat 함수 호출
                showChat(JSON.parse(chat.body));
            });
        });
    }
    function disconnect() { // 연결 종료
        if (stompClient !== null) {
            stompClient.disconnect();
        }
        console.log("Disconnected");
    }


    function sendChat(){ // 메세지 전송 index.html의 text box에 입력된 값을 읽어온다.
        stompClient.send("/send/chat/"+projectId, {}, 
                        JSON.stringify({'sender':$("#sender").val(), 
                                        'message': $("#chatMessage").val()}));
    }

    function showChat(chat) { // 수신한 메세지를 출력
                            // DB에 저장된 값을 출력해주는 Thymeleaf와 형태를 맞추기 위해 
                            // 적절한 배치 필요

        var sendDateTime = chat.sendDateTime.split("T"); // 시간과 분 출력 
        sendDateTime = sendDateTime[1].split(":");
        $("#chattings").append("<tr><td><span>" + chat.sender +
        "</span></td><td><span>" + chat.message + 
        "</span></td><td><span>" + sendDateTime[0] + 
        ":" + sendDateTime[1] + "</span></td></tr>");
    }


    $(function () { // 함수 호출 연결
        $("form").on('submit', function (e) {
            e.preventDefault();
        });
        $( "#chatSend").click(function(){ sendChat();});
    });


</script>
<script>
    //창을 열고 닫을 때 연결과 해제 
    window.onload = function (){
        connect();
    }

    window.BeforeUnloadEvent = function(){
        disconnect();
    }

</script>
~~~


# Reference
> <http://blog.naver.com/PostView.nhn?blogId=scw0531&logNo=221097188275>
> <https://swiftymind.tistory.com/tag/Websocket%20%2B%20STOMP>
> <https://swiftymind.tistory.com/tag/Websocket%20%2B%20STOMP>
> <https://jayks.tistory.com/13>
> <https://spring.io/guides/gs/messaging-stomp-websocket/>


