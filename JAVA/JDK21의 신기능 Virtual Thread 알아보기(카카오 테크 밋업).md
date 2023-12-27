# JDK21의 신기능 Virtual Thread 알아보기(카카오 테크 밋업)

[영상링크](https://www.youtube.com/watch?v=vQP6Rs-ywlQ)

새로운 것을 배우는 경험을 좋아하고 느리지만 하루하루 구덩이를 매꾸는 심정으로 매일 노력하는 평범한 개발자 라고 소개

## Virtual Thread란 무엇인가?

### JDK 21 
- 2023.09.19 JDK 21 LTS 출시
- 2023.10.04 Gradle 8.4
- 2023.11.01 Kotlin v1.9.20 부터 21바이트 코드 지원
- 2023.11.17 Spring 6.1
- 2023.11.23 Spring Boot 3.2 정식 지원
- 2023.12.07 Jetbrain Intellj 2023.3

### Vitual Thread 란?
- JDK 21 에 추가된 경량 스레드 OS 스레드를 그대로 사용하지 않고 JVM 내부 스케쥴링을 통해서 수십만~수백만개의 스레드를 동시에 사용할 수 있게 한다.

### 전통적이 Java의 Thread
- Java의 Thread는 OS Thread를 Wrapping 한 것(=Platform Thread)
- Java 애플리케이션에서 Thread를 사용하면 실제로는 OS Thread를 사용한 것
- OS Thread는 생성 갯수가 제한적이고 생성, 유지하는 비용이 비싸다.
- 이 때문에 애플리케이션에서는 플랫폼 스레드를 효율적으로 사용하기 위해 Thread Pool을 사용했다.

### Throughput
- 기본적인 Web Request 처리 방식은 THREAD per request 처리 방식
- 처리량을 높이려면 스레드 증가, BUT 무한정 늘릴 수 없다.

### Blocking I/O
- Thread에서 I/O 작업을 처리할 때 Blocking이 일어남.
- 작업을 처리하는 시간보다 대기하는 시간이 길다.

### Reactive Programming
- WebFlux 스레드를 대기하지 않고 다른 작업 처리 가능
- 코드를 작성하고 이해하는 비용이 높다.
- Reactive 하게 동작하는 라이브러리 지원을 필요로 한다.
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/45f092a9-775e-4cc7-b8d2-33af19e51a93)

-> 그럼 왜이렇게 차이가 나느냐, Java Design 생각

### Java Design 
- 자바의 디자인은 '스레드 중심'으로 구성되어 있다.
- EXCEPTION STACk Trace, Debugger, Profilling 모두 스레드 기반
- REACTIVE 할때 작업이 여러 스레드를 거쳐 처리되는데, 컨텍스트 확인이 어려워 디버깅이 어려움

## 해결하고자 하는 문제
1. 애플리케이션의 높은 처리량(THROUGhput) 확보
   - Blocking 발생 시 내부 스케쥴리을 통해 다른 작업을 처리
2. 자바 플랫폼의 디자인과 조화를 이루는 코드 생성
   - 기존 스레드 구조 그대로 사용

### Reactive Programming 과 비교
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/92614ad9-e327-46e6-a5be-cbe2b1299769)

## Virtual Thread 구조

### Platform Thread
- OS Thread와 1:1 Mapping
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/d15006db-e51d-4876-8739-f813382e625e)


### Virtual Thread
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/7b803cc6-eeca-495f-b070-e38c5e2b5c9d)
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/f59d834b-a1b4-484c-ab83-b4816a30dfce)
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/bb47cae6-80c1-4999-ab45-44572f5fa9a8)
- Blocking 이 발생하면 Vitual Thread가 Carrier Thread에서 Unmount / Mount 되게됌.

![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/8a1c6b43-d1b3-4a32-a825-dea87aa0c16f)

## 사용법
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/1e71d466-a449-45b4-bf85-c53fe605388b)
- 보통은 ExecutorService 사용, Executors.newVirtualThreadPerTaskExecutor()

### Spring Boot(MVC) 적용 법(3.2 이상)
```yaml
#application.yaml
spring:
   threads:
      virtual:
         enabled: true

```
### Spring Boot(MVC) 적용 법(3.X) -> 직접 Bean 등록
![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/eb02de8b-e3fb-4a68-bc90-9ba6b24c7c6e)

