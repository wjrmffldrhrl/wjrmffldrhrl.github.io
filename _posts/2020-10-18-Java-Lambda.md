---
title:  "Java Lambda"
categories:
  - Java
tags:
  - java
header:
  teaser: /assets/images/java/java.jpg
  image: /assets/images/java/java.jpg
---  

# 람다식이란?

람다식은 간단히 말해서 메서드를 하나의 식으로 표현한 것이다. 메서드를 람다식으로 표현하면 메서드의 이름과 반환값이 없어지므로, 람다식을 익명함수라고도 한다.

```java
int[] arr = new int[5];
Arrays.setAll(arr, (i) -> (int)(Math.random() * 5) + 1 );
```

# 람다식 작성하기

람다식은 익명 함수답게 메서드에서 이름과 반환타입을 제거하고 매개변수 선언부와 몸통{} 사이에 ->를 추가한다.

`int max(int a, int b) { return a > b ? a : b; }`  -> `(a, b) -> a > b ? a : b`

`int square (int x) { return x * x; }` -> `x -> x * x`

# 함수형 인터페이스

자바에서 모든 메서드는 클래스 내에 포함되어야 하는데, 람다식은 어떤 클래스에 포함되어 있는것인가? 

사실 람다식은 익명 클래스의 객체와 동등하다.

`(int a, int b) -> a > b ? a : b`



```java
new Object(){
	// 함수명 max는 상관없다.
	int max(int a, int b) {
		return a > b ? a : b;
	}
}
```

람다식으로 정의된 익명 객체의 메서드를 호출하는 방법은, 참조변수가 있어야 객체의 메서드를 호출 할 수 있으니 먼저 해당 익명 객체의 주소를 f라는 참조변수에 저장한다.

`타입 f = (int a, int b) -> a > b ? a : b;`

이때, 참조변수 f의 타입은 어떤 것이어야하는가?

참조형이므로 클래스 또는 인터페이스가 가능하다.

```java
interface MyFunction {
	int max(int a, int b);
}
```

그러면 이 인터페이스를 구현한 익명 클래스의 객체는 다음과 같이 생성할 수 있다.

```java
MyFunction f = new MyFunction() {
								public int max(int a, int b) {
									return a > b ? a :b;
								}
							};

int big = f.max(5,3);
```

MyFunction 인터페이스에 정의된 메서드 max()는 람다식 `(int a, int b) -> a > b ? a : b` 와 선언부가 일치하므로 다음과 같이 대체할 수 있다.

`MyFunction f = (int a, int b) -> a > b ? a : b;`

`int big = f.max(5, 6);`

이렇게 인터페이스를 통해 람다식을 다룰 수 있으며, 람다식을 다루기 위한 인터페이스를 함수형 인터페이스 라고 부르기로 했다.

```java
@FunctionalInterface
interface MyFunction {
	public abstract int max(int a, int b);
}
```

단 함수형 인터페이스에는 오직 하나의 추상 메서드만 정의되어 있어야 한다는 제약이 있다. 그래야 람다식과 인터페이스의 매서드가 1:1로 연결 가능하기 때문이다. 

또한 람다식을 매개변수로 지정하는 것도 가능하다. 이렇게 하기 위해서는 해당 메서드의 매개변수 타입을 함수형 인터페이스로 지정해야 한다.

```java
@FunctionalInterface
interface MyFunction {
	void myMethod();
}

class Main {
	void aMethod(MyFunction f) {
		f.myMethod();
	}
			......
	aMethod(()-> System.out.println("myMethod"));
	
}
```   


## 람다식의 형변환  


함수형 인터페이스로 람다식을 참조할 수 있는 것일 뿐, 람다식의 타입이 함수형 인터페이스의 타입과 일치하는 것은 아니다.

- 람다식은 익명 객체이다.
- 익명 객체는 타입이 없다.
    - 정확하게는 타입이 있지만 컴파일러가 임의로 이름을 정하기 때문에 알 수 없는 것이다.
- 람다식은 타입이 없다.

`MyFunction f = (MyFunction)(()->{});`

람다식은 MyFunction 인터페이스를 직접 구현하지 않았지만, 이 인터페이스를 구현한 클래스의 객체와 완전히 동일하기 때문에 위와 같은 형변환을 허용하며 생략 가능하다.

람다식은 이름이 없을 뿐 분명히 객체인데도, Object타입으로 형변환 할 수 없다. 람다식은 오직 함수형 인터페이스로만 형변환이 가능하다.

`Object obj = (Object)(()->{}); // 에러`

굳이 형변환하려면, 먼저 함수형 인터페이스로 변환해야 한다.

`Object obj = (Object)(MyFunction)(()->{});`

# java.util.function 패키지

