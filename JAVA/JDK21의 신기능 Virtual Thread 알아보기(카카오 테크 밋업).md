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
## Virtual Thread 구조
## 사용법
## 성능 테스트
## 생각해볼 점
## 마치며
