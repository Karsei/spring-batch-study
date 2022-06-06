# Hello Spring Batch 시작

## 생성

```java
@Configuration  // 1
public class HelloJobConfiguration {
    // 2
    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;
    public HelloJobConfiguration(JobBuilderFactory jobBuilderFactory, StepBuilderFactory stepBuilderFactory) {
        this.jobBuilderFactory = jobBuilderFactory;
        this.stepBuilderFactory = stepBuilderFactory;
    }
    
    // 3
    @Bean
    public Job helloJob() {
        return jobBuilderFactory.get("helloJob")    // Job 생성
                .start(helloStep1())
                .next(helloStep2())
                .build();
    }
    
    // 4
    @Bean
    public Step helloStep1() {
        return stepBuilderFactory.get("helloStep")  // Step 생성
                // 5
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Hello Spring Batch 1");
                    return RepeatStatus.FINISHED;
                }))
                .build();
    }

    @Bean
    public Step helloStep2() {
        return stepBuilderFactory.get("helloStep2")
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Hello Spring Batch 2");
                    return RepeatStatus.FINISHED;
                }))
                .build();
    }
}
```

1. Configuration 선언
   + 하나의 배치 Job 을 정의
2. Builder Factory 생성
   + `JobBuilderFactory` - Job 을 생성하는 빌더 팩토리
   + `StepBuilderFactory` - Step 을 생성하는 빌더 팩토리
3. Job
   + helloJob 이름으로 Job 생성
4. Step
   + helloStep 이름으로 Step 생성
5. tasklet
   + Step 안에서 단일 태스크로 수행되는 로직 구현
   + 기본적으로 Tasklet 을 만들면 무한 반복 상태로 만들어짐
     + `return null` = `RepeatStatus.FINISHED`
   + 비즈니스 로직 포함됨

```mermaid
graph LR;
    A[Job 구동]-->B[Step 실행];
    B-->C[Tasklet 실행];
```

## 결과

```
2022-06-06 22:10:23.144  INFO 3516 --- [           main] o.s.b.c.l.support.SimpleJobLauncher      : Job: [SimpleJob: [name=helloJob]] launched with the following parameters: [{}]
2022-06-06 22:10:23.162  INFO 3516 --- [           main] o.s.batch.core.job.SimpleStepHandler     : Executing step: [helloStep1]
Hello Spring Batch 1
2022-06-06 22:10:23.168  INFO 3516 --- [           main] o.s.batch.core.step.AbstractStep         : Step: [helloStep1] executed in 6ms
2022-06-06 22:10:23.170  INFO 3516 --- [           main] o.s.batch.core.job.SimpleStepHandler     : Executing step: [helloStep2]
Hello Spring Batch 2
2022-06-06 22:10:23.172  INFO 3516 --- [           main] o.s.batch.core.step.AbstractStep         : Step: [helloStep2] executed in 2ms
2022-06-06 22:10:23.173  INFO 3516 --- [           main] o.s.b.c.l.support.SimpleJobLauncher      : Job: [SimpleJob: [name=helloJob]] completed with the following parameters: [{}] and the following status: [COMPLETED] in 20ms
```

## Job, Step, Tasklet 을 간단하게 말하자면?

### Job

일, 일감

### Step

일의 항목, 단계

### Tasklet

작업 내용 (비즈니스 로직)