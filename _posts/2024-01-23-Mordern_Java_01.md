---
title : Modern_Java_01
date : 2024-01-23 11:37:00 +09:00
categories : [자바, 모던자바]
tags : [java] #소문자만 가능
---

asd
<br>


# Mordern Java

## 서론
- 필자는 자바를 학부생 이후로 처음 해본다. <br>
그 때 자바가 8버전이 처음나와서 매우 좋다고 한 교수님이 계셨는데, 수업에서 람다식에 대해 처음배웠었다. <br>
당시에는 자바는 웹이나 하는 사람애들이 하는거라는 인식이 있었고, <br> C++, C#를 하고 있던 나에게는 크게 와닿지가 않았고 '평생 할 일 없을건데 굳이?' 라는 생각이 들어 신경을 끄고 있다가 <br>
최근에 들어서야 웹 개발자로 커리어를 전환을 해야하는 일이 있어, 약 10년의 세월이 지나서야 다시 자바를 보게되고, 모던 자바에 대해서 이해도를 높히기 위해 이 글을 적어본다.
---
---

<br><br>

## 모던 자바 8
- 자바 8은 더 다양한 프로그래밍 도구 그리고 다양한 프로그래밍 문제를 더 빠르고 정확하며 쉽게 유지 보수할 수 있다는 장점을 제공한다. 
- 자바 8에 추가된 기능은 자바에 없던 완전히 새로운 개념이지만 현 시장에 요구하는 기능을 효과적으로 제공한다. 

<br>

### 모던 자바 8의 프로그래밍 개념

1. 스트림 처리 - 조립라인 처럼 스트림 API는 파이프라인을 만드는 데 필요한 많은 메서드를 제공한다. 또한 스레드라는 복잡한 작업을 사용하지 않으면서 공짜로 병렬성을 얻을 수 있다.
2. 동작 파라미터화(behavior parameterization)로 메서드에 코드 전달하기 - 동작(메서드)을 파라미터화 해서 전달 가능하다. 함수형 프로그래밍 기술을 이용한다.
3. 병렬성과 공유 가변 데이터 - 병렬성을 공짜로 얻을 수 있지만 이를 얻기 위해 포기해야 하는 점이 있다. 안전하게 실행할 수 있는 코드를 만들려면 <U>공유된 가변 데이터(shared mutable data)</U>에 접근하지 않아야 한다.

--- 

<br><br>


## 람다  asd
- 람다는 이름이 없는 함수와 같다
- 익명 함수로 람다를 호출 가능
  - 파라미터
  - 리턴타입
- 변수에 할당되어 자바의 다른 변수처럼 통과 가능

```java
() == 람다 인풋 파라미터
->  == 화살표
{} == 람다 바디
```

<br>

### 람다의 특징
- 단일 추상적 메소드(기능적 인터페이스)
    - 하나의 추상적 메서드를 가진 인터페이스
    - @FunctionlInterface 이 달려있음.

<br>


### Runnable 예시
```java
Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("Inside Runnable 1");
            }
        };

        new Thread(runnable).start();
```
- 자바 8 이전은 직접 명령형 프로그래밍으로 코드를 작성해야만했다.

<br>

```java
Runnable runnableLambda = () -> {System.out.println("Inside Runnable 2");};

        Runnable runnableLambdaMultiStatements = () -> {
                                        System.out.println("Inside Runnable 3");
                                        System.out.println("Inside Runnable 3");
        };

        Runnable runnableLambdaSimple = () -> System.out.println("Inside Runnable 3");


        new Thread(runnableLambda).start();
        new Thread(runnableLambdaMultiStatements).start();
        new Thread(runnableLambdaSimple).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Inside Runnable 3");
            }
        }).start();

        new Thread(() -> System.out.println("Inside Runnable 4")).start();
```
- 람다는 단일 문장이나 식만 있으면 중괄호 없이 작성 가능하다.
- 람다식으로 작성하면 굳이 익명클래스로 작성 안해도됨.

<br><br>


### Comparator 예시
```java
Comparator<Integer> comparator = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1.compareTo(o2);
            }
        };
```

<br>

```java
Comparator<Integer> comparator = (Integer a, Integer b) -> {return a.compareTo(b);};

Comparator<Integer> comparator = (a, b) -> a.compareTo(b);
```

- Comparator 도 람다식으로 인터페이스를 구현해 사용 가능함.



<br><br><br>

### 출처
---
Modern Java - Learn Java 8 Features By coding it <br>
https://github.com/ckddn9496/modern-java-in-action