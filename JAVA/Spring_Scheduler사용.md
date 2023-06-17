# Spring 에서 배치 프로그램 사용해보기

## Spring Scheduler 사용

- `@EnableScheduling` 태그를 메인에 추가

```bash
@SpringBootApplication
@Slf4j
@EnableScheduling
public class AwsMetaCollector {

    public static void main(String[] args) {
        SpringApplication.run(AwsMetaCollector.class, args);
    }
}
```

- `@Scheduled(cron = "0 0/5 * * * *")` 태그를 배치 작업 수행할 메서드 위에 선언

```bash
@Scheduled(cron = "0 0/5 * * * *")
public void collectInstanceJob() {
    log.info("Spring Scheduler Cron Job 실행 : \n" +
            "Job 이름 : {}, 현재 시간 : {}", "collectInstanceJob", dateUtil.getDateTimeNow());
    ec2Service.collectInstanceWithCron();
}
```

- 시간 설정법 : cron 시간 설정과 동일
