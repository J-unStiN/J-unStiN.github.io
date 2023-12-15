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


## synchronized 통한 원자성 
```java
    public static void main(String[] args) throws InterruptedException {
        InventoryCounter inventoryCounter = new InventoryCounter();

        
        IncrementThread incrementThread = new IncrementThread(inventoryCounter);
        DecrementThread decrementThread = new DecrementThread(inventoryCounter);

        // 더하는 쓰레드 & 빼는 쓰레드 각각 만번씩 실행
        incrementThread.start();
        decrementThread.start();

        incrementThread.join();
        decrementThread.join();
        
        // 쓰레드가 synchronized 만나면 다른 스레드는 대기상태가 되기때문에 하나씩 실행이 되고 필드는 원자성을 확보하게되어 0이 출력
        System.out.println("inventoryCounter.getItems() = " + inventoryCounter.getItems());
    }

    public static class DecrementThread extends Thread{
        private InventoryCounter inventoryCounter;

        public DecrementThread(InventoryCounter inventoryCounter) {
            this.inventoryCounter = inventoryCounter;
        }

        @Override
        public void run() {
            for (int i = 0; i < 10000; i++) {
                inventoryCounter.decrement();
            }
        }
    }

    public static class IncrementThread extends Thread {
        private InventoryCounter inventoryCounter;

        public IncrementThread(InventoryCounter inventoryCounter) {
            this.inventoryCounter = inventoryCounter;
        }
        @Override
        public void run() {
            for (int i = 0; i < 10000; i++) {
                inventoryCounter.increment();
            }
        }
    }

    public static class InventoryCounter {
        private int items = 0;

        public synchronized void increment() {
            items++;
        }

        public synchronized void decrement() {
            items--;
        }

        public int getItems() {
            return item;
        }
    }

```

<br><br><br>



## 원자적 변수를 적용한 코드
```java
    public static void main(String[] args) throws InterruptedException {
        InventoryCounter inventoryCounter = new InventoryCounter();

        IncrementThread incrementThread = new IncrementThread(inventoryCounter);
        DecrementThread decrementThread = new DecrementThread(inventoryCounter);

        incrementThread.start();
        decrementThread.start();

        incrementThread.join();
        decrementThread.join();
        
        System.out.println("inventoryCounter.getItems() = " + inventoryCounter.getItems());
    }


    public static class DecrementThread extends Thread{
        private InventoryCounter inventoryCounter;

        public DecrementThread(InventoryCounter inventoryCounter) {
            this.inventoryCounter = inventoryCounter;
        }

        @Override
        public void run() {
            for (int i = 0; i < 10000; i++) {
                inventoryCounter.decrement();
            }
        }
    }

    public static class IncrementThread extends Thread {
        private InventoryCounter inventoryCounter;

        public IncrementThread(InventoryCounter inventoryCounter) {
            this.inventoryCounter = inventoryCounter;
        }
        @Override
        public void run() {
            for (int i = 0; i < 10000; i++) {
                inventoryCounter.increment();
            }
        }
    }

    public static class InventoryCounter {
        private AtomicInteger items = new AtomicInteger(0);

        public void increment() {
            items.incrementAndGet();
        }

        public void decrement() {
            items.decrementAndGet();
        }

        public int getItems() {
            return items.get();
        }
    }

```

<br>

- 락을 사용하지않아 복잡하게 하지않고 병력적으로 수행할 수 있음
- 원자적 연산을 할 떄에만 사용해야함 !!!
- 원자적 연산은 비원자적 연산을 보호하기 위해 락을 가진 일반 정수를 사용할때보다 더 성능이 좋음
- 단점은 싱글 스레드를 사용하게되면 일반 정수사용보다 느려질 수 있음


<br>

출처 - https://kmooc.udemy.com/course/java-multi-threading/ 
(Java 멀티스레딩, 병행성 및 성능 최적화 - 전문가 되기
)