# SimpleJob - validator

`Job` 실행에 꼭 필요한 파라미터를 검증하는 용도

`DefaultJobParametersValidator` 구현체를 지원하며, 좀 더 복잡한 제약 조건이 있다면 인터페이스를 직접 구현할 수도 있음

```java
// JobParameters 값을 매개변수로 받아 검증함
void validate(@Nullable JobParamters parameters)
```

```java
public Job batchJob() {
    return jobBuilderFactory.get("batchJob")
        //.start(step)
        //.next(step)
        //.incrementer()
        .validator(JobParametersValidator)
        //.preventRestart()
        //.listener()
        .build();
}
```

## DefaultJobParametersValidator 흐름도

* 필수값 (requiredKeys) - date, name
* 선택값 (optionalKeys) - count

위처럼 파라미터를 받는다고 했을 때 `JobParametersValidator` 를 통해서 `JobParameters` 로부터 검증 시 아래처럼 동작한다.

* date 만 넣었을 경우 - 필수 Key 인 name 없음 -> `JobParametersInvalidException`
* date, count 넣었을 경우 - 필수 Key 인 name 없음 -> `JobParametersInvalidException`
* date, name 넣었을 경우 - 필수 Key 모두 존재. 옵션 Key 없어도 됨 -> 성공

```java
public class CustomJobParameterValidator implements JobParametersValidator {
    @Override
    public void validate(JobParameters parameters) throws JobParametersInvalidException {
        if (parameters.getString("name") == null) {
            throw new JobParametersInvalidException("name parameter is not found");
        }
    }
}
```

```java
public Job batchJob() {
    return jobBuilderFactory.get("batchJob")
        .start(step1)
        .next(step2)
        .validator(new DefaultJobParametersValidator(new String[]{"name", "date"}, new String[]{"count"}))
        .build();
```