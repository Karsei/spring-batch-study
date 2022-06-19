# 배치 초기화 설정

## JobLauncherApplicationRunner

Spring Batch 작업을 시작하는 `ApplicationRunner` 로서 `BatchAutoConfiguration` 에서 생성됨

Spring Boot 에서 제공하는 `ApplicationRunner` 의 구현체로, Application 이 정상적으로 구동되자마자 실행된다.

기본적으로 Bean 으로 등록된 모든 Job 들을 실행시킨다.

## BatchProperties

Spring Batch 의 환경설정 클래스

`Job` 이름, Scheme 초기화 설정, Table Prefix 등의 값을 설정할 수 있다.

`application.properties` 또는 `application.yml` 파일에 설정함

```yaml
batch:
    job:
        names: ${job.name:NONE}
    initialize-scheme: NEVER
    tablePrefix: SYSTEM
```

## Job 실행 옵션

```
spring.batch.job.names: ${job.name:NONE}
```

> 만약에 `job.name` 에 해당하는 Job 이름 이 없다면 그냥 무시하고 Application 이 실행됨

Application 실행 시 Program Arguments 로 `Job` 이름을 입력한다.

```bash
# 하나의 Job 을 실행
--job.name=helloJb
# 하나 이상의 Job 을 실행할 경우 쉼표로 구분해서 입력한다.
--job.name=helloJob,simpleJob
```