### 유의 사항 
1. Platform Thread => Virtual Thread (X) / Task => Virtual Thread
   - 리소스라고 생각하지 말고 TASK 별로 Virtual Thread 할당
2. Thread Local 사용 시 주의
   - Platform Thread Pool 을 사용할 때 공유를 위해 ThreadLocal 을 사용하던 관습
   - Virtual Thread는 Heap을 사용하기 때문에 이를 남발하면 메모리 사용이 늘어남
3. synchronized 사용 시 주의
   - synchronized 사용 시 Virtual Thread에 연결된 Carrier Thread가 Blocking 될 수 있으니 주의(이런 경우를 pinning 이라고 함)
     ![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/2fb843d3-8717-478d-a4bd-2d091f719229)

## 성능 테스트

### 성능 테스트 1 단순 Wait
- ![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/9f1be1b3-079d-4a76-9e51-9d4b525fd1f7)
- SPEC : 4 Core 8G Memory / Ubuntu 20 / Java 21 / Gradle 8.4 / Max Heap 2G
- ![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/a5b31957-300f-455d-a323-a3a0b74e5c01)

### 성능 테스트 2 DB직접 접근
- ![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/69bc56b6-6f91-4b07-aacc-b703399e9536)
- SPEC : 4 Core 8G Memory / Ubuntu 20 / Java 21 / Gradle 8.4 / Max Heap 2G
- ![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/e192ebfd-da55-4f02-ab5d-99637d47cd2b)
- ![image](https://github.com/rlarudgkswkd/TIL-repository/assets/48428850/8e75e6a9-0a9d-4b3c-9022-bb3d21fa93b4)

### 성능 테스트 해석
- T/O Blocking 이 발생하는 경우 Virtual Thread 더 좋은 처리량을 보여준다.
- Tomcat servlet O| virtual thread = (no pool, virtual thread per task) throughput 를 뒤로 넘길 때 DB Connection 을 가져오려다 Timeout.
  a. 이런경우를 Overwhelming 이라고 부름
  
- 기존 Thread pool 을 사용하던 이유
- Platform Thread 가 비쌈
- Throttle 역할도 수행

## 생각해볼 점

### 적합한 사용처
1. I/O Blocking이 발생하는 경우 Virtual Thread 가 적합합니다.
2. CPU Intensive 작업에는 적합하지 않습니다.
3. Spring MVC 기반 Web API 제공시 편리하게 사용할 수 있습니다.
    - 높은 throughput을 위해서 Webflux를 고려중이라면 대안이 될 수 있습니다.
  
### Virtual Thread 에 대한 오해
1. Virtual Thread는 기존 (Platform) Thread를 대체하는 것이 목적이 아닙니다.
2. Virtual Thread는 기다림에 대한 개선, 그리고 플랫폼 디자인과의 조화
3. 도입한다고 무조건 처리량이 높아지지 않습니다.
4. Virtual Thread는 그 자체로 Java의 동시성을 완전히 개선했다고 보기는 어렵습니다.

   => 상호 보완 되는 개념인거지 엄청난 기능 그런게 아님

### Virtual Thread 의 제약
1. Thread Pool 에 적합하지 않습니다. Task 별로 Virtual Thread를 할당하세요.
2. Thread Local 사용시 메모리 사용이 늘어날 수 있습니다.
3. synchronized 사용시 주의가 필요합니다. (carrier thread가 blocking 될 가능성 있습니다.)
- ReentrantLock 을 사용하세요.
4. 제한된 리소스의 경우 semaphore 를 사용하세요.

## 마치며

### 요약 
- Virtual Thread 가 더 좋은 이유는 0기다림에 대한 것
- Virtual Thread 는 Platform Thread 를 대체하려는 것이 아님, 둘다 사용가능
- Virtual Thread 가 추구하는 것은 '처리량을 증가시키면서도 기존 디자인과 조화를 이루는 것 처리량 증가는 "리액티브 프로그래밍"과 같지만 보다 더 가독성이 좋고, 개발자 친화적인 방법으로 이를 달성
- Spring Boot 3 에서 기존 코드 스타일 그대로 사용하면서 혜택을 누릴 수 있을 것
- Virtual Thread 가 동시성을 완전하게 개선했다고 보기는 어려움(제약이 있다)
