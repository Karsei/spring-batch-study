```java
public Job batchJob () {
    return jobBuilderFactory.get("batchJob")
        .start(Flow)
        .next(Step or Flow or JobExecutionDecider)
        .on(String pattern)
        .stop() / fail() / end() / stopAndRestart()
        .end()
        .build();
}
```