java.util.function 패키지에 일반적으로 자주 스이는 형식의 메서드를 함수형 인터페이스로 미리 정의해 놓았다.

## 매개변수가 하나 이하인 함수형 인터페이스

- java.lang.Runnable
    - 매개변수도 없고, 반환값도 없음
- Supplier<T>
    - 매개변수는 없고, 반환값만 있음.
- Consumer<T>
    - Supplier와 반대로 매개변수만 있고, 반환값이 없음
- Function<T, R>
    - 일반적인 함수, 하나의 매개변수를 받아서 결과를 반환
- Predicate<T>
    - 조건식을 표현하는데 사용됨.
    - 매개변수는 하나, 반환 타입은 boolean

## 매개변수가 두 개인 함수형 인터페이스

2개인 함수형 인터페이스는 이름 앞에 접두사 Bi가 붙는다.

- BiConsumer<T,U>
    - 두 개의 매개변수만 있고, 반환값이 없음
- BiPredicate<T,U>
    - 조건식을 표현하는데 사용된다.
    - 매개변수는 둘, 반환값은 boolean
- BiFunction<T,U,R>
    - 두 개의 매개변수를 받아서 하나의 결과를 반환한다.

## 매개변수가 3개 이상인 함수형 인터페이스

3개 이상의 매개변수를 갖는 함수형 인터페이스가 필요하다면 직접 만들어 사용해야한다.

만약 3개의 매개변수를 갖는 함수형 인터페이스를 선언한다면 다음과 같을 것이다.

```java
@FunctionalInterface
interface TriFunction<T, U, V, R> {
	R apply(T t, U u, V v);
}
```

## UnaryOperator, BinaryOperator

Function의 또 다른 변형으로 UnaryOperator와 BinaryOperator가 있다.

매개변수의 타입과 반환타입의 타입이 모두 일치한다는 점만 제외하고는 Function과 같다.

- UnaryOperator<T>
    - Function의 자손
    - Function과 달리 매개변수와 결과의 타입이 같다.
- BinaryOperator<T>
    - BiFunction의 자손, BiFunction과 달리 매개변수와 결과의 타입이 같다.

## 기본형을 사용하는 함수형 인터페이스

기본형 대신 래퍼 클래스를 사용하는 것은 비효율적이다. 그래서 보다 효율적인 기본형 함수 인터페이스들이 제공된다.

- AToBFunction
    - 입력이  A타입 출력이 B타입
    - ex) DoubleToIntFunction
- ToBFunction<T>
    - 출력이 B타입, 입력은 지네릭 타입
    - ex) ToIntFunction<T>
- AFunction<R>
    - 입력이 A타입이고 출력은 지네릭 타입
    - ex) IntFunction<R>
- ObjAFunction<T>
    - 입력이 T, A 타입이고 출력은 없다.

# Function의 합성과 Predicate의 결합

java.util.function 패키지의 함수형 인터페이스에는 추상메서드 외에도 디폴트 메서드와 static 메서드가 정의되어 있다. 모든 함수형 인터페이스의 메서드들은 유사하기 때문에 Function과 Predicate에 정의된 메서드만을 살펴본다.

## Function의 합성

- `default <V> Function<T,V> andThen(Function<? super R, ? extends V after)`
- `default <V> Function<V,R> compose(Function<? super V, ? extends T> before)`
- `static <T> Function<T,T> identity()`

수학에서 두 함수를 합성해서 새로운 함수를 만드는 것 처럼, 두 람다식을 합성해서 새로운 람다식을 만들 수 있다. 이 때 어떤 함수를 먼저 적용하느냐에 따라 결과는 달라진다.

함수 f, g가 있을 때, f.andThen(g)는 f를 먼저 적용하고 그다음 g를 적용한다. 반대로 f.compose(g)는 g를 먼저 적용하고 f를 적용한다.

예를 들어, 문자열을 숫자로 변환하는 함수 f와 숫자를 2진 문자열로 변환하는 함수 g를 andThen()으로 합성하여 새로운 함수 h를 만들 수 있다.

```java
// andThen
Function<String, Integer> f = (s) -> Integer.parseInt(s, 16);
Function<Integer, String> g = (i) -> Integer.toBinaryString(i);
Function<String, String> h = f.andThen(g);

System.out.println(h.apply("FF"));

// compose
Function<Integer, String> g = (i) -> Integer.toBinaryString(i);
Function<String, Integer> f = (s) -> Integer.parseInt(s, 16);
Function<Integer, Integer> h = f.compose(g);

System.out.println(h.apply(2));

```

```bash
11111111
16
```

`identity()`는 함수를 적용하기 이전과 이후가 동일한 '항등 함수'가 필요할 때 사용한다.

