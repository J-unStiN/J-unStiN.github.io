---
title : Modern_Java_03
date : 2024-01-24 11:37:00 +09:00
categories : [자바, 모던자바]
tags : [java] #소문자만 가능
---

asd
<br>


# Mordern Java

## 서론
- 원래 순서대로라면 Functional Interface 개념이 먼저 나오는게 맞지만, 상황상 3편부터 스트림에 관련된 내용을 적고 추후에 2편에서 글을 적도록 할 것.

<br>

- 사실상 자바의 꽃이 아닌가 싶다. <br>
스트림을 처음 볼 때는 난이도가 매우 높아서 좌절을 겪게될것이지만, 하다보면 아에 손도 못댈정도는 아니고, 뭐 어찌저찌 해내게되는 나를 볼 수 있게될것이다. <br>
결국 스트림API도 사람이 논리적인 순서대로 생각한것을 적기위한 도구이기에 차근차근 해나가면 되고, 스트림은 하는만큼 늘어난다는걸 꺠닫게된다.


<br><br>


## Streams API
- 자바 8에 나온 개념
- 컬렉션에 대한 작업을 수행하는것이 목적

<br>

### 컬렉션과 스트림 비교
- 스트림은 추가, 삭제, 수정 허용이 안됨.
- 데이터를 추가하려면 다른 방식으로 추가해야함.
- 컬렉션은 인덱스로 get해서 접근이 가능함.
- 스트림은 <U>지연 연산(Lazy Evaluation)</U>를 통해 결과값이 필요할 때까지 계산을 안한다.
  - 스트림은 한 번만 사용가능함.
  - 스트림은 내부반복을 통해 모든 요소를 반복함.
---
<br><br>

### Stream - map 
---
```java
List<String> namesUpperCase(List<Student> names){
        List<String> namesUpperCase = names.stream() //Stream<Student>
                .map(Student::getName) //Stream<String>
                .map(String::toUpperCase) // Stream<String> -> UpperCase
                .collect(toList()); // returns List // terminal operation
        return namesUpperCase;
    }
```


```java
 Map<String, Integer>  namesLengthMap(ArrayList<String> names){

        Map<String, Integer> namesLengthMap = names.stream()//Stream<String>
                .collect(toMap(String::toString,String::length)); // returns Map

        return namesLengthMap;
```









<br><br><br>

### 출처
---
Modern Java - Learn Java 8 Features By coding it <br>
https://github.com/ckddn9496/modern-java-in-action