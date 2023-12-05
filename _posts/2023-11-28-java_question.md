---
title : 자바 질문 모음(추가,수정중)
date : 2023-11-28 18:30:22 +09:00
categories : [자바, 질문]
tags : [java] #소문자만 가능
---



# 자바 질문 모음집 (계속 추가, 수정중)


## 1. JVM이 무엇이고, 왜 플랫폼 독립적 언어인가?

- JVM 핵심은 컴파일이 된 바이트코드를 실행시켜주고 플랫폼 종속적 -> 이러한 이유로 크로스 플랫폼이 가능.

   ## 자바 실행 원리
   1. 소스 작성
   2. 컴파일
   3. 실행

  
자바 코드로 작성된 .java 파일을 컴퓨터가 이해할 수 있는 언어로 바꿔야함. -> 이 작업이 컴파일이라고 하고 javac 라는 명령어이다. <br>
.class 파일의 바이트코드가 생성되고 플랫폼에 맞춰서 클래스파일을 java라는 명령어로 실행하면 JVM이 플랫폼에 맞춰서 실행한다. 
JVM은 OS에 종속적이다. (운영체제마다 맞는 JVM 설치해야함)
JVM은 바이트코드를 실행시켜 프로그램을 구동하는 역할.
개발자는 플랫폼에 상관없이 자바코드를 작성하면 플랫폼에 맞게 설치된 jvm이 알아서 실행시켜주기에 플랫폼 독립적 언어라고 불린다. 

<br>

---
---

<br><br>

## 2. JVM, JRE, JDK 의 차이점?

- JVM : 컴파일된 클래스파일을 구동하는 역할 -> 실행자 역할
- JRE : 자바 프로그램이 실행될 수 있는 환경
  1. Class Lodaer
  2. Bytecode Verifier
  3. Java Virtual Machine 
    <br>

   - 컴파일된 클래스된 파일을 메모리에 올려주고 로딩된 클래스된 정보가 플랫폼에 실행되는데 문제가 있는지 없는지를 Bytecode Verifier가 실행되기전 검증함. Bytecode Verifier에 검증된 클래스 파일을 JVM에 최종적으로 실행시켜줌
    - JVM은 JRE의 한 부분이며, JRE은 클래스파일이 주어지는 동안 자바가 실행을 할 수 있는 환경 제공
  
<br>

- JRE : 컴파일된 바이트코드를 실행하는 환경 제공
- JDK : 프로그램 개발 이상의 환경 제공
  - 컴파일러 -> javac
  - 데이터베이스
  - 샘플 등 종합 선물세트와 같은 역할
 
=> JDK는 JRE을 통해 자바가 실행될 수 있는 환경뿐아니라 툴셋까지 제공

- 자바를 실질적으로 실행시켜주는건 JVM
- JVM + Class Loder, Byte Verifier

![jvm_jre_jdk](/assets/img/post20231128/11-28-javaquestion_1.png)

---
---

<br><br>

## 3. 오토박싱과 언박싱이 무엇인가요?

  - Primitive & Object 타입 두 가지가 있음.
  - Primitive 가벼운 데이터로 메모리의 스택영역에 저장됨.
  - Object 데이터는 상대적으로 무거우며, 실제 데이터는 힙영역에 있고 그 데이터를 참조주소만 스택메모리에 존재함.
  - => 둘이 서로 호환이 될 수가 없음 !!!

---
- J2SE 5 이후로 오토박싱과 언박싱이라는 개념이 생겨서 서로 직접 호환이 가능하게 됨.
    - Primitive DataType -> Wrapper Class 오토박싱
    - Wrapper Class -> Primitive DataType 언박싱

<br>

### Autoboxing
  - 컴파일러가 Primitive DataType -> Wrapper Class 를 하는 과정
```
int a = 2017;
Interger b = new Interger(a)
=>
Integer b = a // Autoboxing
```

### Unboxing
- 컴파일러가 Wrapper Class -> Primitive DataType 변환

