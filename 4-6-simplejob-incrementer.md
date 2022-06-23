# SimpleJob - incrementer

`JobParameters` 에서 필요한 값을 증가시켜 다음에 사용될 `JobParameters` 오브젝트를 반환

기존의 `JobParameters` 변경없이 `Job` 을 여러 번 시작하고자 할 때 사용

`RunIdIncrementer` 구현체를 지원하며 인터페이스를 직접 구현할 수 있음

```java
JobParameters getNext(@Nulable JobParameters parameters);
```

```java
public Job batchJob() {
    return jobBuilderFactory.get("batchJob")
        //.start(step)
        //.next(step)
        .incrementer(JobParametersIncrementer)
        //.validator()
        //.preventRestart() 
        //.listener()
        .build();
}
```

```java
public class CustomJobParametersIncrementer implements JobParametersIncrementer {
    static final SimpleDateFormat format = new SimpleDateFormat("yyyyMMdd hhmmss");

    @Override
    public JobParamters getNext(JobParameters parameters) {
        String id = format.format(new Date());

        return new JobParametersBuilder.addString("run.id", id).toJobParameters();
    }
}
```

```java
public Job batchJob() {
    return jobBuilderFactory.get("batchJob")
        .start(step1())
        .next(step2())
        .incrementer(new CustomJobParametersIncrementer())
        .build();
```