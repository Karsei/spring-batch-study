# JobExecutionDecider

`ExitStatus` 를 조작하거나 `StepExecutionListener` 를 등록할 필요없이 Transition 처리를 위한 전용 클래스

`Step` 과 `Transition` 역할을 명확히 분리해서 설정할 수 있음

`Step` 의 `ExitStatus` 가 아닌 `JobExecutionDecider` 의 `FlowExecutionStatus` 상태값을 새롭게 설정해서 반환함

```java
// JobExecutionDecider
FlowExecutionStatus decide(JobExecution jobExecution, StepExecution stepExecution)
```

### 예시

```java
@Bean
public Job job() {
    return jobBuilderFactory.get("job")
        .start(startStep())
        .next(decider())
        .from(decider()).on("ODD").to(oddStep())
        .from(decider()).on("EVEN").to(evenStep())
        .end()
        .build();
}

@Bean
public JobExecutionDecider decider() {
    return new OddDecider();
}

public static class OddDecider implements JobExecutionDecider {
    private int count = 0;

    @Override
    public FlowExecutionStatus decide(JobExecution jobExecution,
                                      StepExecution stepExecution) {
        count++;
        if (count % 2 == 0) {
            return new FlowExecutionStatus("EVEN");
        }
        else {
            return new FlowExecutionStatus("ODD");
        }
    }
}
```