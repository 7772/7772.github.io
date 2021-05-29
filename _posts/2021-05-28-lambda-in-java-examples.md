---
layout: post
title: 자바의 람다 표현식 - 활용 방법
comments: true
categories: Java 
---

[이 전 포스팅](https://7772.github.io/2021-05-27-lambda-in-java/)에서 람다의 정의와 사용방법에 대하여 알아보았다.<br/>
이번 포스팅에서는 람다가 적용되어 있지 않은 기존 코드에 람다를 적용하여 변화를 살펴보고, 어떠한 점을 얻게 되었는지 살펴보자.

## 1. 반복되는 동작을 발견하기

동작 파라미터화는 반복되는 동작을 파라미터로 전달받음으로써 해당 부분에 대한 제어권을 호출 영역으로 넘기는 것이다.<br/>

```java
public String processFile() throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return br.readLine();
    }
}
```

위의 예제 코드에서와 같이 파일 처리같은 작업은 대부분 자원을 열고, 처리한 다음, 자원을 닫는 순서로 진행된다.<br/>
그림으로 살펴보면 다음과 같다.

<img src="../img/spring-lambda-2.png"/>

작업 A 와 작업 B 부분의 동작을 파라미터화 시킬 수 있다. 그렇게 함으로써, 초기화/준비 및 정리/마무리 시에 중복되는 코드를 줄일 수 있어 재사용이 가능해진다.<br/>

우리가 준비한 코드의 라인에서는 `return br.readLine();` 부분이 파라미터화 시킬 대상이다.<br/>
즉, 읽어온 파일에 대하여 어떤 동작을 수행할 것인지를 파라미터화 시킨다.

<br/>

## 2. 함수형 인터페이스를 이용해서 동작을 전달하기

파라미터화 하고자 하는 대상이 어떤 인자와 리턴값을 요구하는지를 먼저 파악해야 한다.<br/> 

```java
public String processFile() throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return br.readLine();
    }
}
```

위 코드를 보면 `processFile` 함수는 `BufferReader` 클래스의 인스턴스 내 `readLine` 메소드를 호출하여 그 결과로 `String`을 리턴하고 있다.<br/>

따라서 람다가 가질 시그니처는 `BufferedReader -> String` 형식이 된다.
이것을 `@FunctionalInterface`를 이용하여 구현하면 아래와 같다.<br/>

```java
@FunctionalInterface
public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
}
```

이제 정의한 인터페이스를 `processFile` 메소드가 인수로 받도록 하여, 메소드 내에서 람다를 사용할 준비를 하자.<br/> 

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
    // ...
}
```

<br/>

## 3. 동작 실행하기

동작을 파라미터화 한 `processFile` 메소드는 함수형 인터페이스의 추상 메소드를 실행하도록 구현한다.<br/>

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
    try (BuffredReader br = new BufferedReader(new FileReader("data.txt"))) {
        return p.process(br);
    }
}
```

위 코드는 컴파일 시점에 함수형 인터페이스 `BuffredReaderProcessor`를 구현한 인스턴스인지를 검사하고, 실제 동작은 런타임 시점에 일어나도록 설계되었다.

<br/>

## 4. 동작 전달하기

이제 `processFile` 메소드에 람다를 전달할 수 있다.<br/>
전달할 람다는 함수형 인터페이스 `BufferedReaderProcessor`를 만족해야 한다.

한 행을 처리하는 코드이다.

```java
String oneline = processFile((BufferedReader br) -> br.readLine());
```

다음과 같이 람다를 변수에 담아 사용할 수도 있다. 

```java
BuffredReaderProcessor processor = (BufferedReader br) -> br.readLine();
String oneline = processFile(processor);
```

동작을 파라미터화 하였기 때문에, 인터페이스를 만족시키는 한 어떠한 코드도 실행 가능하다.

```java
String oneline = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

지금까지 함수형 인터페이스를 이용하여 람다를 전달하는 방법을 살펴보았다.<br/>
다음 포스팅에서는 람다를 사용하는데 유용한 자바 8에 추가된 새로운 인터페이스들을 익혀보겠다. 

<br/>

**이 포스팅은 *라울-게이브리얼 우르마*, *마리오 푸스코*, *앨런 마이크로프트*가 지은 [모던 자바 인 액션](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791162242025) 을 참고했다.**

<br/><br/>
