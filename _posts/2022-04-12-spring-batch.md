---
layout: post
title:  "Spring Batch란?"
date:   2022-04-12 16:00:00 +0900
categories: java
tags: java, spring-batch
comments: 1
---

## 배치 프로세싱이란?

일련의 작업을 정해진 로직으로 수행하는 것을 의미합니다.

## 배치 프로세싱이 사용되는 경우는?

- 대용량의 비즈니스 데이터를 복잡한 작업으로 처리해야 하는 경우
- 특정한 시점에 스케줄러를 통해 자동화된 작업이 필요한 경우 (예: 푸시알림, 월말 계산)
- 대용량 데이터의 포맷을 변경, 유효성 검사 등의 작업을 트랜잭션 안에서 처리 후 기록해야 하는 경우

## 스프링 배치란?

배치 프로세싱을 처리하는 것에 편의를 주는 프레임워크입니다.

## 스프링 배치의 배경

- 오픈 소스 소프트웨어 프로젝트 및 관련 커뮤니티가 웹 기반 및 마이크로서비스 기반 아키텍처에 관심이 큰 것에 반해, Java 기반 배치 처리를 수행할 수 있는 아키텍처 프레임워크에 대한 관심은 현저하게 낮았습니다. 
- 그런데 시간이 흘러, 배치 처리를 위해서 IT회사 내에서 배치 처리만을 위한 일회성 솔루션을 만들기 시작하면서 배치 처리 아키텍처 프레임워크의 수요가 늘어났습니다. 
- 그래서 SpringSource와 Accenture가 협력하여, Spring Batch를 만들었습니다. 

## 배치의 일반적인 사용 시나리오

