---
title : 자바 Thread_2
date : 2023-11-23 23:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능
---




# 왜, 언제 스레드를 멈춰야 하는가??
- 스레드는 아무것도 하지 않을때에도 리소스를 사용함.
    1. 메모리와 커널 리소스
    2. CPU 시간뿐아니라 캐시까지 사용 
<br><br>
- 생성한 스레드가 이미 작업을 완료했으면, 사용하지 않는 스레드의 리소스를 정리해야함.
- 스레드가 오작동을 해야하면 중지해야함 ! (응답없는데 서버에 요청)
- 애플리케이션 전체를 중단하기 위해서임 !
(최소 하나의 스레드만 실행되고 있어도 프로세스는 종료되지않음)

<br>
<br>
<br>
<br>
<br>


### Thread.interrupt()
-> 각 스레드는 인터럭트 메서드를 가지고 있음
  1. 우리가 인터럽트하려는 스레드가 인터럽트 당했을 때 익셉션을 발생시키는 경우 !
  2. 이터럽트하려는 스레드가 신호를 명시적으로 처리하기 위해 !



```java

    public static void main(String[] args) throws InterruptedException {

        Thread thread = new Thread(new LongComuptionTask(new BigInteger("2"), new BigInteger("10")));
        thread.start();

        thread.interrupt();

    }


    private static class LongComuptionTask implements Runnable {

        private BigInteger base;
        private BigInteger power;

        public LongComuptionTask(BigInteger base, BigInteger power) {
            this.base = base;
            this.power = power;
        }

        @Override
        public void run() {
            System.out.println(pow(base, power));
        }
    }

    private BigInteger pow(BigInteger base, BigInteger power) {
        BigInteger result = BigInteger.ONE;

        for(BigInteger i = BigInteger.ONE; i.compareTo(power) != 0; i.add(BigInteger.ONE)) {
            if(Thread.currentThread().isInterrupted()) {
                System.out.println("interrupted");
                return BigInteger.ZERO;
            }
            result = result.multiply(base);
        }

        return result;
    }
}
```
<br><br><br><br>

### Thread Daemon
   - 메인 스레드가 종료되어도 애플리케이션 종료를 막지 않음.
   - 주로 백그라운드 작업 (텍스트앱에서 시간마다 자동저장하는 기능)
   - 작업 스레드에서 실행되는 코드를 제어할 수 없는 경우, Daemon쓰레드가 앱 종료를 방해하는 일은 없어야함.


```java

    public static void main(String[] args) throws InterruptedException {

        Thread thread = new Thread(new LongComuptionTask(new BigInteger("2"), new BigInteger("10")));
        thread.setDaemon(true);
        thread.start();
        Thread.sleep(100);
        thread.interrupt();

    }


    private static class LongComuptionTask implements Runnable {

        private BigInteger base;
        private BigInteger power;

        public LongComuptionTask(BigInteger base, BigInteger power) {
            this.base = base;
            this.power = power;
        }

        @Override
        public void run() {
            System.out.println(pow(base, power));
        }


        private BigInteger pow(BigInteger base, BigInteger power) {
            BigInteger result = BigInteger.ONE;

            for(BigInteger i = BigInteger.ONE; i.compareTo(power) != 0; i.add(BigInteger.ONE)) {
                if(Thread.currentThread().isInterrupted()) {
                    System.out.println("interrupted");
                    return BigInteger.ZERO;
                }
                result = result.multiply(base);
            }

            return result;
        }
    }
```
<br><br><br><br><br>



### Thread join
  - 스레드는 각각 독립적이고 실행순서를 우리가 통제할 수 없다.



<br>

출처 - https://kmooc.udemy.com/course/java-multi-threading/ 
(Java 멀티스레딩, 병행성 및 성능 최적화 - 전문가 되기
)