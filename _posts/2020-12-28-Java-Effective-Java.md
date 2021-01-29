---
title:  "Effective Java 3/E"
categories:
  - Book
tags:
  - java
header:
  teaser: /assets/images/java/java.jpg
  image: /assets/images/java/java.jpg
---  
날 잡고 기간을 둬서 읽어야 되는 책이라기보다는 책의 구성과 목차를 파악한 뒤 필요한 내용이 생길때마다 챙겨서 보는게 좋을 것 같다.  
카테고리별로 나눠져 있으며 해당 카테고리에서 중요하다고 생각되는 내용들을 아이템 단위로 나눠서 알려주는 형식이다.  


내가 익혀야 된다고 생각하는 아이템은 아래와 같다.  

# 2장 객체 생성과 파괴
## Item 5
### 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라  

사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다. 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식을 사용하면 된다.  

### Dependency Injection
```java
public class SpellChecker{
  private final Lexicon dictionary;
  
  public SpellChecker(Lexicon dictionary){
    this.dictionary = Objects.requireNonNull(dictionary);
  }
  
  public boolean isValid(String word){ ... }
  public List<String> suggestions(String type){ ... }
}
```  

클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다.  
이 자원들은 클래스가 직접 만들게 해서도 안 된다. 대신 필요한 자원을 생성자에 넘겨주자. 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.  

# 3장 모든 객체의 공통 메서드
## Item 10
### `equals`는 일반 규약을 지켜 재정의하라  

그냥 두면 그 클래스의 인스턴스는 오직 자기 자신과만 같게 된다.  
그러니 다음에서 열거한 상황 중 하나에 해당한다면 재정의하지 않는 것이 최선이다.  

- 각 인스턴스가 본질적으로 고유하다  
- 인스턴스의 '논리적 동치성'을 검사할 일이 없다.  
- 상위 클래스에서 재정의한 `equals`가 하위 클래스에도 딱 들어맞는다.  
- 클래스가 private이거나 package-private이고 `equals` 메서드를 호출할 일이 없다.  

만약 재정의 한다면 반드시 일반 규약을 따라야 한다.  

### 재정의 일반 규약
- 반사성
  - `null`이 아닌 모든 참조 값 x에 대해, `x.equals(x)`는 `true`다.
- 대칭성
  - `null`이 아닌 모든 참조 값 x, y에 대해, `x.equals(y)`가 `true`면 `y.equals(x)`도 true다.
- 추이성  
  - `null`이 아닌 모든 참조 값 x, y, z에 대해, `x.equals(y)`가 `true`이고 `y.equals(z)`가 true`면 `x.equals(z)`도  `true`다.
- 일관성  
  - `null`이 아닌 모든 참조 값 x, y에 대해, `x.equals(y)`를 반복해서 호출하면 항상 `true`를 반환하거나 항상 `false`를 반환한다.
- `null` 아님  
  - `null`이 아닌 모든 참조 값 x에 대해, `x.equlas(null)`은 false다.  
  
꼭 필요한 경우가 아니면 재정의 하지 않아야 하며, 필요한 경우에는 위의 규약을 확실히 지켜가면서 재정의 해야 한다.

## Item 11
### `equals`를 재정의하려거든 `hashCode`도 재정의하라  

`equals`를 재정의할 때 `hashCode`를 재정의 하지 않는다면, 프로그램이 제대로 동작하지 않을 것이다.  
재정의한 `hashCode`는 Object의 API 문서에 기술된 일반 규약을 따라야 하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 구현해야한다.  

### `hashCode`일반 규약
- `equals` 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 `hashCode` 메서드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다.  
- `equals(Object)`가 두 객체를 같다고 판단했다면, 두 객체의 `hashCode`는 똑같은 값을 반환해야 한다.  
- `equals(Object)`가 두 객체를 다르다고 판단했더라도, 두 객체의 `hashCode`가 서로 다른 값을 반환할 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.  


# 4장 클래스와 인터페이스
## Item 14
### public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라  

아래처럼 퇴보한 클래스를 작성해서는 안된다.  

### 퇴보한 클래스
```java
class Point {
  public double x;
  public double y;
}
```   

이런 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못한다.  
철저한 객체 지향 프로그래머라면 아래와 같이 작성해야 한다.  

### 객체 지향적인 클래스
```java
class Point {
  private double x;
  prviate double y;
  
  public Point (double x, double y){
    this.x = x;
    this.y = y;
  }
  
  public double getX() { return x; }
  public double getY() { return y; }
  