![image](https://user-images.githubusercontent.com/88185304/162913934-65e17a4b-9ed4-4626-a8db-590162ea32de.png)

1. 데이터베이스, 파일 또는 큐에서 데이터 **읽기(Read)**
2. 데이터를 정의한 방식으로 **처리(Process)**
3. 처리된 데이터를 데이터 **쓰기(Write)**

스프링 배치는 위와 같은 방식으로 진행되며, (계속된 개발자의 대응 없이) 반복적으로 데이터를 트랜잭션 단위로 처리할 수 있도록 구혀되어 있습니다. 
그래서 개발자는 데이터 처리에 대한 비즈니스 로직에만 집중하여 배치 프로세스를 작성할 수 있습니다.

## 스프링 배치 계층 구조

![image](https://user-images.githubusercontent.com/88185304/162919477-d39fbd4c-a4d2-4f4b-9073-d5e8bbc74d75.png)

- Application: 개발자가 작성한 모든 배치 작업과 사용자 정의 코드 포함합니다.
- Batch Core: 배치 작업을 시작하고 제어하는데 필요한 핵심 런타임 클래스 포함합니다.
- Batch Infrastructure: 개발자와 어플리케이션에서 사용하는 일반적인 Reader, Writer, Retry Template와 같은 서비스를 포함합니다.

스프링 배치는 계층 구조가 위와 같이 설계되어 있기 때문에 **개발자는 Application 계층의 비즈니스 로직에 집중**할 수 있고, 
**배치의 동작과 관련된 것은 Batch Core**에 있는 클래스들을 이용하여 제어할 수 있습니다.

## Job, Step, Tasklet (간단한 스프링 배치 구조)

![image](https://user-images.githubusercontent.com/88185304/162923222-57afcd39-64c8-46f6-a562-e8d80cdbded3.png)

- Job: 배치 **처리 과정을 하나의 단위**로 만들어 표현한 객체이고 **여러 Step 인스턴스를 포함**하는 컨테이너입니다.
- Step: **실질적인 배치 처리를 정의**하고 제어 하는데 필요한 모든 정보가 있는 도메인 객체
- Tasklet: Step 안에서 수행될 비즈니스 로직 전략의 인터페이스

일반적으로 스프링 배치는 대용량 데이터를 다루는 경우가 많기 때문에 
Tasklet보다 상대적으로 트랜잭션의 단위를 짧게 하여 처리할 수 있는 Reader, Proccessor, Writer를 이용한 **Chunk 지향 프로세싱**을 이용합니다.

## 스프링 배치 구조

![image](https://user-images.githubusercontent.com/88185304/162932208-5b8a854f-ce5e-4474-a641-b1578ef86d11.png)

- Run Tier: 어플리케이션의 **예약 및 시작**과 관련이 있는 계층입니다. 배치 작업의 시간 기반 및 상호 의존적인 스케줄링을 제공할뿐만 아니라 병렬 처리 기능을 제공합니다.
- Job Tier: 배치 작업의 전체 **실행**을 담당합니다. **배치 단계를 순차적으로** 실행하여 모든 단계가 올바른 상태에 있고 모든 적절한 정책이 시행 되도록합니다. 
- Application Tier: 프로그램을 실행하는 데 필요한 구성 요소가 포함되어 있습니다. 이 계층에는 요구된 배치의 기능을 처리하고 Tasklet 실행에 대한 **정책**을 시행하는 특별한 Tasklet이 포함됩니다.
- Data Tier: 데이터베이스, 파일 또는 대기열을 포함할 수 있는 물리적 데이터 소스와의 연결을 제공합니다. 


### Job

- 간단 설명
	- Job은 전체 배치 프로세스를 캡슐화하는 엔터티입니다. 다른 스프링 프로젝트와 동일하게 XML 또는 Java로 설저알 수 있습니다. 
- Job 설정에 필요한 구성요소
	- 작업의 간단한 이름
	- Step 인스턴스의 정의 및 순서
	- 작업을 다시 시작할 수 있는지 여부
- JobRunner
	- **외부 요청에 의해 작업**을 실행하는 클래스입니다. 예를 들어, 쉘 스크립트와 같은 다양한 외부 트리거로 메소드 호출을 할 수 있는 등의 여러 구현 방법들이 있습니다. 그리고 JobLauncher의 인스턴스화를 수행합니다. 
- JobInstance
	- 배치 처리에서 Job이 실행될 때 하나의 논리적 작업 실행의 단위입니다. 
- JobExecution
	- JobInstance에 대한 한 번의 실행을 나타내는 객체입니다. 
	- JobExecution 속성들
		- satus: 실행의 상태를 나타내는 객체입니다. (BatchStatus.STARTED, BatchStatus.FAILED, BatchStatus.COMPLETED)
		- startTime: 작업이 시작되었을 때 현재 시스템 시간입니다.
		- endTime: 작업의 성공 여부와 관계 없이 작업이 끝났을 때의 현재 시스템 시간입니다.
		- exitStatus: 실행의 결과를 나타내는 상태로 호출 대상에게 반환할 exitCode를 포한하고 있어 중요한 속성입니다.
		- createTime: JobExecution이 처음 지속된 현재 시스템 시간입니다.
		- lastUPdated: JobExecution이 마지막으로 지속된 현재 시스템 시간입니다.
		- executionContext: 실행과 실행 사이에 유지되어야 하는 유저 정보를 포함하는 속성 데이터 더미입니다.
		- failureException: 작업 실행 중에 발생한 예외 목록이며, 작업 중 둘 이상의 예외가 발생할 경우 유용하게 사용할 수 있습니다.
- JobExecutionContext
	- Job 실행 중 개발자가 지속적으로 유지되었으면 하는 데이터를 저장하는 데이터 컬렉션입니다. 
	- 이 컬렉션은 프레임워크에 의해 유지 및 관리 되고 Job이 실행되는 중간 커밋지점에서 주기적으로 저장됩니다. 
- JobParameters
	- Job이 실행될 때 필요한 파라미터 셋을 가지고 있는 객체입니다.
	- JobParameters와 JobInstance는 1:1 관계이기 때문에 JobParameters는 JobInstance를 구분하는 기준이 되기도 합니다.
	- JobParameters를 정상적으로 사용하기 위해서는 JobScope 또는 StepScope를 함께 사용해야 합니다. 그 이유는 JobParameters는 Scope Bean의 생성 시점에만 함께 생성될 수 있기 때문입니다.
	- JobParameters로 사용가능한 데이터 타입: Long, Double, Date, String
	
### Step

- StepExecution
	- Job에 JobExecution Job실행 정보가 있다면 Step에는 StepExecution이라는 Step 실행 정보를 담는 객체입니다. 
	- StepExecution 속성들
		- status: 실행 상태를 나타내는 배치 상태 정보를 나타내는 객체입니다. (BatchStatus.STARTED, BatchStatus.FAILED, BatchStatus.COMPLETED)
		- starTime: 실행이 시작되었을 때, 현재 시스템 시간입니다.
		- endTime: 실행 성공 여부와 관계없이 작업이 끝났을 때의 현재 시스템 시간입니다. 
		- exitStatus: 실행 결과에 대한 종료 상태로 호출 대상에게 반환할 exitCode를 포함하고 있어 중요한 속성입니다. 
		- executionContext: 실행과 실행 사이에 유지되어야 하는 유저 정보를 포함하는 속성 데이터 더미입니다.
		- readCount: 성공적으로 읽은 아이템의 수입니다.
		- writeCount: 성공적으로 쓰여진 아이템의 수입니다. 
		- CommitCount: 해당 실행을 위해 커밋된 트랜젝션의 수입니다.
		- rollbackCount: Step에서 제어하는 비즈니스 트랜젝션이 롤백된 수입니다. 
		- readSkipCount: 읽기에 실패하여 건너 뛴 아이템의 수를 의미합니다. 
		- processSkipCount: 프로세스가 실패하여 건너 뛴 아이템의 수입니다. 
		- filterCount: ItemProcessor에 의해 필터링된 항목의 수 입니다. 
		- writeSkipCount: 쓰기에 실패하여 건너 뛴 아이템의 수입니다. 
- StepExecutionContext
	- JobExecution과 동일하게 동작하지만, 각 Step에 대해서 개발자가 지정한 데이터를 저장합니다. 
	
## JobRepository
	
- 배치 처리 과정 중 프레임워크에 사용하는 메타데이터 정보를 엑세스하는 퍼사드(facade) 객체입니다. Job이 실행되면서 생성되는 정보들을 JobRepository를 통해 저장되고 관리됩니다.
- 퍼사드 객체: 복잡한 소프트웨어 바깥쪽의 코드가 라이브러리의 안쪽 코드에 의존하는 일을 감소시켜 주고, 복잡한 소프트웨어를 사용 할 수 있게 간단한 인터페이스를 제공해줍니다.

## JobLauncher

- 작업의 시작과 실제 실행을 관리하는 인터페이스로 JobRunner에 의해 인스턴스화되고, JobParameters와 함께 배치를 실행하는 주체입니다. 

## JobLocator

- 파라미터로 전달된 작업에 대한 구현 계획(작업 스크립트)과 같은 정보를 가져오는 책임이 있는 클래스입니다. JobRunner와 함꼐 동작합니다. 

## Tasklet

- 배치의 특정 작업을 개발하 위해 Step의 기본 단위를 만들 수 있는 클래스입니다. 

## ItemReader

- 일반적으로 청크 구조에서 사용되는 컴포넌트로 배치 작업에서 모든 데이터에 대한 처리가 될 때까지 DB, File 등의 소스에서 반복적으로 읽는데 사용하는 인터페이스이니다. 프레임워크에서 Reader 셋을 제공하지만 필요한 경우 개발자가 직접 개발할 수 있습니다. 

## ItemProcessor

- 역시 청크 구조에서 사용되는 컴포넌트로 Reader로 읽어온 데이터를 변환하기 위한 역할을 수행합니다. 
- 경우에 따라 ItemProcessor는 사용하지 않아도 됩니다. 
- Reader, Proccessor, Writer로 분리된 구조는 비즈니스 로직을 분리하기에 용의하고, Reader와 Writer 데이터 타입이 다른 경우 어뎁터의 역할을 할 수 있습니다. 

## ItemWriter

- ItemWriter 또는 청크 구조에서 사용되는 컴포넌트로 배치에서 데이터를 DB, File 등에 저장하기 위한 인터페이스입니다. 동일하게 프레임워크에 기본적으로 제공하지만 개발자가 직접 개발할 수도 있습니다. 


### 맺음

주기적으로 실행해야하는 작업을 처리할 때 그 동안 파이썬의 스케줄러를 통해 처리하였다. 그런데 SpringBatch를 처음 알게 되었다. 
파이썬으로 처리할 때는 그냥 맨바닥에서 짠 코드이기 때문에 서로의 의존성이 컸다. 그런데 SpringBatch는 각각의 역할들이 잘 분배되어 있는 것 같다. 
이제 직접 batch를 만들어서 보면서 좀 더 알아보아야겠다. 


### 참고 자료
- https://docs.spring.io/spring-batch/docs/current/reference/html/spring-batch-intro.html
- https://deeplify.dev/back-end/spring/batch-tutorial
- https://deeplify.dev/back-end/spring/batch-architecture-and-components