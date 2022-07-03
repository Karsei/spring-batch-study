# SimpleFlow

Spring Batch 에서 제공하는 Flow 의 구현체로서 각 요소(Step, Flow, JobExecutionDecider) 들을 담고있는 State 를 실행시키는 도메인 객체

FlowBuilder 를 사용해서 생성하며 Transition 과 조합하며 여러 개의 Flow 및 중첩 Flow 를 만들어 Job 을 구성할 수 있다.

![simpleflow-concept](./imgs/simpleflow-concept.jpg)