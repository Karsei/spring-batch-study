# ChunkOrientedTasklet

스프링 배치에서 제공하는 Tasklet 의 구현체로서 Chunk 지향 프로세싱을 담당하는 도메인 객체

`ItemReader`, `ItemWriter`, `ItemProcessor` 를 사용하여 Chunk 기반의 데이터 입출력 처리를 담당한다.

`TaskletStep` 에 의해서 반복적으로 실행되며, `ChunkOrientedTasklet` 이 실행될 때마다 매번 새로운 트랜잭션이 생성되어 처리가 이루어진다.

Exception 이 발생할 경우, 해당 Chunk 는 롤백되며 이전에 커밋한 Chunk 는 완료된 상태가 유지된다.

내부적으로 `ItemReader` 를 핸들링하는 `ChunkProvider` 와 `ItemProcessor`, `ItemWriter`  를 핸들링하는 `ChunkProcessor` 타입의 구현체를 가진다.

아래 흐름은 Transaction 경계 내에서 이루어진다.

write 는 Chunk 단위로 이루어진다.

![chunk-oriented-flow](./imgs/chunk-oriented-flow.jpg)

![chunk-oriented-inner](./imgs/chunk-oriented-inner.jpg)