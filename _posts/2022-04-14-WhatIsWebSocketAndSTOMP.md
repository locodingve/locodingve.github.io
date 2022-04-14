---
layout: post
title:  "WetSocket과 STOMP란?"
date:   2022-04-14 10:00:00 +0900
categories: messageBroker
tags: WebSocket, STOMP
comments: 1
---


## WetSocket

WebSocket 프로토콜을 단일 TCP 연결을 통해 클라이언트와 서버 간에 전이중 양방향 통신 채널을 설정하는 표준화된 방법을 제공합니다. 
특징으로는 WebSocket 프로토콜은 접속 확립에 HTTP를 사용하지만, 그 후 통신은 WebSocket 독자의 프로토콜로 이루어집니다. 그리고  header가 상당히 작아 overhead가 적다는 특징이 있습니다. 
장점으로는 http와 같은 포트(80, 444)를 사용하기 때문에, 기존 방화벽 규칙을 재사용할 수 있다는 점과 데이터의 송신과 수신에 각각 커넥션을 맺을 필요가 없어 하나의 커넥션으로 데이터를 송수신 할 수 있다는 점이 있습니다.  

{% highlight text %}
⚠️ http와 socket이란 뭘까?
- 공통점 
	- TCP 기반으로 패킷이 만들어진다. 
	- 즉 둘 다 3 way handshaking 작업과 4 way handshaking 작업을 거칩니다. 
- 차이점
	- 정해진 규칙에 의거하여 클라이언트와 요청에 대한 정보를 제공해주고 연결을 끊을 것인가? Yes(-> http), No(-> socket)
{% endhighlight %}

## STOMP

 STOMP(Simple/Stream Text Oriented Message Protocol)는 websocket 위에서 동작하는 문자 기반 메시지 프로토콜로써 클라이언트와 서버가 전송할 메세지의 유형, 형식, 내용들을 정의하는 매커니즘입니다. 
 기본적으로 pub/sub 구조로 되어 있어, 메시지를 전송하고 받아 처리하는 부분이 확실히 정해져 있습니다. 

## SpringBoot WebSocket STOMP Guide

- https://www.javaguides.net/2019/06/spring-boot-websocket-stomp-sockjs-example.html

### 참고 자료
- https://stackoverflow.com/questions/40988030/what-is-the-difference-between-websocket-and-stomp-protocols
- https://www.javaguides.net/2019/06/spring-boot-websocket-stomp-sockjs-example.html
- https://hwan-shell.tistory.com/271
- https://duckdevelope.tistory.com/19
