# Reactive Programming 2차 발표

---

# Reactive Programming

- Reactive
    - 반응을 잘하는 사람
- Reactive System
    - 반응을 잘하는 시스템
    - Reactive 매니페스토
        - 반응 잘하는 시스템 만들기 위한 원칙
- 특징
    - Non Blocking I/O
    - 선언적 프로그래밍
        - 명령형 VS 선언형 프로그래밍
            - 명령형(How)
                - "저기 Gone Fishin' 이라고 적힌 표지판 아래에 있는 테이블이 비어있네요. 우리는 저기로 걸어가서 저 테이블에 앉도록 하겠습니다."
                - 어떻게 처리하는지 보여줌
                
                ```jsx
                function double (arr) {
                  let results = []
                  for (let i = 0; i < arr.length; i++){
                    results.push(arr[i] * 2)
                  }
                  return results
                }
                
                function add (arr) {
                  let result = 0
                  for (let i = 0; i < arr.length; i++){
                    result += arr[i]
                  }
                  return result
                ```
                
            - 선언형(What)
                - “2명 자리 주세요”
                - 무엇을 처리할 것인지 보여줌
                
                ```jsx
                function double (arr) {
                  return arr.map((item) => item * 2)
                }
                
                function add (arr) {
                  return arr.reduce((prev, current) => prev + current, 0)
                }
                ```
                
        - 데이터 스트림
            - 데이터가 흐름으로 즉 지속적으로 발생함을 의미
    - 리액티브 프로그래밍 코드 구성
        - Publisher
            - **데이터를 발행, 제공, 방출(emit)**
        - Subscriber
            - 데이터를 전달받아 사용(Consumer)
        - Data Source
            - 원본 데이터
            - Data Stream이라고도 함.
        - Operator
            - data stream을 가공 처리

## 필요 사전 지식

- 함수형 인터페이스
    
    ```jsx
    Collections.sort(cryptoCurrencies, new Comparator<CryptoCurrency>() {
    	@Override
    	public int compare(CryptoCurrency cc1, CryptoCurrency cc2) {
    		return cc1.getUnit().name().compareTo(cc2.getUnit().name());
    	}
    });
    ```
    
- 람다표현식
    
    ```jsx
    (String a, String b) -> a.equals(b)
    ```
    
- 메서드 레퍼런스
    
    ```jsx
    (Car car) -> car.getName() // 메서드 레퍼런스 적용 전
    
    (Car car) -> Car::getCarName // 메서드 레퍼런스 적용 후
    ```
    
- 함수 디스크립터
    - Predicate<T>
    - Consumer<T>
    - Function<T,R>
    - 등
        - 설명 생략

---

# 리액티브 스트림즈

- 데이터 스트림을 비동기 Non Blocking 방식으로 처리하기 위한 라이브러리 표준
- 구현 API 컴포넌트
    - Publisher
    - Subscriber
    - Subscription
    - Processor
- 동작 방식 → 핵심은 subscribe

![Untitled](Reactive%20Programming%202%E1%84%8E%E1%85%A1%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208c1fdce8f10f4d8d908f8d9924ce2c93/Untitled.png)

- 용어 정의
    - Signal
        - Publisher와 Subscriber사이에 주고 받는 신호
        - ex. onComplete(), onError()
    - Demand
        - Subscriber가 Publisher에 요청하는 데이터
    - Emit
        - 데이터가 전달될때 방출 되는 경우
    - Upstream/DownStream
        - 메서드 체인 에서 위와 아래
        
        ```jsx
        public class Example {
        	public static void main(String[] args) {
        		Flux
        			.just(1, 2, 3, 4, 5)
        			.filter(n -> n % 2 == 0) //이거 입장에서 위에는 Upstream, 아래는 downStream
        			.map(n -> n * 2)
        			.subscribe(System.out::println)
        	}
        }
        ```
        
    - Sequence
        - 위에 코드 보면 Flux.just부터 데이터가 방출되어 map으로 해서 연산되는 부분 이것을 Sequence라고 함.
        - 다양한 Operator로 데이터의 연속적인 흐름을 정의한 것.
    - Operator
        - just, filter, map
    - Source
        - 원본 데이터
- 적합한 시스템
    - 대량의 요청 트래픽이 발생하는 시스템
    - 마이크로 서비스 기반 시스템
    - 스트리밍 또는 실시간 시스템

---

# Project Reactor

- 리액티브 스트림즈 구현체

## Mono 와 Flux

