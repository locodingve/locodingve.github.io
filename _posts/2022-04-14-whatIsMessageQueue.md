---
layout: post
title:  "메시지큐란?"
date:   2022-04-04 16:00:00 +0900
categories: MOM
tags: kafka, rabbitMQ
comments: 1
---

## Message Queue

- 특징
	- 메시지 지향 미들웨어(Message Originted Middleware: MOM)은 비동기 메시지를 사용하는 다른 응용프로그램 사이의 데이터 송수신을 의미하는데 MOM은 구현한 시스템을 메시지큐(Message Queue:MQ)라고 합니다. 
	- MOM(Message Originted Middleware)를 구현한 솔루션으로 비동기 메시지를 사용하는 서비스들 사이에서 데이터를 교환해주는 역할을 한다. 
	- Producer(sender)가 메시지를 큐에 전송하면 Consumer(receiver)가 처리하는 방식으로, producer와 consumer에 message 프로세스가 추가되는 것이 특징이다. 
- 장점
	- Queue에 넣기 때문에 나중에 처리가 가능합니다. (비동기, Asynchronous)
	- 애플리케이션과 분리되어 있습니다. (비동조, Decoupling)
	- 일부가 실패 시 전체에 영향을 받지 않습니다. (탄력성, Resilience)
	- 실패할 경우 재실행이 가능합니다. (과잉, Redundancy)
	- 작업이 처리된 걸 확인 가능합니다. (보증, Guarantees)
	- 다수의 프로세스들이 큐에 메시지를 보낼 수 있습니다. (확장성, Scalable)

## Kafka 

- 대용량 실시간 로그 처리에 특화되어 설계된 메시지 시스템으로 TPS가 매우 우수하고, 메시지를 메모리에 저장하는 기존 메시징 시스템과 달리 파일에 저장을 저장하는데 그로 인해 카프카를 재시작해도 메시지 유실 우려가 감소된다. 
- **기본 메시지 시스템(rabbitMQ 등)**에서는 브로커가 컨슈머에게 메시지를 **push**해 주는 방식인 것에 반해, **카프카**는 컨슈머가 브러커로 부터 **메시지를 직접 가져가는 pull 방식**으로 동작하기 때문에 컴슈머는 자신의 처리 능력 만큼의 메시지만 가져와 최적의 성능을 낼 수 있습니다. 

![image](https://user-images.githubusercontent.com/88185304/163325683-aa06f4ba-ae43-4554-b90d-fe3b4bb37842.png)

- publish-subscribe 모델을 기반으로 동작하며 크게 **producer, consumer, broker**로 구성되어 있습니다. 
- pub-sub 모델은 메시지를 특정 수신자에게 직접적으로 보내주는 시스템이 아닙니다.
- publisher는 메시지를 topic을 통해서 카테고리화되어 있습니다. 
- 분류된 메시지를 받기 위해 subscribe는 그 해당 topic을 subscribe함으로써 메시지를 읽어올 수 있습니다. 
- 클러스터 내에 broker에 대한 분산처리는 zookeeper가 담당합니다.
- producer 중심적, 많은 양의 데이터를 파티셔닝하는데에 기반을 둡니다. 
- consumer는 전달 상태를 기억합니다. 

- topic/partition
	- 메시지는 topic으로 분류되고, topic은 여러개의 partition으로 나뉘어집니다. 
	- partition 내의 한 칸을 log라고 불립니다. 
	- 한 번 늘린 partition은 줄일 수 없으므로 충분히 고려하여 늘려야합니다. 
	- partition을 늘렸을 때, 메시지는 Round-Robin 방식으로 전달됩니다. 

- broker/zookeeper
	- broker는 kafka의 서버를 의미합니다. 
	- (반드시 필요) zookeeper는 분산 메시지 큐의 정보를 관리하는 역할을 합니다. 

{% highlight text %}
⚠️ TPS란?
- Transaction per Second의 약자로서, 1초당 처리할 수 있는 트랜잭션의 개수를 의미한다.

{% endhighlight %}


## RabbitMQ

- rabbitMQ는 AMQP 프로토콜을 구현한 메세지 브로커입니다. 쉽게 말해, 시스템 간에 메세지를 전달해주는 서비스입니다.

![image](https://user-images.githubusercontent.com/88185304/163334366-9324fb61-03da-4697-b3ef-b5d1736a6e94.png)

- Publisher: Queue에 넣을 메시지를 생성하는 주체입니다. publisher가 exchange에게 메시지를 전달할 때, publisher는 메시지가 담기게 될 Queue의 key 값도 같이 전달합니다. Queue 각각은 자신의 key(routing key라고도 함)를 소유하고 있습니다. 
- Exchange: 우선 수신한 key에 특정한 알고리즘 중 하나를 적용하여 메시지가 될 Queue를 찾습니다. 이를 binding이라고 합니다. 그 다음 Publisher로부터 수신한 메시지를 이 Queue로 분배합니다. 이를 routing이라고 합니다. 
	- 알고리즘(exchange type)
		- fanout
			- 모든 Queue에 메세지를 전달합니다. 
			- routing key에 대해 연산할 필요가 없기 때문에 성능적인 이점이 있습니다. 
			- broad cast에 적합하다.
		- direct
			- publisher가 전달한 key와 routing key가 일치하는 Queue에만 메세지를 전달합니다. 
			- 매우 단순합니다.
			- uni cast에 적합합니다.
		- topic
			- publisher가 전달한 key가 routing key 패턴과 일치하는 Queue에만 메시지를 전달합니다. 
			- muti cast에 적합합니다. 
		- header
			- 메시지 속성 내의 header 테이블을 사용하여 특정한 규칙에 맞는 Queue에만 메시지를 전달합니다. 
			- routing key를 사용하는 것보다 쉽습니다. 
			- muti cast에 적합합니다.
- Queue: 여러개가 존재할 수 있습니다. 각 Queue는 routing key 또는 routing pattern을 갖습니다. 
- Consumer: Queue에 쌓인 메시지를 꺼내서 처리합니다. 이때 가장 먼저 쌓인 메시지부터 꺼내서 처리합니다. Consumer가 꺼내어진 메시지를 성공적으로 처리하면 Queue로 ack를 전달하며, ack 응답을 받은 Queue는 메시지를 삭제한다. 

- Round Robin 와 Prefetch Count
	- Prefetch Count: 하나의 Consumer가 처리할 수 있는 메시지 양입니다.
- Message와 Queue 보존
	- RabbitMQ는 종료되면 Queue 안에 있는 메시지가 모두 삭제됩니다. 
	- 그런데 publisher가 message를 exchange로 보낼 때 persistent 속성을 부여하고, Queue를 선언할 때 durable 속성을 true로 설정하면 메시지를 보존할 수 있다. 
	- 메시지가 회복될 때 이미 저장이 되어 있다면, 기존 메시지는 삭제되면서 메시지 회복이 이루어집니다. 
	
### 참고 자료
- https://gwonbookcase.tistory.com/49
- https://armful-log.tistory.com/61
- https://jithub.tistory.com/443
- https://www.rabbitmq.com/queues.html