---
title : 자바 Thread_7
date : 2023-12-08 17:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능
---

<br>

# ReentrantLock
- synchronized 키워드처럼 작동함
- 그러나 명확한 락킹과 언락킹을 명시해야함
- 단점은 공유된 리소스사용한 후 언락킹을 명시안했을때 데드락의 원인이 된다.
- 락킹, 언락킹을 명시하더라도 throwExceptionMethod로 인해 문제가 생기면 임계영역에서 에러가 났으니 언락이 호출 안 될 수도있음
- 이러한 문제로 인해 락킹을 걸고 예외처리구문의 끝에 finally까지 만들면 임계영역에서 언락킹을 실행할 수 있게된다. 
  
- 과정이 복잡하기 떄문에 우리는 락을 더 통제하고 **수준높은 작업**을 해야함
- #### <U>getQueuedThreads()</U> 메서드를 통해 락을 기다리는 스레드 목록을 반환함
- #### <U>getOwner()</U> 메서드는 락을 가지고 있는 스레드를 반환
- #### <U>isHeldbyCurrentThread()</U> 현재 스레드에 락이 있으면 true를 반환
- isLocked() 메서드는 스레드에 락이 있는지 없는지 알려줌
- 프로덕션 코드에 대한 테스트를 빡세게 해야하고, 복잡한 스레드를 테스트할떄 isLocked(), getQueuedZThreads() 를 사용하면 편리함.
  
- **락의 공정성을 제어할 때 가장 뛰어남**
- 기본적으로 reentrantLock 및 동기화 키워드는 공정성을 보장하지 않음
- ***ReentrantLock(ture)*** 으로 실제 값을 전달해서 공정성을 만들수있음
- ***공정성을 유지하면 문제가 생김.***
- 락을 얻으면 시간이 걸려 프로그램의 처리량이 줄어들 수 있음 => 꼭 필요할 때만 사용해야함
- 감시 장치를 실행할 때 LockInterruptibly()이 메서드를 사용하면 유용함
- 감시 장치로 데드락 스레드를 검출하고
데드락 스레드에 인터럽트를 실시해 스레드를 복구하죠
그리고 이 메서드를 사용해서 앱을 정상적으로 종료할 수 있음 -> lock을 기다리는 스레드를 인터럽트로 꺠우고 종료

## tryLock()
- 어떤 경우에도 tryLock메서드를 차단하지 않음
- lock 객체가 어떤 스레드에 있든 즉시 값을 반환
  
- 만약 실시간 앱에 사용한다면 기억해야할게있음
- lock 메서드로 스레드를 중단할 순 없음 (영상, 사진, 속도가 빠른앱, 지연이 없는 시스템, GUI등)

<br><br><br>

```java

// lock 객체를 얻고 다른 스레드가 임계 영역에 못들어감
// 다른 스레드 입장에서는 먼저 잡은 스레드때문에 lock이 걸려 진행을 못하게 됨
lockObject.lock();
try {
    // 실행구문
}
finally {
    lockObject.unlock();
}



// tryLock 메서드로 lock 객체를 얻음.
// true 반환해 현재 스레드를 얻었다는것을 알 수 있음
// 다른 스레드가 들어오더라도 lock객체를 얻지 못했으니 else 조건을 실행하면 됨.
if(lockObject.tryLock());
    try {
        // 실행구문
    }
    finally {
        lockObject.unlock();
    }
}else {
    // 실행구문
}

```

- 어떤 경우라도 tryLock 메서드를 차단하지않음
- lock객체가 어떤 스레드에 있든 값을 반환함


<br> <br> <br>
### **ReentrantLock은 synchronized와 똑같은 특성과 기능을 가지고 있음**
  - lock 객체를 테스트하기 위한 쿼리 메서드가 주요한 기능
  - lockInterruptibly() 으로 인터럽트
  - tryLock()