- Reactor에서 사용 하는 Publisher
- subscribe를 사용해서 데이터 소비
- 마블 다이어 그램 사용해야 연산자 이해가 쉬움
- Mono
    - 1개의 데이터만 Emit 하는 Publisher
    
    ![Untitled](Reactive%20Programming%202%E1%84%8E%E1%85%A1%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208c1fdce8f10f4d8d908f8d9924ce2c93/Untitled%201.png)
    
    - 예제
        
        ```java
        public class Example6_1 {
            public static void main(String[] args) {
                Mono.just("Hello Reactor")
                        .subscribe(System.out::println);
            }
        }
        ```
        
- Flux
    - 여러개의 데이터를 emit 할수있는 Publisher 타입
        
        ![Untitled](Reactive%20Programming%202%E1%84%8E%E1%85%A1%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208c1fdce8f10f4d8d908f8d9924ce2c93/Untitled%202.png)
        
    - 예제
        
        ```java
        public class Example6_4 {
            public static void main(String[] args) {
                Flux.just(6, 9, 13)
                        .map(num -> num % 2)
                        .subscribe(System.out::println);
            }
        }
        
        public class Example6_5 {
            public static void main(String[] args) {
                Flux.fromArray(new Integer[]{3, 6, 7, 9}) //배열 데이터 처리 위함
                        .filter(num -> num > 6)
                        .map(num -> num * 2)
                        .subscribe(System.out::println);
            }
        }
        ```
        
- 문제
    
    ```java
    public class Example6_7 {
        public static void main(String[] args) {
            Flux.concat(
                            Flux.just("Mercury", "Venus", "Earth"),
                            Flux.just("Mars", "Jupiter", "Saturn"),
                            Flux.just("Uranus", "Neptune", "Pluto"))
                    .collectList()
                    .subscribe(planets -> System.out.println(planets));
        }
    }
    ```
    
    1. concat() operator에서 리턴하는 Publisher는 Mono or Flux?
        - 해답
            
            Flux
            
    2. collectList() operator 에서 리턴하는 Publisher는 Mono or Flux?
        - 해답
            
            Mono
            
    3. 8번 라인 최종 데이터 형태
        - 해답
            
            3개가 List 형태로
            

## Cold Sequeunce와 Hot Sequence

- Cold
    - 새로시작 O
    - 잡지 구독
        - 5월에 해도 1월 부터 다줌
    
    ```java
    @Slf4j
    public class Example7_1 {
        public static void main(String[] args) throws InterruptedException {
    
            Flux<String> coldFlux =
                    Flux
                        .fromIterable(Arrays.asList("KOREA", "JAPAN", "CHINESE"))
                        .map(String::toLowerCase);
    
            coldFlux.subscribe(country -> log.info("# Subscriber1: {}", country));
            System.out.println("----------------------------------------------------------------------");
            Thread.sleep(2000L);
            coldFlux.subscribe(country -> log.info("# Subscriber2: {}", country));
        }
    }
    
    10:27:34.927 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    10:27:34.941 [main] INFO com.example.reactorstudy.ShareExample - # Subscriber1: korea
    10:27:34.942 [main] INFO com.example.reactorstudy.ShareExample - # Subscriber1: japan
    10:27:34.942 [main] INFO com.example.reactorstudy.ShareExample - # Subscriber1: chinese
    ----------------------------------------------------------------------
    10:27:36.949 [main] INFO com.example.reactorstudy.ShareExample - # Subscriber2: korea
    10:27:36.949 [main] INFO com.example.reactorstudy.ShareExample - # Subscriber2: japan
    10:27:36.949 [main] INFO com.example.reactorstudy.ShareExample - # Subscriber2: chinese
    
    Process finished with exit code 0
    ```
    
- Hot
    - 새로시작 X
    - 잡지 구독
        - 5월에 해도 5월꺼만 줌
    - share() operator로 가능
    
    ```java
    @Slf4j
    public class Example7_2 {
        public static void main(String[] args) throws InterruptedException {
            String[] singers = {"Singer A", "Singer B", "Singer C", "Singer D", "Singer E"};
    
            log.info("# Begin concert:");
            Flux<String> concertFlux =
                    Flux
                        .fromArray(singers)
                        .delayElements(Duration.ofSeconds(1))
                        .share();
    
            concertFlux.subscribe(
                    singer -> log.info("# Subscriber1 is watching {}'s song", singer)
            );
    
            Thread.sleep(2500);
    
            concertFlux.subscribe(
                    singer -> log.info("# Subscriber2 is watching {}'s song", singer)
            );
    
            Thread.sleep(3000);
        }
    }
    
    10:28:43.883 [main] INFO com.example.reactorstudy.HotSequenceExample - # Begin concert:
    10:28:44.066 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    10:28:45.174 [parallel-1] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber1 is watching Singer A's song
    10:28:46.180 [parallel-2] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber1 is watching Singer B's song
    10:28:47.186 [parallel-3] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber1 is watching Singer C's song
    10:28:47.187 [parallel-3] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber2 is watching Singer C's song
    10:28:48.192 [parallel-4] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber1 is watching Singer D's song
    10:28:48.192 [parallel-4] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber2 is watching Singer D's song
    10:28:49.196 [parallel-5] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber1 is watching Singer E's song
    10:28:49.196 [parallel-5] INFO com.example.reactorstudy.HotSequenceExample - # Subscriber2 is watching Singer E's song
    ```
    

