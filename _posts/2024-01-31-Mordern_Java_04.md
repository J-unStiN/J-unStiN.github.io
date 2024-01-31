---
title : Modern_Java_04
date : 2024-01-31 11:37:00 +09:00
categories : [자바, 모던자바]
tags : [java] #소문자만 가능
---


<br>


# Mordern Java


### Stream Factory - of, generate, iterate
---
```java
List<String> myList = List.of("kim","lee","son");
```
- of: 타입을 선언할 때 제네릭의 타입의 요소를 넣어서 값을 생성.

<br>

```java
stream.iterate(1, x -> x*2)
        forEach(System.out::println);
// 무한반복으로 계속 1부터 2를 곱함.

stream.iterate(1, x -> x*2)
        .limit(10)
        .forEach(System.out::println);
// 무한반복이 되면 안되니, 필요한만큼 정함. 

```
- 첫번째 파라미터의 값을 활용해서 반복적으로 동작함.

<br>

```java
Supplier<Integer> supplier = new Random()::nextInt;

        List<Integer> integerList1  = Stream.generate(supplier)
                .limit(10)
                .collect(toList());
```

- generate: 함수형 인터페이스를 이용해서 반복적으로 동작함.

<br>

- iterate 와 generate 모두 어디까지 요소를 반복할건지 정해줘야한다. 만약 없다면 무한반복이 되기때문이다.

---

<br><br>










<br><br><br>

### 출처
---
Modern Java - Learn Java 8 Features By coding it <br>
https://github.com/ckddn9496/modern-java-in-action