---
title: "Java 실행 매커니즘"
date: 2024-04-07 07:40:00 +09:00
categories: [ java ]
tags:
  [
    jvm, java, byteCode
  ]
---
## 개요


### Java의 실행 매커니즘 
자바는 소스 코드를 컴파일하여 바이트 코드로 변환합니다. 이 바이트 코드는 주로 .class 파일 형태로 저장되며, 이들은 JAR 또는 WAR 파일에 패키징 됩니다.
자바 프로그램을 실행 시, JVM은 바이트 코드를 해석하고 필요한 경우 JIT 컴파일러를 통해 해당 코드를 기계어로 변환하여 실행합니다.
이렇게 빌드된 바이트 코드는 자바가 실행 가능한 CPU 아키텍처와 OS에서 추가적인 빌드 없이 실행될 수 있어 편의성을 제공합니다. 이러한 특성으로 인해 자바는 컴파일과 동시에 인터프리트하는 이중 매커니즘을 갖는 언어입니다.

![java-compile.jpg](/assets/img/java-compile.jpg)

### Java, 컴파일언어 비교
그러나 컴파일 과정에서 바이트 코드를 생성하는 자바는 C나 C++과 같은 컴파일 언어에 비해 성능이 약간 떨어질 수 있습니다. Java와 달리 컴파일 언어들은 코드를 미리 기계어로 변환하여 실행 가능한 형태로 준비하기 때문입니다. 
다만 컴파일을 통해 생성된 기계어는 빌드환경의 CPU아키텍처에 종속적이기 때문에 다른 환경에서 실행하고 싶다면 해당 빌드 환경에서 빌드를다시해야하는 번거로움이 있습니다.
그렇다면 인터프리트방식을 취하는 Java는 느리기만 한것일까요? 이러한 성능차이를 해결하기 위해 JVM에서는 앞서 언급한 JIT 컴파일러를 도입합니다. 적시에 기계어를 만들어낸다는 의미 입니다.
JIT 컴파일러는 바이트 코드를 머신 코드로 변환하는 과정에서 반복적으로 실행되는 코드 블록을 선택하여 해당 블록을 기계어로 컴파일하고, 이를 캐시에 저장하여 재사용함으로써 성능을 향상시킵니다. 이러한 최적화 과정은 추가적인 성능 향상을 가져옵니다.

## 예시 
아래는 간단한 자바 덧셈 코드입니다.

### Source Code
```java
public class Main {
  public static void main(String[] args) {
    int a = 5;
    int b = 3;
    int sum = a + b;

    System.out.println("sum : " + sum);
  }
}
 ```

### Byte Code
```
public class io/concurrency/Main {
  // compiled from: Main.java
  // access flags 0x19
  public final static INNERCLASS java/lang/invoke/MethodHandles$Lookup java/lang/invoke/MethodHandles Lookup

  // access flags 0x1
  public <init>()V
   L0
    LINENUMBER 3 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V
    RETURN
   L1
    LOCALVARIABLE this Lio/concurrency/Main; L0 L1 0
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x9
  public static main([Ljava/lang/String;)V
   L0
    LINENUMBER 5 L0
    ICONST_5
    ISTORE 1
   L1
    LINENUMBER 6 L1
    ICONST_3
    ISTORE 2
   L2
    LINENUMBER 7 L2
    ILOAD 1
    ILOAD 2
    IADD
    ISTORE 3
   L3
    LINENUMBER 9 L3
    GETSTATIC java/lang/System.out : Ljava/io/PrintStream;
    ILOAD 3
    INVOKEDYNAMIC makeConcatWithConstants(I)Ljava/lang/String; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/StringConcatFactory.makeConcatWithConstants(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
      // arguments:
      "sum : \u0001"
    ]
    INVOKEVIRTUAL java/io/PrintStream.println (Ljava/lang/String;)V
   L4
    LINENUMBER 10 L4
    RETURN
   L5
    LOCALVARIABLE args [Ljava/lang/String; L0 L5 0
    LOCALVARIABLE a I L1 L5 1
    LOCALVARIABLE b I L2 L5 2
    LOCALVARIABLE sum I L3 L5 3
    MAXSTACK = 2
    MAXLOCALS = 4
}
 ```

## 결론 
오늘은 자바 어플리케이션이 실행되는 전반적인 과정에 대해 알아 보았습니다.
Byte코드를 해석할 필요를 아직은 느끼지 못하지만, 다양한 장애상황을 겪게되면 Byte코드를 분석해야하는 상황을 만날지도 모르겠습니다.
다음엔 Byte코드를 생성하고, 번역하는 JVM에 대해 알아보겠습니다.