## Backpressure

- Reactor에서 데이터 개수 제어를 위함

```java
@Slf4j
public class Example8_1 {
    public static void main(String[] args) {
        Flux.range(1, 5)
            .doOnRequest(data -> log.info("# doOnRequest: {}", data))
            .subscribe(new BaseSubscriber<Integer>() {
                @Override
                protected void hookOnSubscribe(Subscription subscription) {
                    request(1);
                }

                @SneakyThrows
                @Override
                protected void hookOnNext(Integer value) {
                    Thread.sleep(2000L);
                    log.info("# hookOnNext: {}", value);
                    request(1);
                }
            });
    }
}

10:31:43.809 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
10:31:43.889 [main] INFO com.example.reactorstudy.BackPressureExample - # doOnRequest: 1
10:31:45.952 [main] INFO com.example.reactorstudy.BackPressureExample - # hookOnNext: 1
10:31:45.952 [main] INFO com.example.reactorstudy.BackPressureExample - # doOnRequest: 1
10:31:47.958 [main] INFO com.example.reactorstudy.BackPressureExample - # hookOnNext: 2
10:31:47.958 [main] INFO com.example.reactorstudy.BackPressureExample - # doOnRequest: 1
10:31:49.963 [main] INFO com.example.reactorstudy.BackPressureExample - # hookOnNext: 3
10:31:49.963 [main] INFO com.example.reactorstudy.BackPressureExample - # doOnRequest: 1
10:31:51.968 [main] INFO com.example.reactorstudy.BackPressureExample - # hookOnNext: 4
10:31:51.969 [main] INFO com.example.reactorstudy.BackPressureExample - # doOnRequest: 1
10:31:53.974 [main] INFO com.example.reactorstudy.BackPressureExample - # hookOnNext: 5
10:31:53.974 [main] INFO com.example.reactorstudy.BackPressureExample - # doOnRequest: 1

Process finished with exit code 0
```

- 전략
    - IGNORE
    - ERROR
    - DROP
    - LATEST
    - BUFFER

## Sinks

- 리액티브 스트림즈의 Signal(ex. onError, onComplete, onSubscribe) 등을 명시적으로 사용 가능함.
- 일일이 체크 가능하기에 스레드 안정성올리기 가능

```java
@Slf4j
public class Example9_1 {
    public static void main(String[] args) throws InterruptedException {
        int tasks = 6;
        Flux
            .create((FluxSink<String> sink) -> {
                IntStream
                        .range(1, tasks)
                        .forEach(n -> sink.next(doTask(n)));
            })
            .subscribeOn(Schedulers.boundedElastic())
            .doOnNext(n -> log.info("# create(): {}", n))
            .publishOn(Schedulers.parallel())
            .map(result -> result + " success!")
            .doOnNext(n -> log.info("# map(): {}", n))
            .publishOn(Schedulers.parallel())
            .subscribe(data -> log.info("# onNext: {}", data));

        Thread.sleep(500L);
    }

    private static String doTask(int taskNumber) {
        // now tasking.
        // complete to task.
        return "task " + taskNumber + " result";
    }
}
```

## Scheduler

- Reactor Sequence 스레드의 동작을 관리해주는 관리자
- 스레드의 개념
    - 물리적 스레드
        - 실제 코어 수와 비례
        - 병렬성
    - 논리적 스레드
        - 빈공간에 얼마든지 만들수 있음
        - 동시성
            - 동시처리가 아니라 동시에 실행되는것처럼 보이는 거임