  public void setX( double x ) { this.x = x; }
  public void setY( double y ) { this.y = y; }
}
```  

패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다.  
하지만 packkage-private 클래스 또는 private 중첩 클래스라면 데이터 필드를 노출한다 해도 문제될 것은 없다.


# 6장 열거 타입과 애너테이션
## Item 34
### int 상수 대신 열거 타입을 사용하라  

### 정수형 상수 열거 패턴
```java
public static final int APPLE_FUJI          = 0;
public static final int APPLE_PIPPIN        = 1;
public static final int APPLE_GRANNY_SMITH  = 2;

public static final int ORANGE_NAVEL        = 0;
public static final int ORANGE_TEMPLE       = 1;
public static final int ORANGE_BLOOD        = 2;
```

정수나 문자열 열거 패턴을 이용하게 된다면 해당 코드는 프로그래머가 그대로 하드코딩하게 만들고, 이러한 하드코딩된 열거 타입은 오타가 있어도 컴파일러는 확인할 길이 없으니 자연스럽게 런타임 버그가 생기며 문자열 비교에 따른 성능 저하 역시 당연한 결과다.  

이러한 열거 패턴의 대안이 바로 열거 타입이다.  

### 간단한 열거 타입
```java
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH}
public enum Orange { NAVEL, TEMPLE, BLOOD }
```

### 데이터와 메서드를 갖는 열거 타입
```java
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS(4.869e+24, 6.052e6),
    EARTH(5.975e+24, 6.378e6),
    MARS(6.419+23, 3.393e6),
    JUPITER(1.899+27, 7.149e7),
    SATURN(5.685+26, 6.027e7),
    URANUS(8.683+25, 2.556e7),
    NEPTUNE(1.024+26, 2.477e7);

    private final double mass;
    private final double radius;
    private final double surfaceGravity;
    
    private static final double G = 6.67300E-11;
    
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        surfaceGravity = G * mass / (radius * radius);
    }
    
    public double mass() { return mass; }
    public double radius() { return radius; }
    public double surfaceGravity() { return surfaceGravity; }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity; // F = ma
    }

}
```
열거 타입 상수 각각을 특정 데이터와 연결지으려면 생성자에서 데이터를 받아 인스턴스 필드에 저장하면 된다.

# 9장 일반적인 프로그래밍 원칙
## Item 61
### 박싱된 기본 타입보다는 기본 타입을 사용하라  

자바 데이터 타입은 int, double, boolean과 같은 기본 타입과 String, List와 같은 참조 타입 두 가지로 나눌 수 있다. 
그리고 각각의 기본 타입에 대응하는 참조 타입이 하나씩 있으며, 이를 박싱된 기본 타입이라고 한다. int, double, boolean에 대응하는 박싱된 타입은 Integer, Double, Boolean이다.  

박싱된 기본 타입을 사용할 때는 아래의 주의사항을 명시해야 한다.

### 박싱된 기본 타입 사용시 주의사항
- 기본 타입과 박싱된 타입 중 하나를 선택해야 한다면 가능하면 기본 타입을 사용하라.  
- 기본 타입은 간단하고 훨씬 빠르다. 박싱된 기본 타입을 써야 한다면 주의를 기울이자.  
- 오토박싱이 박싱된 기본 타입을 사용할 때의 번거로움을 덜어주지만 그 위험까지 없애주지는 않는다.  
- 두 박싱된 기본 타입을 == 연사낮로 비교한다면 식별성 비교가 이루어지는데, 이는 우리들이 원한 게 아닐 수 있다.  
- 같은 연산에서 기본 타입과 박싱된 기본 타입을 혼용하면 언박싱이 이루어지며, 언박싱 과정에서 NPE를 던질 수 있다.  
- 마지막으로, 기본 타입을 박싱하는 작업은 필요 없는 객체를 생성하는 부작용을 나을 수 있다.

그렇다면 박싱된 기본 타입은 언제 사용해야 하는가?

1. 컬렉션의 원소, 키, 값으로 사용한다.
2. 리플렉션을 통해 메서드를 호출할 때도 박싱된 기본 타입을 사용해야 한다.  

# 10장 예외
## Item 72
### 표준 예외를 사용하라  

자바 라이브러리는 대부분의 API에서 쓰기에 충분한 수의 예외를 제공한다.  

Exception, RuntimeException, Throwable, Error는 직접 재사용 하지 말자. 이 예외들은 다를 예외들의 상위 클래스이므로 안정적으로 테스트할 수 없다.  

### 자주 재사용되는 예외
- `IllegalArgumentException`
  - 허용하지 않는 값이 인수루 건네졌을 때
- `IllegalStateException`
  - 객체가 메서드를 수행하기에 적절하지 않은 상태일 때
- `NullPointerException`
  - null을 허용하지 않는 메서드에 null을 건넸을 때
- `ConcurrentModificationException`
  - 인덱스가 범위를 넘어섰을 때
- `UnsupportedOperationException`
  - 호출한 메서드를 지원하지 않을 때

# Reference
Effective Java 3/E
