---
title : 자바 Thread_10
date : 2023-12-14 12:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능
---

<br>

# 원자적 정수
- 원자적
 
```java
int initValue = 0;
// 원자적 정수의 입력값을 생성자에 넣음
AtomicIneger atomicIneger = new AtomicIneger(initValue);
```

```java
/* 둘 다 값을 1씩 증가시킴 */
atomicIneger.incrementAndGet(); // 1을 더 한값을 반환
atomicIneger.getAndIncrement(); // 현재값을 반환하고 값을 더함


/* 둘 다 값을 1씩 감소시킴 */
atomicIneger.decrementAndGet(); // 1을 뺀값을 반환
atomicIneger.getAndDecrement(); // 현재값을 반환하고 값을 뺌

/* 값을 더함 (+역할, -역할) */
atomicIneger.addAndGet(5); // 값을 더하고 반환
atomicIneger.getAndadd(5); // 기존값을 반환하고 더함
```
- 락이나 동기화가 필요하지않아 경쟁상태나 데이터 경쟁이 필요없음
- 단점은 메서드 자체가 원자적인데, 다른 연산과 함꼐 사용이 어려움


<br>

# Lock Free 이커머스 





<br>

출처 - https://kmooc.udemy.com/course/java-multi-threading/ 
(Java 멀티스레딩, 병행성 및 성능 최적화 - 전문가 되기
)