- 예제 (subscribeOn)
    - 구독 이 일어난 직후 실행
    
    ```java
    @Slf4j
    public class Example10_1 {
        public static void main(String[] args) throws InterruptedException {
            Flux.fromArray(new Integer[] {1, 3, 5, 7})
                    .subscribeOn(Schedulers.boundedElastic())
                    .doOnNext(data -> log.info("# doOnNext: {}", data))
                    .doOnSubscribe(subscription -> log.info("# doOnSubscribe"))
                    .subscribe(data -> log.info("# onNext: {}", data));
    
            Thread.sleep(500L);
        }
    }
    
    10:38:10.584 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    10:38:10.704 [main] INFO com.example.reactorstudy.SchedulerExample - # doOnSubscribe
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # doOnNext: 1
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # onNext: 1
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # doOnNext: 3
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # onNext: 3
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # doOnNext: 5
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # onNext: 5
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # doOnNext: 7
    10:38:10.706 [boundedElastic-1] INFO com.example.reactorstudy.SchedulerExample - # onNext: 7
    
    ```
    
- 예제 (publishOn)
    - publishOn operator가 Downstream으로 Signal 전달 시 실행
- 예제 ( parallel )
    
    ```java
    @Slf4j
    public class Example10_3 {
        public static void main(String[] args) throws InterruptedException {
            Flux.fromArray(new Integer[]{1, 3, 5, 7, 9, 11, 13, 15, 17, 19})
                    .parallel(4) // or parallel() -> 자동으로 병렬 처리
                    .runOn(Schedulers.parallel())
                    .subscribe(data -> log.info("# onNext: {}", data));
    
            Thread.sleep(100L);
        }
    }
    
    10:39:11.014 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    10:39:11.045 [parallel-3] INFO com.example.reactorstudy.Parallel - # onNext: 5
    10:39:11.045 [parallel-1] INFO com.example.reactorstudy.Parallel - # onNext: 1
    10:39:11.047 [parallel-3] INFO com.example.reactorstudy.Parallel - # onNext: 13
    10:39:11.047 [parallel-1] INFO com.example.reactorstudy.Parallel - # onNext: 9
    10:39:11.047 [parallel-1] INFO com.example.reactorstudy.Parallel - # onNext: 17
    10:39:11.045 [parallel-2] INFO com.example.reactorstudy.Parallel - # onNext: 3
    10:39:11.045 [parallel-4] INFO com.example.reactorstudy.Parallel - # onNext: 7
    10:39:11.047 [parallel-2] INFO com.example.reactorstudy.Parallel - # onNext: 11
    10:39:11.047 [parallel-2] INFO com.example.reactorstudy.Parallel - # onNext: 19
    10:39:11.047 [parallel-4] INFO com.example.reactorstudy.Parallel - # onNext: 15
    
    Process finished with exit code 0
    ```
    

## Debugging

- Debug Mode 사용
    - Hooks.onOperatorDebug()
- checkpoint() operator 사용
- log() Operator 사용

## Testing

- StepVerifier 사용
- TestPublisher 사용
- PublisherProbe 사용

## Operators

### Sequence 생성을 위한 OPERATOR

- just()
- justOrEmpty()
    - null일 경우  NPE 발생 X
    
    ```java
    @Slf4j
    public class Example14_1 {
        public static void main(String[] args) {
            Mono
                .justOrEmpty(null)
                .subscribe(data -> {},
                        error -> {},
                        () -> log.info("# onComplete"));
        }
    }
    
    10:40:08.395 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    10:40:08.401 [main] INFO com.example.reactorstudy.JustOrEmpty - # onComplete
    
    Process finished with exit code 0
    ```
    
- fromiterable()
    - Iterable에 포함된 데이터를  emit하는 Flux를 생성
    - Java에서 Iterable은 for-each 루프 또는 Iterator를 사용하여 반복하거나 반복할 수 있는 개체 컬렉션을 나타내는 인터페이스
    
    ```java
    @Slf4j
    public class Example14_2 {
        public static void main(String[] args) {
            Flux
                    .fromIterable(SampleData.coins)
                    .subscribe(coin ->
                            log.info("coin 명: {}, 현재가: {}", coin.getT1(), coin.getT2())
                    );
        }
    }
    ```
    
- fromStream()
- range()
- defer()
- using()
- generate()
- create()

### Sequence 필터링 위한 OPERATOR

- filter()
    
    ```java
    public class Example14_15 {
        public static void main(String[] args) {
            Flux
                .range(1, 20)
                .filter(num -> num % 2 != 0)
                .subscribe(data -> log.info("# onNext: {}", data));
        }
    }
    
    10:42:05.407 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    10:42:05.411 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 1
    10:42:05.411 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 3
    10:42:05.411 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 5
    10:42:05.411 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 7
    10:42:05.411 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 9
    10:42:05.411 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 11
    10:42:05.411 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 13
    10:42:05.412 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 15
    10:42:05.412 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 17
    10:42:05.412 [main] INFO com.example.reactorstudy.FilterExample - # onNext: 19
    
    ```
    