```
Interger a = new Interger(2017);
int b = a.intValue();
=>
Interger a = new Interger(2017);
int b = a; // Unboxing
```

---
---

<br><br>

## 4. Java 스택, 힙 메모리 구조

### Stack
  - Primitive data
  - Reference


### Heap
  - Object
  - 가비지 컬렉션을 통해 공간을 확보하게 됨.

=> 메모리 관리 측면에서는 힙 영역을 신경써야하고, 만약 가득차면 메모리 에러가 발생하게 됨.


---
---

<br><br>

## 5. String 과 StringBuffer 차이점?
 - 결론부터 시작하면 문자열 병합이 자주 일어난다면 메모리 관리가 효율적이기에 StringBuffer를 사용함
 - String에서 힙에서 올라가면 불변성이 있어서 새로운 값을 가져야한다면 새롭게 객체를 생성함.
 - StringBuffer 가변성이 있어 기존의 객체를 이용해 값만 변경하기에 메모리측면에서 효율적임.

```
// Immutable
String s1 = "South Korea";
s1 = s1 + "Seoul"; // 이때 새로운 객체를 생성해서 할당함.

// mutable
StringBuffer s2 = new StringBuffer("South Korea");
s2.append("Seoul"); // 기존 객체에 값을 더함.
```


```
// Synchronization
StringBuffer s2 = new StringBuffer("South Korea");
s2.append("Seoul"); // 기존 객체에 값을 더함.

// non-Synchronization
StringBuilder s4 = new StringBuilder("South Korea");
s4.append("Seoul"); // 기존 객체에 값을 더함.
```

- Synchronization 동기화 지원여부 차이가 있음.
동기화는 여러 스레드가 공유할 경우, 키를 가지고 있는 스레드만 건드리고 나머지 스레드는 대기해야기에 무결성을 보장하지만, 속도가 더 느릴 수 밖에 없음.
- 멀티스레드를 고려하지않는 단순 문자열 병합은 StringBuilder가 더 빠름.


---
---

<br><br>

## 6. 자바에서 오버라이딩, 오버로딩에 대해 아는가?


### 오버라이딩
- 

<br>

### 오버로딩

---
---

<br><br>


## 7. String을 new()로 생성하는 것과 리터럴로 생성하는 것의 차이점?


---
---

<br><br>

## 8. static 키워드는 무엇인가?


---
---

<br><br>

## 9. 추상클래스와 인터페이스의 차이?


---
---

<br><br>


## 10. 자바 예외 처리란?


---
---

<br><br>


## 11. 확인되지 않는 예와 확인된 예외의 차이점?


---
---

<br><br>


## 12. 예외처리에서 throw, throws의 차이점?


---
---

<br><br>


## 13. 바이트 스트림과 문자 스트림의 차이?


---
---

<br><br>


## 14. try 또는 catch 블록에서 System.exit를 호출하면 어떻게 되는가? 



---
---

<br><br>


## 15. ArrayList 와 LinkedList 차이?


---
---

<br><br>


## 16. 반복자와 열거형의 차이점?


---
---

<br><br>


## 17. SimpleDateFormat 용도는 무엇이며 이를 표시하는 데 어떻게 사용할 수 있습니까?


---
---

<br><br>


## 18. Marker Interface 란?


---
---

<br><br>


## 19. 스레드를 생성하는 다양한 방법을 설명합니다. 어느 것이 더 나은가?


---
---

<br><br>


## 20. 교착상태란? (멀티쓰레드 상태에서 발생)


---
---

<br><br>


## 21. main() 메서드에서 static 수정자를 제거하면 어떻게 되는가?


---
---

<br><br>


## 22. PreparedStatement, CallableStatement 설명


---
---

<br><br>


## 23. RMI 아키텍쳐의 기본 원리?


---
---

<br><br>


## 24. JDBC 연결 단계 설명


---
---

<br><br>

## 25. 데이터베이스에서 이미지를 어떻게 저장하고 검색하는가?


---
---

<br><br>


