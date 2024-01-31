---
title : Modern_Java_03(추가 및 수정중)
date : 2024-01-24 11:37:00 +09:00
categories : [자바, 모던자바]
tags : [java] #소문자만 가능
---

asd
<br>


# Mordern Java

## 서론
- 원래 순서대로라면 Functional Interface 개념이 먼저 나오는게 맞지만, 상황상 3편부터 스트림에 관련된 내용을 적고 추후에 2편에서 글을 적도록 할 것.

- 먼저는 코드를 올리고, 추후에 글에 내용을 추가.

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

### Stream Operations - map 
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
---


<br><br>


### Stream Operations - flatMap 
---

```java
List<String> printStudentActivities() {

        List<String> studentActivities = StudentDataBase.getAllStudents()
                .stream()
                .map(Student::getActivities) //Stream<List<String>>
                .flatMap(List::stream) //<Stream<String>
                .collect(toList());

        return studentActivities;

```

```java
List<String> printUniqueStudentActivities() {

        List<String> studentActivities = StudentDataBase.getAllStudents()
                .stream()
                .map(Student::getActivities)
                .flatMap(List::stream)
                .distinct()
                .sorted()
                .collect(toList());

        return studentActivities;
```

```java
long getStudentActivitiesCount() {

        long totalActivities = StudentDataBase.getAllStudents()
                .stream()
                .map(Student::getActivities)
                .flatMap(List::stream)
                .distinct()
                .count();

        return totalActivities;
```

---

<br><br>


### Stream Operations - distinct, count, sorted 
---
- distinct
  - 중복되는 요소를 제거하는 함수
- count
  - 요소의 개수를 반환하는 함수
- sorted
  - 요소를 정렬하는 함수

=> 만약 HashSet으로 반환하는것을 사용한다면 순서도 정렬하면서 중복도 제거된다. (상황에 맞게 기능뿐만 아니라 자료구조도 잘 사용하자)

---

<br><br>


### Stream Operations - sorted -> Comparator.comparing
---

```java
List<Student> sortStudentsByName(){

       return  StudentDataBase.getAllStudents().stream()
                .sorted(Comparator.comparing(Student::getName))
                .collect(toList());
    }
```
- sorted(Comparator.comparing(Student::getName)) 에서 특정 요소를 선택해 정렬이 가능함.


---

<br><br>




### Stream Operations - filter
---
```java
List<Student> filterStudents(){

        List<Student> filteredStudentList = StudentDataBase.getAllStudents()
                .stream()
                .filter(student -> student.getGpa()>=3.9)
                .filter(student -> student.getGender().equals("female"))
                .collect(toList());

        return filteredStudentList;
```
- 필터를 통해 원하는 조건에 맞는 요소만 처리.
- 반목문에 if문을 통해 원하는 요소만 넣는것과 같은 역할
- 조건이 여러개라면 필터를 여러개 넣으면 가능.

---

<br><br>




### Stream Operations - reduce
---
```java
List<Integer> mylist = List.of(1,3,5,7);

int result = mylist.stream
        .reduce(1,(a,b) -> a*b);

```
- a=1, b=1 => a*b 결과 리턴
- a=1, b=3 => a*b 결과 리턴
- a=3, b=5 => a*b 결과 리턴
- a=15, b=7 => a*b 결과 리턴
- 최종연산 결과 105로 반환

<br>

```java
String combineStudentNames(){

        return StudentDataBase.getAllStudents().stream()
                .map(Student::getName)
                .distinct()
                .reduce("",(a,b) -> a.concat(b));  // performs multiplication for each element in the stream.
```

```java
Optional<Student> getHighestGradeStudent(){

        Optional<Student> studentOptional =  StudentDataBase.getAllStudents().stream()
                .reduce((s1,s2)->(s1.getGpa()>s2.getGpa()) ? s1 : s2);
        return studentOptional;
```

---

<br><br>



### Stream Operations - Map + Filter + Reduce Pattern
---
```java
myList.stream()
        .filter(조건1)
        .filter(조건2)
        .map(Student::getNoteBo0ok)
        .reduce(0, Integer::sum);
```
- reduce의 2번째 파라미터에 sum, max를 통해 a+b, a>b 등을 직접 구현안해도된다.
- 매핑하기전에 filter의 조건을 추가해 map을 줄이는 방법

---

<br><br>



### Stream Operations - max + reduce
---
```java
List<Integer> myList = List.of(5,6,7,8,9);

myList.stream()
        .reduce(0,(x,y) -> x < y ? x : y);
// 5 -> y
// 6 -> y
// 7 -> y
// 8 -> y
// 9 -> y       
```
---

<br><br>



### Stream Operations - min + reduce
---

```java
List<Integer> myList = List.of(5,6,7,8,9);

myList.stream()
        .reduce(0,(x,y) -> x < y ? x : y);
// 5 -> y
// 6 -> y
// 7 -> y
// 8 -> y
// 9 -> y       
```

---

<br><br>

### Stream Operations - 작성중
---
```java
Optional<Integer> skip(List<Integer> integers){
        return  integers.stream()
                .limit(3)
                .reduce((a,b)-> a+b);
    }
```
```java
Optional<Integer> skip(List<Integer> integers){
        return  integers.stream()
                .skip(3)
                .reduce((a,b)-> a+b);
    }
```
- limit는 limit의 개수만큼 스트림에 있는 요소를 반복.
- skip은 이름처럼 skip에서 지정한 숫자만큼 건너뛰고 실행. 
---

<br><br>

### Stream Operations - anyMatch, allMatch, noneMatch
---
```java
boolean result = StudentDataBase.getAllStudents().stream()
                .allMatch(student -> student.getGpa()>=3.9);
```

```java
boolean result = StudentDataBase.getAllStudents().stream()
                .anyMatch(student -> student.getGpa()>=3.9);
```

```java
boolean result = StudentDataBase.getAllStudents().stream()
                .noneMatch(student -> student.getGpa()>=3.9);
```
- anyMatch: 하나의 속성이 있는지 확인해서 있으면 true를 반환함.
- allMatch: 전부다 맞아야 true를 반환.
- noneMatch: 하나라도 없으면 true를 반환함.
---

<br><br>


### Stream Operations - 작성중
---

---

<br><br>


### Stream Operations - 작성중
---

---

<br><br>


### Stream Operations - 작성중
---

---

<br><br>










<br><br><br>

### 출처
---
Modern Java - Learn Java 8 Features By coding it <br>
https://github.com/ckddn9496/modern-java-in-action