```bash
Function<String, String> f = x -> x;
// Function<String, String> f = Function.identity(); // 위 문장과 동일

System.out.println(f.apply("AAA")); // AAA 출력
```

잘 사용되지 않으며, 나중에 map()으로 번환작업할 때, 변환없이 그대로 처리하고자할 때 사용된다.

## Predicate의 결합

### Predicate

- `default Predicate<T> and(Predicate<? super T> other)`
- `default Predicate<T> or(Predicate<? super T> other)`
- `default Predicate<T> negate()`
- `static <T> Predicate<T> isEqual(Object targetRef)`

여러 조건식을 논리 연산자인 &&, ||, ! 으로 연결해서 하나의 식을 구성하는 것 처럼, 여러 Predicate를 and(), or(), negate()로 연결해서 하나의 새로운 Predicate로 결합할 수 있다.

```java
Predicate<Integer> p = i -> i < 100;
Predicate<Integer> q = i -> i < 200;
Predicate<Integer> r = i -> i % 2 == 0;

Predicate<Integer> notP = p.negate(); // i >= 100;

// 100 <= i && (i < 200 || i % 2 == 0)
// Predicate<Integer> all = notP.and(i -> i < 200).or(i -> i % 2 == 0); 
Predicate<Integer> all = notP.and(q.or(r));

System.out.println(all.test(150)); // true
```

`isEqual()`은 두 대상을 비교하는 Predicate를 만들 때 사용한다.  

```java
Predicate<String> p = Predicate.isEqual(str1);

//boolean result = p.test(str2);  // str1과 str2가 같은지 비교하여 결과를 반환
boolean result = Predicate.isEqual(str1).test(str2);
```

먼저, `isEqual()`의 매개변수로 비교대상을 하나 지정하고, 또 다른 비교대상은 test()의 매개변수로 지정한다.

# 메서드 참조

람다식이 하나의 메서드만 호출하는 경우에는 '메서드 참조(method reference)' 라는 방법으로 람다식을 간략히 할 수 있다.

예를 들어 문자열을 정수로 변환하는 람다식은 다음과 같다.

`Function<String, Integer> f = (String s) -> Integer.parseInt(s);`

이것을 아래와 같이 간략하게 할 수 있다.

`Function<String, Integer> f = Integer::parseInt;`

한 가지 예를 더 들면

`BiFunction<String, String, Boolean> f = (s1, s2) -> s1.equals(s2);`

참조변수 f의 타입을 보면 람다식이 두 개의 String 타입의 매개변수를 받는다는 것을 알 수 있다. 그러므로 람다식의 매개변수들은 없어도 된다. 이를 통해 매개변수들을 제거해서 메서드 참조로 변경하면 다음과 같다.

`BitFunction<String, String, Boolean> f = String::equals;`

s1과 s2를 생력해버리고 나면 equals만 남는데, 두 개의 String을 받아서 Boolean을 반환하는 equals라는 이름의 메서드는 다른 클래스에도 존재할 수 있기 때문에 equals앞에 클래스 이름은 반드시 필요하다.

또한 이미 생성된 객체의 메서드를 람다식에서 사용한 경우에는 클래스 이름 대신 그 객체의 참조변수를 적어줘야 한다.

```java
MyClass obj = new MyClass();
Function<String, Boolean> f = (x) -> obj.equals(x);
Function<String, Boolean> f2 = obj::equals;
```

이것들을 정리하면 다음과 같다.

- Static 메서드 참조

    `(x) -> ClassName.method(x)` → `ClassName::method`

- 인스턴스메서드 참조

    `(obj, x) -> obj.method(x)` → `ClassName::method`

- 특정 객체 인스턴스메서드 참조

    `(x) -> obj.method(x)` → `obj::method`

## 생성자의 메서드 참조

생성자를 호출하는 람다식도 메서드 참조로 변환할 수 있다.

```java
Supplier<MyClass> s = () -> new MyClass();
Supplier<MyClass> s = MyClass::new;
```

매개변수가 있는 생성자라면, 매개변수의 개수에 따라 알맞은 함수형 인터페이스를 사용하면 된다.

```java
Function<Integer, MyClass> f = (i) -> MyClass(i);
Function<Integer, MyClass> f2 = MyClass:new;

BiFunction<Integer, String, MyClass> bf = (i, s) -> new MyClass(i, s);
BiFunction<Integer, String, MyClass> bf2 = MyClass::new;
```

그리고 배열을 생성할 때는 아래와 같이 하면 된다.

```java
Function<Integer, int[]> f = x -> new int[x];
Function<Integer, int[]> f2 = int[]::new;
```

# Reference
[자바의 정석](http://www.yes24.com/Product/Goods/24259565)
