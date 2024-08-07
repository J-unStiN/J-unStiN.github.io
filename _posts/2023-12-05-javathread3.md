---
title : 자바 Thread_3
date : 2023-12-05 10:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능
---




# 멀티스레딩의 퍼모먼스
 - 레이턴시
   - 시간 단위로 측정되고 작업 하나의 완료 시간으로 정의
  
<br>

 - 처리량
   - 일정 시간 동안 완료한 작업의 양 (시간단위당)
 - 둘은 별개이며, 한쪽이 개선되어도 한쪽에는 영향이 없을수도 영향이 미칠 수도 있습니다.

## 멀티스레드 프로그래밍에서 지연시간을 어떻게 줄이는가?
- 지연시간 = T, 작업개수 = N
- N으로 지연시간을 줄이는 방법이 있음.
  1. N = 컴퓨터 코어의 개수
     - 모든 스레드가 인터럽트 없이 하위 작업을 실행해야 최적입니다. => 모든 스레드가 runable 상태여야함, IO 이나 블로킹 호출도 없어야함.
  2. 시스템에서 CPU를 많이 소모하는 건 실행되고 있지 않아야 한다는것 => 최적은 안되지만, 어느정도 효율은 가능함.


## 어떤 작업을 분할로 수행할 수 있는가?
- 본질적으로 병행 가능하며 하위 작업으로 쉽게 분할가능한 작업
- 분할이 불가능해 처음부터 끝까지 싱글스레드로 해야하는 작업
- 부분적으로 하위 작업으로 나누고, 순차적으로 실행해야하는 작업.

=> 성능이라는 용어는 다양하게 정의되며, 
실제로 측정과 개선에 관심있는 메트릭은 경우에 따라 정의해야함
  - 성능의 두 가지 지표 => 지연 시간과 처리량
  - 지연시간에 집중해 작업을 여러개로 분할해 지연시간을 
  줄이는 방법과 이 방식으로 무엇을 할 수 있는지?


처리량 - 주어진 기간에 완성되는 작업의 수
시간단위, 특히 초 단위로 작업을 나눠 처리량을 측정함

처리량 개선방법
1. Breaking Tasks into Subtasks
    - 작업을 N개의 task로 나누면 T/N으로 가능. => 최적의 N을 찾을 수 있음.
    - 여러 작업으로 나누고 합치는데에 비용이 드는 단점이 있음
2. 각 작업을 별개 스레드에 스케줄링 하는법
   1. 이론적으로 최대 처리량은 N/T
        - 각 작업을 작은 작업으로 나눌 필요가 없음.
        - 완전 별개의 작업임.
3. 스레드 풀링
   1. 스레드가 샐성되면 풀에 쌓이고 작업이 대기열을 통해 스레드별로 분배됨.
   2. 스레드는 이용 가능할 때마다 대기열을 통해 작업을 받음
   3. 모든 스레드가 바쁘면 대기열에서 기다림.
    - 낮은 오버헤드와 효율적인 대기열을 구현가능
    - 자바를 이용한 스레드풀 사용 가능 => 고정 스레드풀

<br>

출처 - https://kmooc.udemy.com/course/java-multi-threading/ 
(Java 멀티스레딩, 병행성 및 성능 최적화 - 전문가 되기
)
