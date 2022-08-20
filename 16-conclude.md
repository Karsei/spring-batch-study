# 정리

## 기초 및 기본 이해

* 도메인 이해 - JobInstance, JobExecution, StepExecution, ExecutionContext, JobParameter, JobRepository, JobLauncher
* Job 구성 및 API 활용 - Job, Step, Flow, Tasklet
* Chunk 프로세스 - Chunk, ItemReader, ItemProcessor, ItemWriter
* 반복 및 내결함성 - Repeat, Skip, Retry, Listener
* 이벤트 리스너 - JobExecutionListener, StepListener, RetryListener, SkipListener
* 멀티 스레드 배치 처리 - MultiThread Batch Process
* 테스트 및 운영 - TDD & JobExplorer, JobRegistry, JobOperator

## 구조 및 원리 이해

단순한 API 사용법을 넘어 각 기능에 대한 아키텍처 및 흐름과 원리 이해

기본 기능을 확장한 커스트마이징한 기능 구현

## 배치 연동

Jenkins - 작업 등록 및 실행, 스케줄링

Quartz - 실시간 스케줄링, 서버 간 클러스터 기능