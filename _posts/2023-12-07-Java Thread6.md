---
title : 자바 Thread_6
date : 2023-12-07 17:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능
---

<br> <br>

# 락킹
- 멀티스레드 애플리케이션을 제작할 때 **Fine-Grained(세밀한 락킹)**, **Coarse-Grained(성긴 락킹)** 을 선택할 수 있음.

- Coarse-Grained
  - 단일 락킹만 신경쓰면 된다
  - 공유 리소스에 액세스할 떄마다 해당 락킹만 사용하면 됨
  - 메서드에 synchronized 키워드로 해결 가능함

=> 전체 리소스를 잠그는것으로 간단하지만 성능이 중요하다면 최적의 방법은 아님 !

- Fine-Grained
  - 모든 리소스에 개별 락킹을 생성하는 것과 같음
  - 병렬성은 키우고 경쟁을 낮춤

    => 데드락에 걸릴 문제가 생김

<br> <br> <br> <br>


# 데드락
- 모두가 움직이려고 하는데, 다른 사람이 움직인다고 기다리느라 나도 못움직이는 현상 (교착현상) => 뻗는 현상

<br>

```java

    // 스레드A가 B를 락을 잡고있는와중에
    // 스레드B가 A를 락을 잡고있어서 서로 접근을 못하고 대기를 해서 데드락이 걸린 상황

    public static void main(String[] args) throws InterruptedException {
        Intersection intersection = new Intersection();
        Thread trainA = new Thread(new TrainA(intersection));
        Thread trainB = new Thread(new TrainB(intersection));

        trainA.start();
        trainB.start();
    }


    public static class TrainB implements Runnable {
        private Intersection intersection;
        private Random random = new Random();

        public TrainB(Intersection intersection) {
            this.intersection = intersection;
        }

        @Override
        public void run() {
            while (true) {
                long sleepTime = random.nextInt(5);
                try {
                    Thread.sleep(sleepTime);
                } catch (Exception e){
                }
                intersection.takeRoadB();
            }
        }
    }

    public static class TrainA implements Runnable {
        private Intersection intersection;
        private Random random = new Random();

        public TrainA(Intersection intersection) {
            this.intersection = intersection;
        }

        @Override
        public void run() {
            while (true) {
                long sleepTime = random.nextInt(5);
                try {
                    Thread.sleep(sleepTime);
                } catch (Exception e){
                }
                intersection.takeRoadA();
            }
        }
    }


    public static class Intersection {
        private Object roadA = new Object();
        private Object roadB = new Object();

        public void takeRoadA() {
            synchronized (roadA) {
                System.out.println("RoadA" + Thread.currentThread().getName());

                synchronized (roadB) {
                    System.out.println("");

                    try {
                        Thread.sleep(1);
                    } catch (Exception e) {
                    }
                }
            }
        }

        public void takeRoadB() {
            synchronized (roadB) {
                System.out.println("Road B" + Thread.currentThread().getName());

                synchronized (roadA) {
                    try {
                        Thread.sleep(1);
                    } catch (Exception e){
                    }
                }
            }
        }
    }
```





### 해결방안
- 상호 배제 - 한 번에 한 스레드만 단독으로 리소스에 액세스함
- 점유와 대기 - 최소 하나의 스레드가 리소스를 점유하며 다른 리소스에 대기
- 비선점 할당 - 스레드가 사용 완료할 때까지 리소스를 사용할 수 없는 것, 다른 스레드의 리소스를 뺏을 수 없고 다 쓸때까지 기다려야함
- 순환대기 - 데드락에 빠진 스레드에서 발견할 수 있는데 서로 다른 스레드가 대기함

=> 위 네가지 조건이 갖춰지면 데드락이 매우 잘 걸린다
그래서 네가지 조건중 하나라도 충족하지않게 만들어주는것으로 해결할 수 있다

<br>

1. 마지막 조건인 순환 대기를 예방하는 것으로 동일한 순서로 공유 리소스를 잠그고 모든 코드에 해당 순서를 유지하기


```java
    public static void main(String[] args) throws InterruptedException {
        Intersection intersection = new Intersection();
        Thread trainA = new Thread(new TrainA(intersection));
        Thread trainB = new Thread(new TrainB(intersection));

        trainA.start();
        trainB.start();
    }


    public static class TrainB implements Runnable {
        private Intersection intersection;
        private Random random = new Random();

        public TrainB(Intersection intersection) {
            this.intersection = intersection;
        }

        @Override
        public void run() {
            while (true) {
                long sleepTime = random.nextInt(5);
                try {
                    Thread.sleep(sleepTime);
                } catch (Exception e){
                }
                intersection.takeRoadB();
            }
        }
    }

    public static class TrainA implements Runnable {
        private Intersection intersection;
        private Random random = new Random();

        public TrainA(Intersection intersection) {
            this.intersection = intersection;
        }

        @Override
        public void run() {
            while (true) {
                long sleepTime = random.nextInt(5);
                try {
                    Thread.sleep(sleepTime);
                } catch (Exception e){
                }
                intersection.takeRoadA();
            }
        }
    }


    public static class Intersection {
        private Object roadA = new Object();
        private Object roadB = new Object();

        public void takeRoadA() {
            synchronized (roadA) {
                System.out.println("RoadA" + Thread.currentThread().getName());

                synchronized (roadB) {
                    System.out.println("");

                    try {
                        Thread.sleep(1);
                    } catch (Exception e) {
                    }
                }
            }
        }

        public void takeRoadB() {
            synchronized (roadA) {
                System.out.println("Road A" + Thread.currentThread().getName());

                synchronized (roadA) {
                    try {
                        Thread.sleep(1);
                    } catch (Exception e){
                    }
                }
            }
        }
    }
```

- 락킹 순서를 통일하는것도 데드락을 예방할 수 있는 방법
- 프로그램이 단순하고 작을수록 훨씬 용이하고 적합할 수 있음
- 복잡한 프로그램은 다양한 곳에 락킹이 걸려있고 순서를 유지하는게 어려운 단점이 존재

<br>

### 대체방법

- 데드락을 감시하는 장치를 만들수있음
- 매우 낮은 레벨에서 주기적으로 특정 레지스터 상태를 체크해서 응답하지 않는 스레드가 있다면 데드락이라 판단하고 재가동함
- 락킹을 처리하기전에 다른 스레드에 의해 락킹이 처리되었는지 확인해서 스레드가 중단되는걸 막은 try lock이라는 방법이 존재
- synchronized는 중단된 스레드를 인터럽트할 수 없고, trylock도 사용할 수 없음 => 순서를 지키는게 불가능할때 적용

<br>

출처 - https://kmooc.udemy.com/course/java-multi-threading/ 
(Java 멀티스레딩, 병행성 및 성능 최적화 - 전문가 되기
)