- skip
    - 개수만큼 스킵
- take
    - 개수만큼 가져감 (추출)
- next
    - 첫번째 데이터만 방출

### Sequence 변환 Operator

- map()
    
    ```java
    @Slf4j
    public class Example14_27 {
        public static void main(String[] args) {
            Flux
                .just("1-Circle", "3-Circle", "5-Circle")
                .map(circle -> circle.replace("Circle", "Rectangle"))
                .subscribe(data -> log.info("# onNext: {}", data));
        }
    }
    ```
    
- flatMap
    - 마블 다이어그램
    - map 과의 차이
        - 비동기적 처리에는 flatMap 그러나 Mono일경우에는 상관 없음
            
            
            | Publisher | method |  |
            | --- | --- | --- |
            | Mono | map | Transform the item emitted by this http://eclipse-javadoc:%E2%98%82=brick-modules-autoconfigure/C:%5C/Users%5C/bluesky%5C/.m2%5C/repository%5C/io%5C/projectreactor%5C/reactor-core%5C/3.2.13.RELEASE%5C/reactor-core-3.2.13.RELEASE.jar%3Creactor.core.publisher(Mono.class%E2%98%83Mono~map~Ljava.util.function.Function%5C%3C-TT;+TR;%3E;%E2%98%82Mono by applying a synchronous function to it. |
            |  | flatMap | Transform the item emitted by this http://eclipse-javadoc:%E2%98%82=brick-modules-autoconfigure/C:%5C/Users%5C/bluesky%5C/.m2%5C/repository%5C/io%5C/projectreactor%5C/reactor-core%5C/3.2.13.RELEASE%5C/reactor-core-3.2.13.RELEASE.jar%3Creactor.core.publisher(Mono.class%E2%98%83Mono~flatMap~Ljava.util.function.Function%5C%3C-TT;+Lreactor.core.publisher.Mono%5C%3C+TR;%3E;%3E;%E2%98%82Mono asynchronously, returning the value emitted by another http://eclipse-javadoc:%E2%98%82=brick-modules-autoconfigure/C:%5C/Users%5C/bluesky%5C/.m2%5C/repository%5C/io%5C/projectreactor%5C/reactor-core%5C/3.2.13.RELEASE%5C/reactor-core-3.2.13.RELEASE.jar%3Creactor.core.publisher(Mono.class%E2%98%83Mono~flatMap~Ljava.util.function.Function%5C%3C-TT;+Lreactor.core.publisher.Mono%5C%3C+TR;%3E;%3E;%E2%98%82Mono (possibly changing the value type). |
            | Flux | map | Transform the items emitted by this http://eclipse-javadoc:%E2%98%82=brick-modules-autoconfigure/C:%5C/Users%5C/bluesky%5C/.m2%5C/repository%5C/io%5C/projectreactor%5C/reactor-core%5C/3.2.13.RELEASE%5C/reactor-core-3.2.13.RELEASE.jar%3Creactor.core.publisher(Flux.class%E2%98%83Flux~map~Ljava.util.function.Function%5C%3C-TT;+TV;%3E;%E2%98%82Flux by applying a synchronous function to each item. |
            |  | flatMap | Transform the elements emitted by this http://eclipse-javadoc:%E2%98%82=brick-modules-autoconfigure/C:%5C/Users%5C/bluesky%5C/.m2%5C/repository%5C/io%5C/projectreactor%5C/reactor-core%5C/3.2.13.RELEASE%5C/reactor-core-3.2.13.RELEASE.jar%3Creactor.core.publisher(Flux.class%E2%98%83Flux~flatMap~Ljava.util.function.Function%5C%3C-TT;+Lorg.reactivestreams.Publisher%5C%3C+TR;%3E;%3E;%E2%98%82Flux asynchronously into Publishers, then flatten these inner publishers into a single http://eclipse-javadoc:%E2%98%82=brick-modules-autoconfigure/C:%5C/Users%5C/bluesky%5C/.m2%5C/repository%5C/io%5C/projectreactor%5C/reactor-core%5C/3.2.13.RELEASE%5C/reactor-core-3.2.13.RELEASE.jar%3Creactor.core.publisher(Flux.class%E2%98%83Flux~flatMap~Ljava.util.function.Function%5C%3C-TT;+Lorg.reactivestreams.Publisher%5C%3C+TR;%3E;%3E;%E2%98%82Flux through merging, which allow them to interleave |
    
    ![Untitled](Reactive%20Programming%202%E1%84%8E%E1%85%A1%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208c1fdce8f10f4d8d908f8d9924ce2c93/Untitled%203.png)
    
    - 예제
        
        ```java
        @Slf4j
        public class Example14_29 {
            public static void main(String[] args) {
                Flux
                    .just("Good", "Bad")
                    .flatMap(feeling -> Flux
                                            .just("Morning", "Afternoon", "Evening")
                                            .map(time -> feeling + " " + time))
                    .subscribe(log::info);
            }
        }
        
        10:52:46.734 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
        10:52:46.761 [main] INFO com.example.reactorstudy.FlatMapExmpl - Good Morning
        10:52:46.762 [main] INFO com.example.reactorstudy.FlatMapExmpl - Good Afternoon
        10:52:46.762 [main] INFO com.example.reactorstudy.FlatMapExmpl - Good Evening
        10:52:46.762 [main] INFO com.example.reactorstudy.FlatMapExmpl - Bad Morning
        10:52:46.762 [main] INFO com.example.reactorstudy.FlatMapExmpl - Bad Afternoon
        10:52:46.762 [main] INFO com.example.reactorstudy.FlatMapExmpl - Bad Evening
        
        ```
        
