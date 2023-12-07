---
title : 자바 Thread_4
date : 2023-12-05 18:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능
---




# 스레드 간 데이터 공유

```java

public static void main(String[] args) throws InterruptedException {
        // 하나의 인스턴스로 아이템 객체를 주입
        InvertoryCounter invertoryCounter = new InvertoryCounter();
        IncrementingThread incrementingThread = new IncrementingThread(invertoryCounter);
        DecrementingThread decrementingThread = new DecrementingThread(invertoryCounter);

        /* 아이템을 넣는 스레드가 시작되고 종료후, 
           아이템을 빼는 스레드가 시작되어 0이 나옴 */
        incrementingThread.start();
        incrementingThread.join();

        decrementingThread.start();
        decrementingThread.join();


        /* 스레드가 서로 데이터를 공유하고 있기 때문에 
           실행할때마다 값이 달라짐 */
        incrementingThread.start();
        decrementingThread.start();

        incrementingThread.join();
        decrementingThread.join();

        System.out.print("현재 아이템수: ");
        System.out.println(invertoryCounter.getItems());
    }

    // 아이템을 빼는 스레드
    public static class DecrementingThread extends Thread {

        private InvertoryCounter invertoryCounter;

        public DecrementingThread(InvertoryCounter invertoryCounter) {
            this.invertoryCounter = invertoryCounter;
        }

        @Override
        public void run() {
            for (int i = 0; i < 10000; i++) {
                invertoryCounter.decrement();
            }
        }
    }

    // 아이템을 넣는 스레드
    public static class IncrementingThread extends Thread {

        private InvertoryCounter invertoryCounter;

        public IncrementingThread(InvertoryCounter invertoryCounter) {
            this.invertoryCounter = invertoryCounter;
        }

        @Override
        public void run() {
            for (int i = 0; i < 10000; i++) {
                invertoryCounter.increment();
            }
        }
    }


    private static class InvertoryCounter {
        private int items = 0;

        public void increment() {
            items++;
        }

        public void decrement() {
            items--;
        }

        public int getItems() {
            return items;
        }
    }
```

<br> 
  
  - 한 스레드는 1을 더하는 작업, 다른 하나는 1을 빼는 작업
  
  - 실행되는 순서는 스케줄링을 하는 방식에 따라 달라지기에 결과값이 항상 바뀜.

### 대참사가 일어나는 시나리오
1. currenVal <- item = 0 (In스레드)
2. newVal <- currenVal + 1 = 1 (In스레드)
3. currenVal <- item = 0 (De스레드)
4. newVal <- currenVal + 1 = 1 (De스레드)
5. Items <- newVal = -1 (De스레드)
6. Items <- newVal = 1 (In스레드)

=> 최종적으로는 Items에 1이 할당되어 De스레드에서 일어난 일이 아무것도 아니게 되는 결과가 발생되었음 ! (5번과 6번이 바뀌어도 대참사)

<br> <br> <br>

## 개선하기 !

<br> <br>

### 임계영역
  - 멀티 스레드에 의해 동시 실행되지않게 보호해야하는 코드가 있는 영역

<br>

1. Synchronized - monitor
- 여러 개의 스레드가 코드 블록이나 전체 메서드에 액세스할 수 없도록 설계된 락킹 메커니즘

```java
private static class InvertoryCounter {
        private int items = 0;

        public synchronized void increment() {
            items++;
        }

        public synchronized void decrement() {
            items--;
        }

        public synchronized int getItems() {
            return items;
        }
    }
```
- 하나의 스레드가 접근하면 다른 스레드는 접근자체가 안됨.
- 인스턴스(객체)를 기준으로 동기화가 걸림
=> Thread A가 메서드를 실행하면 다른 메서드는 모두 실행할 수 가 없음.

<br> <br>

2. Synchronized - Lock
   - 코드의 블럭을 정의하고 Synchronized 키워드를 통해 전체 메서드는 동기화하지않고 특정 영역만 액세스함
   - 스레드간 동기화가 필요한 부분을 적게 만들 수 있는 장점이 있음

```java
    private static class InvertoryCounter {
        private int items = 0;

        Object lock = new Object();

        public void increment() {
            synchronized (this.lock) {
                items++;
            }
        }

        public void decrement() {
            synchronized (this.lock) {
                items--;
            }
        }

        public int getItems() {
            synchronized (this.lock) {
                return items;
            }
        }
    }
```
- 동기화 블록이나 메서드는 Reentrant 즉, 재진입할 수 있는 요소
- 스레드1이 이미 다른 동기화 메서드나 블록에 있는 상태에서 또 동기화 메서드에 액세스하면 별 문제 없이 그 동기화 메서드에 액세를 할 수 있게됨.
- 기본적으로는 스레드가 임계영역에 접근하는 것 자체를 막을 순 없음