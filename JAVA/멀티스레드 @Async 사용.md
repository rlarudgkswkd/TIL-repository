
# 멀티 스레드 @Async 사용기

## 사용계기
- 필자가 AWS sdk를 활용하여 각 리소스의 데이터를 가져와 대시보드에 보여주는 API를 개발
- 각각의 모듈을 불러오는 중, 속도 저하 문제 발생
- 멀티스레드로 구현을 위해 리서치 중 @Async 확인

## 개념
- Spring 에서 쓰레드 풀을 활용한 비동기 메소드를 지원
- 메소드에 @Async를 달아두면 비동기로 리턴하고 spring TaskExecutor로 새로운 스레드 실행

## 사용법
### 1. AsyncConfig.java 생성
```
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer { // 추가

    private int CORE_POOL_SIZE = 2;
    private int MAX_POOL_SIZE = 10;
    private int QUEUE_CAPACITY = 100_000;

    @Bean(name = "dashBoardExecutor")
    public Executor threadPoolTaskExecutor() {

        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();

        taskExecutor.setCorePoolSize( CORE_POOL_SIZE );
        taskExecutor.setMaxPoolSize( MAX_POOL_SIZE );
        taskExecutor.setQueueCapacity( QUEUE_CAPACITY );
        taskExecutor.setTaskDecorator( new CustomDecorator() ); // 데코레이터 적용
        taskExecutor.setThreadNamePrefix( "BoardExecutor-" );
        taskExecutor.setRejectedExecutionHandler( new ThreadPoolExecutor.CallerRunsPolicy() );

        return taskExecutor;
    }

    // override method
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new AsyncExceptionHandler(); // 추가
    }
}
```

### 2. 메서드에 @Async 추가
```
@Async("dashBoardExecutor")
public CompletableFuture<List<AwsAssetsVo>> getAllResource(long providerId) {
  //
  //내부 로직
  //
  return CompletableFuture.completedFuture(awsAssets);
}
```


## TroubleShooting
- 문제 : 아래와 같이 코드를 사용했을 경우 정상적으로 멀티스레드 구동 되지 않음
- 원인 : 로직 실행 후 .join() 하는 구문이 있을 경우 다중 실행 X
```
@GetMapping("/--- 예시 내용---")
public ResponseVo getAllAssetData(@PathVariable long 무언가) {
    CompletableFuture<List<AwsAssetsVo>> threadAwsAssetsVo = dashboardUserService.getAllResource(providerId);
    List<AwsAssetsVo> awsAssetsVoList = threadAwsAssetsVo.join();
    CompletableFuture<UpdatedResourceVo> threadUpdatedResourceVo = dashboardUserService.getUpdatedResource(providerId);
    UpdatedResourceVo updatedResourceVo = threadUpdatedResourceVo.join();

    AssetDataVo assetData = AssetDataVo.builder()
            .awsAssetsVo(awsAssetsVoList)
            .updatedResourceVo(updatedResourceVo)
            .build();

    ResponseVo result = new ResponseVo();
    result.setResult(assetData);
    return ok(result);
}
```

- 결과 : 코드 수정 및 제네릭 사용하여 공통으로 메서드 구현
```
@GetMapping("/--- 예시 내용---")
public ResponseVo getAllAssetData(@PathVariable long 무언가) {
    List<AwsAssetsVo> awsAssetsVoList = joinCompletableFuture(dashboardUserService.getAllResource(providerId));
    UpdatedResourceVo updatedResourceVo = joinCompletableFuture(dashboardUserService.getUpdatedResource(providerId));
    AssetDataVo assetData = AssetDataVo.builder()
            .awsAssetsVo(awsAssetsVoList)
            .updatedResourceVo(updatedResourceVo)
            .build();

    ResponseVo result = new ResponseVo();
    result.setResult(assetData);
    return ok(result);
}

private static <T> T joinCompletableFuture(CompletableFuture<T> completableFuture) {
    return completableFuture.join();
}
```


## 참고 링크
- 구현 시 참고한 링크 : https://cano721.tistory.com/208#Return_%EA%B0%92%EC%9D%B4_%ED%95%84%EC%9A%94%ED%95%A0%EB%95%8C