- concat()
    
    ```java
    public class Example14_31 {
        public static void main(String[] args) {
            Flux
                .concat(Flux.just(1, 2, 3), Flux.just(4, 5))
                .subscribe(data -> log.info("# onNext: {}", data));
        }
    }
    ```
    
- merge()
    
    ```java
    @Slf4j
    public class Example14_33 {
        public static void main(String[] args) throws InterruptedException {
            Flux
                .merge(
                        Flux.just(1, 2, 3, 4).delayElements(Duration.ofMillis(300L)),
                        Flux.just(5, 6, 7).delayElements(Duration.ofMillis(500L))
                )
                .subscribe(data -> log.info("# onNext: {}", data));
    
            Thread.sleep(2000L);
        }
    }
    ```
    
- **zip**
    - 각 Publisher가 1개씩 데이터를 EMIT 하길 기다렸따가 결합
    
    ![Untitled](Reactive%20Programming%202%E1%84%8E%E1%85%A1%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208c1fdce8f10f4d8d908f8d9924ce2c93/Untitled%204.png)
    
    - 예제
    
    ```java
    @Slf4j
    public class Example14_35 {
        public static void main(String[] args) throws InterruptedException {
            Flux
                .zip(
                        Flux.just(1, 2, 3).delayElements(Duration.ofMillis(300L)),
                        Flux.just(4, 5, 6).delayElements(Duration.ofMillis(500L))
                )
                .subscribe(tuple2 -> log.info("# onNext: {}", tuple2));
    
            Thread.sleep(2500L);
        }
    }
    
    00:14:11.354 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    00:14:11.935 [parallel-2] INFO com.example.reactorstudy.ZipExample - # onNext: [1,4]
    00:14:12.442 [parallel-4] INFO com.example.reactorstudy.ZipExample - # onNext: [2,5]
    00:14:12.946 [parallel-6] INFO com.example.reactorstudy.ZipExample - # onNext: [3,6]
    ```
    
- and
    
    ```java
    @Slf4j
    public class AndExample {
    
      public static void main(String[] args) throws InterruptedException {
        Mono
            .just("Task 1")
            .delayElement(Duration.ofSeconds(1))
            .doOnNext(data -> log.info("# Mono doOnNext: {}", data))
            .and(
                Flux
                    .just("Task 2", "Task 3")
                    .delayElements(Duration.ofMillis(600))
                    .doOnNext(data -> log.info("# Flux doOnNext: {}", data))
            )
            .subscribe(
                data -> log.info("# onNext: {}", data),
                error -> log.error("# onError:", error),
                () -> log.info("# onComplete")
            );
    
        Thread.sleep(5000);
      }
    }
    
    00:17:18.177 [main] DEBUG reactor.util.Loggers - Using Slf4j logging framework
    00:17:18.864 [parallel-2] INFO com.example.reactorstudy.AndExample - # Flux doOnNext: Task 2
    00:17:19.261 [parallel-1] INFO com.example.reactorstudy.AndExample - # Mono doOnNext: Task 1
    00:17:19.471 [parallel-3] INFO com.example.reactorstudy.AndExample - # Flux doOnNext: Task 3
    00:17:19.471 [parallel-3] INFO com.example.reactorstudy.AndExample - # onComplete
    ```
    
