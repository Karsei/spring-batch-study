# SimpleJob - start() / next()

* start(step) - 처음 실행 후 Step 설정. 최초 한 번 설정. `SimpleJobBuilder` 가 생성되고 반환된다.
* next(step) - 다음에 실행할 Step 들을 순차적으로 연결하도록 설정. 여러 번 설정이 가능하며, 모든 next() 의 `Step` 이 종료가 되면 `Job` 이 종료된다.

> 인자에 따라서 `SimpleJobBuilder` 가 생성되거나 `FlowJobBuilder` 가 생성되므로 주의할 것

```java
public Job batchJob() {
    return jobBuilderFactory.get("batchJob")
        .start(step)
        .next(step)
        //.incrementer()
        //.validator()
        //.preventRestart()
        //.listener()
        .build();
}
```