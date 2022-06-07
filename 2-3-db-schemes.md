# DB 스키마 생성

## 스프링 배치 메타 데이터

![Spring Batch Meta Schema](https://docs.spring.io/spring-batch/docs/current/reference/html/images/meta-data-erd.png)

> https://docs.spring.io/spring-batch/docs/current/reference/html/schema-appendix.html#metaDataSchema

스프링 배치의 실행 및 관리를 위한 목적으로 여러 도메인들(`Job`, `Step`, `JobParameters`, ...) 의 정보들을 저장, 업데이트, 조회할 수 있는 스키마 제공

* 과거, 현재의 실행에 대한 상세 정보, 실행에 대한 성공/실패 여부 등을 일목요연하게 관리함으로서 배치 운용에 있어 리스크 발생 시 빠른 대처 가능
* DB 와 연동할 경우 필수적으로 메타 테이블이 생성되어야 함

### 파일 위치

**org.springframework.batch:spring-batch-core** 라이브러리에서 DB 유형별로 제공하고 있음

*org/springframework/batch/core/scheme-***.sql*

## 스키마 생성 설정

### 수동 생성

쿼리 복사 후 직접 실행

### 자동 생성

`spring.batch.jdbc.initialize-schema` 속성 설정

* ALWAYS
   + 스크립트 항상 실행
   + RDBMS 설정이 되어 있을 경우 내장 DB 보다 우선적으로 실행
* EMBEDDED
   + 내장 DB 일 때만 실행되며 스키마가 자동 생성됨
   + *기본값*
* NEVER
   + 스크립트 항상 실행 안 함
   + 내장 DB 일 경우 스크립트가 생성이 안 되기 때문에 오류 발생
   + **운영에서 수동으로 스크립트 생성 후 설정하는 것을 권장**

### 예시

아래는 `local` (H2), `mysql` (MySQL) 이름의 프로필 2개를 만들어서 선택적으로 DB 를 이용할 수 있도록 `application.yml` 파일에 속성을 작성한 것

```yaml
spring:
  config:
    activate:
      on-profile: local
  datasource:
    hikari:
      jdbc-url: jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
      username: sa
      password:
      driver-class-name: org.h2.Driver
---
spring:
  config:
    activate:
      on-profile: mysql
  datasource:
    hikari:
      jdbc-url: jdbc:mysql://localhost:3306/springbatch?useUnicode=true&characterEncoding=utf8
      username: root
      password: pass
      driver-class-name: com.mysql.cj.jdbc.Driver # 8.x 이후
      #driver-class-name: com.mysql.jdbc.Driver # 8.x 이전
---
spring:
  profiles:
    active: local
  batch:
    jdbc:
      initialize-schema: embedded
```

## DB 테이블 설명

### Job 관련

* `BATCH_JOB_INSTANCE`
   + `Job` 이 실행될 때 `JobInstance` 정보가 저장되며, `job_name` 과 `job_key` 키로 하여 하나의 데이터가 저장
   + 동일한 `job_name` 과 `job_key` 로 중복 저장될 수 없음
   + 컬럼 상세
      + JOB_INSTANCE_ID - 고유하게 식별할 수 있는 기본 키
      + VERSION - 업데이트 될 때마다 1씩 증가
      + JOB_NAME - `Job` 을 구성할 때 부여하는 이름
      + JOB_KEY - `job_name` 과 `JobParameter` 를 합쳐서 해싱한 값을 저장
* `BATCH_JOB_EXECUTION`
   + `Job` 의 실행 정보가 저장
   + 생성, 시작, 종료 시간, 실행 상태, 메시지 등을 관리
   + 컬럼 상세
     + JOB_EXECUTION_ID - `JobExecution` 을 고유하게 식별할 수 있는 기본 키. `JOB_INSTANCE` 와 일대다 관계
     + VERSION - 업데이트 될 때마다 1씩 증가
     + JOB_INSTANCE_ID - `JOB_INSTANCE` 의 키 저장
     + CREATE_TIME - 실행(Execution)이 생성된 시점을 Timestamp 형식으로 기록
     + START_TIME - 실행(Execution)이 시작된 시점을 Timestamp 형식으로 기록
     + END_TIME - 실행이 종료된 시점을 Timestamp 으로 기록하며, `Job` 실행 도중 오류가 발생해서 `Job` 이 중단된 경우 값이 저장되지 않을 수 있음
     + STATUS - 실행 상태(BatchStatus) 를 저장 (COMPLETED, FAILED, STOPPED, ...)
     + EXIT_CODE - 실행 종료코드(ExitStatus) 를 저장 (COMPLETED, FAILED, ...)
     + EXIT_MESSAGE - Status 가 실패일 경우 실패 원인 등의 내용을 저장
     + LAST_UPDATED - 마지막 실행(Execution) 시점을 Timestamp 형식으로 기록
     + JOB_CONFIGURATION_LOCATION
* `BATCH_JOB_EXECUTION_PARAMS`
   + `Job` 과 함께 실행되는 `JobParameter` 정보를 저장
   + 컬럼 상세
     + JOB_EXECUTION_ID - `JobExecution` 식별 키, `JOB_EXECUTION` 과는 일대다 관계
     + TYPE_CD - STRING, LONG, DATE, DOUBLE 타입 정보
     + KEY_NAME - 파라미터 키 값
     + STRING_VAL - 파라미터 문자 값
     + DATE_VAL - 파라미터 날짜 값
     + LONG_VAL - 파라미터 LONG 값
     + DOUBLE_VAL - 파라미터 DOUBLE 값
     + IDENTIFYING - 식별 여부 (TRUE, FALSE)
* `BATCH_JOB_EXECUTION_CONTEXT`
   + `Job` 실행동안 여러 가지 상태 정보, 공유 데이터를 직렬화(Json)해서 저장
   + `Step` 간 서로 공유 가능
   + 컬럼 상세
     + JOB_EXECUTION_ID - `JobExecution` 식별 키, `JOB_EXECUTION` 마다 각 생성
     + SHORT_CONTEXT - `Job` 의 실행 상태 정보, 공유 데이터 등의 정보를 문자열로 저장
     + SERIALIZED_CONTEXT - 직렬화(serialized)된 전체 컨텍스트

### Step 관련

* `BATCH_STEP_EXECUTION`
   + `Step` 의 실행 정보가 저장
   + 생성, 시작, 종료 시간, 실행 상태, 메시지 등을 관리
   + 컬럼 상세
     + STEP_EXECUTION_ID - `Step` 의 실행 정보를 고유하게 식별할 수 있는 기본 키
     + VERSION - 업데이트 될 때마다 1씩 증가
     + STEP_NAME - `Step` 을 구성할 때 부여하는 이름
     + JOB_EXECUTION_ID -  `JobExecution` 기본키, `JobExecution` 과 일대다 관계
     + START_TIME - 실행(Execution) 이 시작된 시점을 Timestamp 형식으로 기록
     + END_TIME - 실행이 종료된 싲머을 Timestamp 으로 기록하며, `Job` 실행 도중 오류가 발생해서 `Job` 이 중단된 경우 값이 저장되지 않을 수 있 음
     + STATUS - 실행 상태(BatchStatus) 를 저장 (COMPLETED, FAILED, STOPPED, ...)
     + COMMIT_COUNT - 트랜잭션 당 커밋되는 수를 기록
     + READ_COUNT - 실행 시점에 Read 한 Item 수를 기록
     + FILTER_COUNT - 실행 도중 필터링된 Item 수를 기록
     + WRITE_COUNT - 실행 도중 저장되고 커밋된 Item 수를 기록
     + READ_SKIP_COUNT - 실행 도중 Read 가 Skip 된 Item 수를 기록
     + WRITE_SKIP_COUNT - 실행 도중 Write 가 Skip 된 Item 수를 기록
     + PROCESS_SKIP_COUNT - 실행 도중 Process 가 Skip 된 Item 수를 기록
     + ROLLBACK_COUNT - 실행 도중 Rollback 이 일어난 수를 기록
     + EXIT_CODE - 실행 종료 코드(ExitStatus) 를 저장 (COMPLETED, FAILED, ...)
     + EXIT_MESSAGE - Status 가 실패일 경우, 실패 원인 등의 내용을 저장
     + LAST_UPDATED - 마지막 실행(Execution) 시점을 Timestamp 형식으로 기록
* `BATCH_STEP_EXECUTION_CONTEXT`
   + `Step` 실행동안 여러 가지 상태 정보, 공유 데이터를 직렬화(Json)해서 저장
   + `Step` 간 서로 공유 불가능
   + `Step` 별로 저장
   + 컬럼 상세
     + STEP_EXECUTION_ID - `StepExecution` 식별 키, STEP_EXECUTION 마다 각 생성
     + SHORT_CONTEXT - `Step` 의 실행 상태 정보, 공유 데이터 등의 정보를 문자열로 저장
     + SERIALIZED_CONTEXT - 직렬화(Serialized)된 전체 컨텍스트