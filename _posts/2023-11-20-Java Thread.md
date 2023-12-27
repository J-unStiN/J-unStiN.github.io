---
title : 자바 Thread_1
date : 2023-11-20 23:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능

---



>### 왜 쓰레드가 필요한가??

- 반응성
- 성능

</br></br>

1. 컨텍스트 스위치
- 어플리케이션 각각의 인스턴스는 독립적으로 실행이 됨.
- 일반적으로는 코어의 개수보다 프로세스의 개수가 더 많고, 하나의 프로세스는 하나 이상의 쓰레드가 있음.
  그래서 OS는 스레드 하나를 실행하고, 멈추고 다시 실행하는것이 **컨텍스트 스위치**

</br></br></br>

  >**컨텍스트 스위치**
   - 동시에 많은 스레드를 다룰 때는 효율성이 떨어지기에 이 개념이 아주 중요함 !
   - 사람으로 생각하면 생각을 가다듬고, 집중력을 회복하는 시간 ! (회사에서 많은 업무를 동시에 못하는것처럼!!)
   - CPU에서 실행되는 각 스레드는 CPU 안에 레지스터, 캐시, 메모리 내의 커널 리소스 일정 부분을 차지하게됨.
   다른 쓰레드로 전환할 때는 기존의 모든 데이터를 저장하고, 다른 쓰레드의 리소스를 CPU와 메모리에 복원해야함.

>## 너무 많은 스레드를 사용시 '스래싱'이 발행

- 같은 프로세스에 속한 두 스레드 간의 컨텍스트 스위치(효율) >> 다른 프로세스의 두 스레드간의 스위치(비효율)


>### 그럼 언제 컨텍스트 스위치를 할 지 결정하는가??


1. 쓰레드 스케줄
    - 들어온 순서대로 작업을 처리할지?
    - 짧은 시간인것부터 먼저 처리할지?

2. 쓰레드 vs 프로세스
     - 멀티 스레드와 멀티 프로세스.
     - 스레드는 많은 리소스를 공유하니 같은 프로세스 안에서 스레드를 사용하는 것이 좋음.
     - 보안,안정성 -> 멀티 스레드는 스레드 하나에 프로세스가 다운될 가능성도 있음.



## 자바 스레드의 기능과 디버깅 방법
```java
        // 스레드 생성
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("now thread: " + Thread.currentThread().getName());
                System.out.println("current thread: " + Thread.currentThread().getPriority());

                /* 에러 반환 */
                throw new RuntimeException("Exception");
            }
        });


        // 스레드에 문제를 생겼을때 해당 헨들러가 작동해서 에러를 반환
        thread.setUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
            @Override
            public void uncaughtException(Thread t, Throwable e) {
                System.out.println("error happy");
            }
        });
```



</br>

## 자바 스레드 상속 예제

```java


public static final int MAX_PASSWORD = 9999;
    public static void main(String[] args) throws InterruptedException {
        Random random = new Random();

        Vault vault = new Vault(random.nextInt(MAX_PASSWORD));

        // 모든 클래스가 Thread클래스를 상속받았기에 제네릭으로 사용 가능함.
        List<Thread> threads = new ArrayList<>();

        threads.add(new AscendingHackerThread(vault));
        threads.add(new DescendingHackerThread(vault));
        threads.add(new PoliceThread());

        // 쓰레드 담긴 개수만큼 반복해서 실행
        for (Thread thread : threads) {
            thread.start();
        }

    }

    // 해당 방법처럼 클래스에 쓰레드를 상속받아서 쓰레드클래스로 사용 가능
    private static class NewThread extends Thread {
        @Override
        public void run(){
            System.out.println("Hello" + this.getName());
        }
    }



    private static class Vault {
        private int passward;

        public Vault(int passward) {
            this.passward = passward;
        }
        public boolean isCorrentPassword(int guess) {

            try {
                Thread.sleep(5);
            } catch (Exception e){
            }

            return this.passward == guess;
        }
    }


    // 쓰레드를 상속받은 클래스를 추상클래스로 만들어 활용
    private static abstract class HackerThread extends Thread {
        protected Vault vault;

        public HackerThread(Vault vault) {
            this.vault = vault;
            this.setName(this.getClass().getSimpleName());
            this.setPriority(Thread.MAX_PRIORITY);
        }

        @Override
        public void start() {
            System.out.println("string thread: " + this.getName());
            super.start();
        }
    }


    private static class AscendingHackerThread extends HackerThread {

        public AscendingHackerThread(Vault vault) {
            super(vault);
        }

        @Override
        public void run() {
            for (int i = 0; i < MAX_PASSWORD; i++) {
                if(vault.isCorrentPassword(i)){
                    System.out.println(this.getName() + "password" + i);
                    System.exit(0);
                }
            }
        }
    }

    private static class DescendingHackerThread extends HackerThread {
        public DescendingHackerThread(Vault vault){
            super(vault);
        }

        @Override
        public void run() {
            for (int i = MAX_PASSWORD; i >= 0; i--) {
                if (vault.isCorrentPassword(i)) {
                    System.out.println(this.getName() + "password" + i);
                    System.exit(0);
                }
            }
        }
    }


    private static class PoliceThread extends Thread {
        @Override
        public void run() {
            for (int i = 10; i > 0; i--) {
                try {
                    Thread.sleep(1000);
                }catch (Exception e) {

                }
                System.out.println(i);
            }

            System.out.println("Over Hacker");
            System.exit(0);
        }
    }
```

---
---

## 핵심 !!!!
- 애플리케이션에서 여러 개의 스레드를 사용하는 이유는 무엇일까요?
 <br> => 여러 개의 스레드를 사용하면 동시에 여러 개의 관련 작업을 실행할 수 있어서, 애플리케이션의 반응성을 향상시킬 수 있기 때문입니다. 또한 여러 개의 작업을 동시에 실행하여 더 뛰어난 성능을 달성할 수도 있습니다.
<br>
<br>


- 하나의 프로세스에 속한 스레드가 공유하는 것은? 
<br> => 힙, 코드, 프로세스의 열린 파일, 프로세스의 메타 데이터
<br>
<br>


- OS는 어떤 방식으로 스케줄링할 스레드를 설계하는가? 
<br> => 각 스레드에 대한 동적 우선 순위를 유지해 인터랙티브 스레드를 우선시하고 시스템의 특정 스레드가 기아 상태가 되는 것을 방지하는것이 모든 OS의 목표 (스케줄링 스레드에 대한 알고리즘은 OS마다 다름)
<br>
<br>

출처 - https://kmooc.udemy.com/course/java-multi-threading/ 
(Java 멀티스레딩, 병행성 및 성능 최적화 - 전문가 되기
)