- collectList()
    
    ![Untitled](Reactive%20Programming%202%E1%84%8E%E1%85%A1%20%E1%84%87%E1%85%A1%E1%86%AF%E1%84%91%E1%85%AD%208c1fdce8f10f4d8d908f8d9924ce2c93/Untitled%205.png)
    
    ```java
    @Slf4j
    public class Example14_40 {
        public static void main(String[] args) {
            Flux
                .just("...", "---", "...")
                .map(code -> transformMorseCode(code))
                .collectList()
                .subscribe(list -> log.info(list.stream().collect(Collectors.joining())));
        }
    
        public static String transformMorseCode(String morseCode) {
            return SampleData.morseCodeMap.get(morseCode);
        }
    }
    
    23:14:15 [main] INFO - sos
    ```
    
- collectMap()
    - 동일
- 기타 등등… 넘 많아….
    - 나머지는 찾아보세요…

---

# Spring WebFlux

- Servlet Stack
    - Servlet Containers
    - Servlet API
    - Spring Security
    - Spring MVC
    - Spring Data Repositorie
        - JDBC, JPA, NoSQL
- Reactive Stack
    - Netty, Servlet 3.1+ Containers
    - Reactive Streams Adapter
    - Spring Security Reactive
    - Spring WebFlux
    - Spring Data Reactive Repositories
        - R2DBC, Mongo, Redis, Cassandra, Couchbase
- 비동기 Non Blocking
- Netty
    
    ```java
    Thread = core 수 * 2
    ```
    
    - 이벤트 루프 모델
- AnnotatedController

```java
@RestController
@RequestMapping("/v1/controller/books")
public class BookController {
    @GetMapping("/{book-id}")
    public Mono<BookDto.Response> getBook(@PathVariable("book-id") long bookId) {
        return Mono.just(BookDto.Response.builder()
                .bookId(bookId)
                .bookName("Advanced Java")
                .author("Kevin")
                .isbn("111-11-1111-111-1").build());
    }
}
```

## finale-ncp-api 뽀개기

```java
@Operation(summary = "subnet validation", description = "중복 체크 통과시 true 실패시 false")
    @Parameters({
            @Parameter(name = "providerId", description = "워크스페이스 아이디", in = ParameterIn.HEADER, required = true),
            @Parameter(name = "regionCode", description = "리전코드", in = ParameterIn.HEADER, required = true),
            @Parameter(name = "subnetTypeCode", description = "public 일경우 PUBLIC 나머지 null 이나 빈 데이터로 " , in = ParameterIn.QUERY , required = false)

    })
    @GetMapping
    public Mono<ResponseEntity<Response<List<InquirySubnet>>>> subnetList(@RequestHeader("providerId") String providerId,
            @RequestHeader(value = "userId", required = false) String userId,
            @RequestParam(value = "regionCode", required = false) String regionCode,
            @RequestParam(value = "subnetNoList", required = false)List<String> subnetNoList, @RequestParam(value = "subnetName", required = false) String subnetName, @RequestParam(value = "subnet", required = false)String subnet, @RequestParam(value = "subnetTypeCode", required = false)String subnetTypeCode,
            @RequestParam(value = "usageTypeCode", required = false) String usageTypeCode, @RequestParam(value = "networkAclNo", required = false) String networkAclNo, @RequestParam(value = "subnetStatusCode", required = false) String subnetStatusCode, @RequestParam(value = "vpcNo", required = false) String vpcNo,
            @RequestParam(value = "zoneCode", required = false) String zoneCode,
            @PageableDefault(page = 0, size = 10) @Parameter(hidden = true) Pageable pageable) {

            return inquirySubnetUseCase.getSubnetList(providerId,userId, regionCode,subnetNoList,subnetName,subnet,subnetTypeCode,usageTypeCode,networkAclNo,subnetStatusCode,vpcNo,zoneCode,pageable)
                .map(customSubnets -> ResponseAdapter.pageResponse(HTTPResponse.OK, customSubnets));
    }
```

- getSubnetList

