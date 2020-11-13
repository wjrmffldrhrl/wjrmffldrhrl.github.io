---
title:  "Type conversion in Java"
categories:
  - Java
tags:
  - java
header:
  teaser: /assets/images/java/java.jpg
  image: /assets/images/java/java.jpg
---  

자바에서 데이터 타입 변경 방법

# Integer

## int to String

```java
// String.valueOf(i)
int a = 1;
String aStr = String.valueOf(a);

// "" + i -> 빈문자열에 String을 +로 연산시 컴파일러에서 데이터 타입 판단 작업이 발생한다.
// 그래서 효율이 떨어진다.
int b = 2;
String bStr = a + "";

// Integer.toString(i);
int c = 3;
String cStr = Integer.toString(c);

// String.format("%d",i);
int d = 4;
String dStr = String.format("%d",d);

```

## int to char

```java
// ASCII
int a = 65;
char aChar = (char) a; // aChar -> 'A'

// '1'
int b = '1';
char bChar = (char) b; // b -> '1'

// i + '0'
int c = 3;
char cChar = (char) (c + '0');

// Character.forDigit(i, radix);
int radix = 10; // 10진수
int d = 4;
char dChar = Character.forDigit(d, radix); // dChar -> '4'

int radix = 16; // 16진수
int e = 5;
char eChar = Character.forDigit(e, radix); // eChar -> 'c'

// Integer.toString(i).charAt(0);
int f = 6;
char fChar = Integer.toString(f).charAt(0);
```
  
    
    
# String

## String to int

```java
// Integer.parseInt(str);
String a = "100";
int aInteger = Integer.parseInt(a);

// Integer.valueOf(str);
String b = "100";
int bInteger = Integer.valueOf(b);
```

## String to char

```java
/// str.charAt(0);
String a = "a";
char aChar = a.charAt(0);

// char[]로 변환, toCharArray()
String b = "abcdefg";
char[] bCharArray = b.toCharArray()[0];
```
  
  
  
# Character

## char to int

```java
// ASCII
char a = 'a';
int aInteger = a; // aInteger -> 97

char b = '1';
int bInteger = b; // bInteger -> 49

// char - '0'
char c = '2';
int cInteger = c - '0'; // cInteger -> 2

//  Character.getNumericValue(char);
char d = '3';
int dInteger = Character.getNumericValue(d);

// String.valueOf()
char e = '4';
int eInteger = Integer.parseInt(String.valueOf(e));
```

## char to String

```java
// String.valueOf()
char a = 'a';
String aStr = String.valueOf(a);

// Character.toString()
char b = 'b';
String bStr = Character.toString(b);
```
  
  
  
# Reference

[[Java] 문자열 형변환 방법 비교(valueOf, toString, Casting)](https://m.blog.naver.com/PostView.nhn?blogId=yysvip&logNo=220105002997&proxyReferer=https:%2F%2Fwww.google.com%2F)

[Java Convert int to String - javatpoint](https://www.javatpoint.com/java-int-to-string)

[이광기 블로그 : 네이버 블로그](http://blog.naver.com/gglee0127/221286184882)

[Java Convert char to int - javatpoint](https://www.javatpoint.com/java-char-to-int)
