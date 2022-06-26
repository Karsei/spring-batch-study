# JobStep

`Job` 에 속하는 `Step` 중 외부의 `Job` 을 포함하고 있는 `Step`. 커다란 시스템을 작은 모듈로 쪼개고 `Job` 의 흐름을 관리하고자 할 때 사용할 수 있다.

* 외부의 `Job` 이 실패하면, 해당 `Step` 이 실패하므로 결국 최종 기본 `Job` 도 실패한다.
* 모든 메타데이터는 기본 `Job` 과 외부 `Job` 별로 각각 저장된다.

```java
public Step jobStep() {
    return stepBuilderFactory.get("jobStep")
        // JobStep 내에서 실행될 Job 설정. JobStepBuilder 반환
        .job(Job)
        // Job 을 실행할 JobLauncher 설정
        .launcher(JobLauncher)
        // Step 의 ExecutionContext 를 Job 이 실행되는데 필요한 JobParameters 로 변환
        .parameterExtractor(JobParametersExtractor)
        // JobStep 생성
        .build();
}
```