```java
@Override
    public Mono<Page<InquirySubnet>> getSubnetList(String providerId, String userId, String regionCode, List<String> subnetNoList, String subnetName, String subnetCidr, String subnetTypeCode, String usageTypeCode, String networkAclNo, String subnetStatusCode, String vpcNo, String zoneCode, Pageable pageable) {

        return keyProviderPort.build(providerId)

                .flatMap(keyProvider -> {
                    Mono<List<Subnet>> subnetListMono = inquirySubnetPort.getSubnetList(keyProvider, regionCode, subnetNoList, subnetName, subnetCidr, subnetTypeCode, usageTypeCode, networkAclNo, subnetStatusCode, vpcNo, zoneCode)
                            .map(InquirySubnetListResponse::getSubnetList);
                    Mono<List<Vpc>> vpcListMono = inquiryVpcPort.getVpcListMono(keyProvider, regionCode);
                    Mono<List<NetworkAcl>> acgListMono = inquiryNetworkAclPort.getNetworkAcl(keyProvider, regionCode);

                    return Mono.zip(subnetListMono, vpcListMono, acgListMono)
                            .map(monoZip -> {

                                List<InquirySubnet> inquirySubnetList = new ArrayList<>();
                                List<Subnet> subnetList = monoZip.getT1();
                                List<Vpc> vpcList = monoZip.getT2();
                                List<NetworkAcl> aclList = monoZip.getT3();

                                Map<String, String> vpcNameMap = new HashMap<>();
                                Map<String, String> acgNameMap = new HashMap<>();

                                for (Vpc vpc : vpcList)
                                    vpcNameMap.put(vpc.getVpcNo(), vpc.getVpcName());

                                for (NetworkAcl acl : aclList)
                                    acgNameMap.put(acl.getNetworkAclNo(), acl.getNetworkAclName());
                                for (Subnet subnetData : subnetList) {

                                    String vpcName = vpcNameMap.get(subnetData.getVpcNo());
                                    String acgName = acgNameMap.get(subnetData.getNetworkAclNo());

                                    InquirySubnet inquirySubnet = InquirySubnet.of(subnetData, vpcName, acgName);
                                    inquirySubnetList.add(inquirySubnet);
                                }
                                return inquirySubnetList;
                            });
                })
                .map(inquirySubnetList -> PageableUtils.getPageImpl(inquirySubnetList, pageable));
    }
```

- 이 코드를 봤을때 궁금증
    - Q. 여기서 sequence는 어디서 생성해주는 것?
        - Mono<List<Subnet>> subnetListMono = inquirySubnetPort.getSubnetList(keyProvider, regionCode, subnetNoList, subnetName, subnetCidr, subnetTypeCode, usageTypeCode, networkAclNo, subnetStatusCode, vpcNo, zoneCode)
                                    .map(InquirySubnetListResponse::getSubnetList);

```java
@Override
    public Mono<InquirySubnetListResponse> getSubnetList(KeyProvider keyProvider, String regionCode, List<String> subnetNoList, String subnetName, String subnetCidr, String subnetTypeCode, String usageTypeCode, String networkAclNo, String subnetStatusCode, String vpcNo, String zoneCode) {

        GetSubnetListRequest request = GetSubnetListRequest.builder()
                .regionCode(regionCode)
                .subnetName(subnetName)
                .subnet(subnetCidr)
                .vpcNo(vpcNo)
                .zoneCode(zoneCode)
                .build();

        if (!(subnetTypeCode == null || subnetTypeCode.equals("")))
            request = request.toBuilder().subnetTypeCode(subnetTypeCode).build();

        String uri = "/getSubnetList";

        return http.get(endPoint.getBase(),
                ENDPOINT_SUFFIX_VPC + uri,
                request,
                InquirySubnetListResponse.class, keyProvider);
    }
```

```java
public <T> Mono<T> get(String endpoint, String uri, Object payload, Class<T> responseClass, KeyProvider provider) {
        String queryPath = makeQueryPath(uri, payload);
        RequestHeadersSpec<?> spec = webClient.get().uri(endpoint + queryPath);
        return executeGet(spec, uri, responseClass, true, provider, queryPath);
    }
```

```java
private <T> Mono<T> executeGet(RequestHeadersSpec<?> spec, String uri, Class<T> responseClass, boolean isWrapped, KeyProvider provider, String queryPath) {
        return spec.httpRequest(request -> addAuthHeaderGet(request,provider, queryPath))
                .retrieve()
                .onStatus(HttpStatus.INTERNAL_SERVER_ERROR::equals,
                        response->{
                                throw new NcpServerErrorException("NCP API 호출 중 서버 에러 발생", uri);
                            })
                .bodyToMono(InputStreamResource.class)
                .flatMap(inputStreamResource -> ResponseSuccessHandler.apply(inputStreamResource, responseClass, unmarshaller, isWrapped));
    }
```

- bodyToMono , bodyToFlux
    - sequence 생성

[ResponseEntity :: Spring Framework](https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/responseentity.html)
