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



---


<br> <br> <br>



# ReentrantReadWriteLock

- ReadLock, WriteLock을 합쳐서 하나의 락으로 만든 것

<br>

### 경쟁상태
- 리소스를 공유하는 스레드가 다양함
- 하나의 스레드는 리소스를 수정함 

### 해결방법은 상호배제
- 스레드가 공유 리소스를 읽기, 쓰기, 둘다해도 상관없음
- 임계영역을 통해 스레드 하나만 실행가능함
  
- 지금까지의 락은 스레드 여러개가 공유 리소스에 접근하는 것을 막음
- 읽기작업이 빠르고, 락이 경쟁상태를 막는 일도 거의 발생하지않음

<br>

## 만약 캐시처럼 읽기작업을 주로 하거나, 읽기작업이 느릴경우??
  1. 변수를 여러개 읽기 작업하기 !
  2. 데이터 구조가 복합하면 작업이 느려짐
- 읽기 작업을 주로 하는 스레드 여러개가 공유 자원을 읽는 것을 막으면 성능 저하가 생김.
  
<br><br>

## 그래서 ReentrantReadWriteLock 사용 !!!
- 락 기능을 제공하지않음 -> 쿼리메서드 !
- 내부 락이 두개 있는데, 읽기락(readLock)과 쓰기락(writeLock)이 있음
- 쓰기락을 통해 스레드가 임계영역에 접근하는 것을 막고 해당 영역에서 공유 리소스를 수정함
- 공유 리소스를 읽는 작업만 하려면 읽기락을 사용해 임계 영역을 보호하고 작업이 완료되면 락을 해제함 => 읽기 스레드들이 읽기락을 건 임계영역에 여러개 접근이 가능함
- 읽기락, 쓰기락은 서로 차단하는 것도 가능함
만약 어떤 스레드가 쓰기락을 얻으면 해당락이 풀릴때까지 다른 스레드는 읽기 락을 얻을 수 없음. (반대의 상황도 가능함)

<br>

```java

    public static final int HIGHET_PRICE = 1000;

    public static void main(String[] args) throws InterruptedException {
        InvertoryDatabase invertoryDatabase = new InvertoryDatabase();

        Random random = new Random();
        for (int i = 0; i < 100000; i++) {
            invertoryDatabase.addItem(random.nextInt(HIGHET_PRICE));
        }

        Thread writer = new Thread( () -> {
           while (true) {
               invertoryDatabase.addItem(random.nextInt(HIGHET_PRICE));
               invertoryDatabase.removeItem(random.nextInt(HIGHET_PRICE));

               try{
                   Thread.sleep(10);
               }catch (Exception e) {
               }
           }
        });

        writer.setDaemon(true);
        writer.start();

        int nuberTreadTheads = 7;
        List<Thread> readers = new ArrayList<>();

        for (int i = 0; i < nuberTreadTheads; i++) {
            Thread reader = new Thread(() -> {
                for (int j = 0; j < 100000; j++) {
                    int upperBoundPrice = random.nextInt(HIGHET_PRICE);
                    int lowerBoundPrice = upperBoundPrice > 0 ? random.nextInt(upperBoundPrice) : 0;
                    invertoryDatabase.getNumberOfItemInPriceRange(lowerBoundPrice, upperBoundPrice);
                }
            });

            //reader.setDaemon(true);
            readers.add(reader);
        }

        long start = System.currentTimeMillis();

        for (Thread reader : readers) {
            reader.start();
        }

        for (Thread reader : readers) {
            reader.join();
        }


        long end = System.currentTimeMillis();

        System.out.println(end - start);

    }


    public static class InvertoryDatabase {
        // 레드 블랙 트리
        private TreeMap<Integer, Integer> priceToCountMap = new TreeMap<>();
        // 기존 락
        private ReentrantLock lock = new ReentrantLock();

        // 읽기 쓰기 락
        private ReentrantReadWriteLock reentrantReadWriteLock = new ReentrantReadWriteLock();
        // 읽기 락
        private Lock readLock = reentrantReadWriteLock.readLock();
        // 쓰기 락
        private Lock writeLock = reentrantReadWriteLock.writeLock();

        public int getNumberOfItemInPriceRange(int loverBound, int upperBound) {
            // 읽기부분 락
            //lock.lock();
            readLock.lock();
            try {
                Integer fromKey = priceToCountMap.ceilingKey(loverBound);
                Integer toKey = priceToCountMap.ceilingKey(upperBound);

                if (fromKey == null || toKey == null) {
                    return 0;
                }

                NavigableMap<Integer, Integer> rangeOfPrices =
                        priceToCountMap.subMap(fromKey, true, toKey, true);

                int sum = 0;

                for (int numberOfItemForPrice : rangeOfPrices.values()) {
                    sum += numberOfItemForPrice;
                }

                return sum;
            } finally {
                //lock.unlock();
                readLock.unlock();
            }
        }


        public void addItem(int price) {
            // 쓰기부분 락
            //lock.lock();
            writeLock.lock();
            try {
                Integer itemForPrice = priceToCountMap.get(price);

                if (itemForPrice == null) {
                    priceToCountMap.put(price, 1);
                } else {
                    priceToCountMap.put(price, itemForPrice + 1);
                }
            } finally {
                //lock.unlock();
                writeLock.unlock();
            }
        }

        public void removeItem(int price) {
            // 쓰기부분 락
            //lock.lock();
            writeLock.lock();
            try {
                Integer itemForPrice = priceToCountMap.get(price);

                if (itemForPrice == null || itemForPrice == 1) {
                    priceToCountMap.remove(price);
                } else {
                    priceToCountMap.put(price, itemForPrice - 1);
                }
            } finally {
                //lock.unlock();
                writeLock.unlock();
            }
        }
    }

```

- 기존 락에서는 다수의 Reader스레드가 공유 리소스에 접속하지못했음
- 읽기쓰기락을 통해 읽기부분의 락을 여러개 얻고 읽음 
- 성능차이는 3~4배차이남 (위 코드기준 - 하드웨어 성능에 따라 다름)
- 읽기 작업만 실행하지 않는 곳에서는 일반적인 락보다 읽기쓰기 락의 효율성이 더 좋음
