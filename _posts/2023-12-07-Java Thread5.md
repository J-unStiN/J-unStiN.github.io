---
title : 자바 Thread_5
date : 2023-12-07 17:09:22 +09:00
categories : [자바, 멀티스레딩]
tags : [java] #소문자만 가능
---




# 원자적
- 모든 레퍼런스 할당은 원자적임
- long 이나 double 을 제외하고는 모두 원자적임.
- 예외인 이유는 64비트라서임
- 64비트 컴퓨터라도 실제로는 CPU가 두 개의 연산을 통해
32비트씩 읽어서 완료할 가능성이 높음

    => **volatile** 키워드를 선언시 앞에 붙여주면 원자적으로 계산이 가능함.

- 원자적 연산은 멀티스레드 애플리케이션을 쓰는 이유에
해당하는, 많은 작업을 병렬 실행하면서도 정확한 결과값을 도출할 수 있는 고성능 애플리케이션을 구축가능함.

```java
public static void main(String[] args) {
        Mertics mertics = new Mertics();

        BusinessLogic businessLogic1 = new BusinessLogic(mertics);
        BusinessLogic businessLogic2 = new BusinessLogic(mertics);
        MerticsPrint merticsPrint = new MerticsPrint(mertics);

        businessLogic1.start();
        businessLogic2.start();
        merticsPrint.start();

    }

    public static class Mertics {
        private long count = 0;
        private volatile double averge = 0.0;

        public synchronized void addSample(long sample) {
            double currentSum = averge * count;
            count++;
            averge = (currentSum + sample) / count;
        }

        public double getAverge() {
            return averge;
        }
    }

    public static class MerticsPrint extends Thread{
        private Mertics mertics;

        public MerticsPrint(Mertics mertics) {
            this.mertics = mertics;
        }

        @Override
        public void run() {
            while (true) {

                try {
                    Thread.sleep(100);
                }
                catch (Exception e) {

                }
                
                double currentAverage = mertics.getAverge();
                System.out.println("currentAverage = " + currentAverage);
            }
        }

    }


    public static class BusinessLogic extends Thread {
        private Mertics mertics;
        private Random random = new Random();

        public BusinessLogic(Mertics mertics) {
            this.mertics = mertics;
        }

        @Override
        public void run() {


            while (true) {
                long start = System.currentTimeMillis();
                try {
                    Thread.sleep(random.nextInt(10));
                } catch (Exception e) {

                }
                long end = System.currentTimeMillis();

                mertics.addSample(end - start);
            }
        }
    }
```

<br> <br> <br>


### 경쟁상태

- 공유 리소스에 접근하는 여러스레드가 있거나
그 중 최소한 한 스레드가 공유 리소스를 수정하는 경우로
스레드 스케줄리의 순서나 시점에 따라 결과가 달라지는 상황
- 공유 리소스에서 비원자적 연산이 실행되는게 문제
  
- 우선 경쟁 상태를 파악하고 경쟁 상태가 일어나는 임계영역을 동기화 블록을 넣어 보호해야함

<br> <br>

### 데이터 경쟁

```java
    public static void main(String[] args) throws Exception {
        SharedClass sharedClass = new SharedClass();

        ShardTest shardTest = new ShardTest(sharedClass);
        ShardTest2 shardTest2 = new ShardTest2(sharedClass);
        
        shardTest.start();
        shardTest2.start();
    }


    public static class ShardTest2 extends Thread{
        private SharedClass sharedClass;

        public ShardTest2(SharedClass sharedClass) {
            this.sharedClass = sharedClass;
        }

        @Override
        public void run() {
            for (int i = 0; i < Integer.MAX_VALUE; i++) {
                sharedClass.checkDataRace();
            }
        }
    }


    public static class ShardTest extends Thread{
        private SharedClass sharedClass;

        public ShardTest(SharedClass sharedClass) {
            this.sharedClass = sharedClass;
        }

        @Override
        public void run() {
            for (int i = 0; i < Integer.MAX_VALUE; i++) {
                sharedClass.increment();
            }
        }
    }


    public static class SharedClass {
        private int x = 0; // volatile으로 해결가능
        private int y = 0; // volatile으로 해결가능

        public void increment() {
            x++;
            y++;
        }

        public void checkDataRace() {
            if(y > x) {
                System.out.println("y > x 상태가 일어났습니다.");
            }
        }
    }
```
=> 컴파일러와 CPU가 성능 최적화와 하드웨어 활용을 위해 비순차적으로 명령을 처리하는 경우가 있음 (논리를 위반하진않음)

- 비순차적 명령어 처리가 없다면 프로그램 속도는 매우 느려짐
  
- 컴파일러 엔지니어들은 입력코드를 가져와서 재정렬해 CPU의 분기 예측 능력과 백테화 능력을 향상시키고 캐시 라인을 프리페치해서 미리 사용할 수 있도록 하고 다양한 최적화를 함
- 일부 명령이 사용할 수 없는 하드웨어를 필요로 하면 CPU가 명령을 비순차적으로 하지만 다른 명령은 CPU 주기를 낭비하는 대신에 다른 하드웨어에서 실행될 수 있음

```java

// 각각의 윗줄에 종속성이 있어 절대 논리가 위반될 수 없음
public void Funct() {
    x = 1;
    y = x+2;
    z = y+10;
}
```

```java
/* CPU나 컴파일러 관점에서는 해당 메서드들의 논리가 동일함
*/

public void Funct1() {
    x++;
    y++;
}

public void Funct2() {
    y++;
    x++;
}
```
- Java는 일반적으로 다른 스레드를 통해 동시에 일어나는 연산에 대해서는 시맨틱 이전의 순서는 보장하지 않지만, 예외인 경우가 몇 가지 있습니다.
  
  ### 순서를 지키는 솔루션
  1. synchoronized 키워드를 사용해서 동시 실행에 대응하고 읽기, 쓰기, 공유 변수로부터 보호할 수 있음.
     - synchoronized가 선언된 메서드를 재정렬해도 문제가 되지않음
  하나의 스레드만 접근이 가능하기에 데이터 경쟁은 일어나지않음
  1. 공유 변수에 volatile 키워드를 적기
      - 잠금 오버헤드를 줄이고 처리 순서를 보장함
    ```java
    volatile int shared;
    public void func() {
        // 모든 명령은 이전에 실행 메모리 장벽
        Read or Write -> shared 변수
        // 모든 명령은 이후에 실행 메모리 장벽
    }
    ```

- 경쟁 상태
- 데이터 경쟁
  
=> 두 상황 모두 스레드가 공유 리소스를 사용하거나 스레드가 리소스를 수정하는 경우 (원인은 다름)
  - synchoronized, volatile 키워드로 문제를 해결가능함.
  - volatile는 변수 타입의 읽기, 쓰기, long + double 타입에 대한 데이터 경쟁을 해결할 수 있음.
  - volatile는 순서가 보장되기에 모든 경우의 데이터 경쟁을 해결해줌
  - 한 스레드에 의해 수정된 모든 공유 변수는 보호해야함

<br>

출처 - https://kmooc.udemy.com/course/java-multi-threading/ 
(Java 멀티스레딩, 병행성 및 성능 최적화 - 전문가 되기
)