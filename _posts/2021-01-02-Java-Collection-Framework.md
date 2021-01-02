---
title:  "Java Collection Framework"
categories:
  - Java
tags:
  - java
header:
  teaser: /assets/images/java/java.jpg
  image: /assets/images/java/java.jpg
---  


# Collection Framework

# 컬렉션 프레임웍의 핵심 인터페이스

컬렉션 프레임웍에서는 컬렉션데이터 그룹을 크게 3가지 타입이 존재한다고 인식하고 각 컬렉션을 다루는데 필요한 기능을 가진 3개의 인터페이스를 정의하였다. 그리고 인터페이스를 List와 Set의 공통된 부분을 다시 뽑아서 새로운 인터페이스인 Collection을 추가로 정의하였다.

![collection]({{ site.baseurl }}/assets/images/java/collection_framework/collection.png) 

인터페이스 list와 Set을 구현한 컬렉션 클래스들은 서로 많은 공통 부분이 있어서, 공통된 부분을 다시 뽑아 Collection 인터페이스를 정의할 수 있었지만 Map 인터페이스는 이들과는 전혀 다른 형태로 컬렉션을 다루기 때문에 같은 상속계층도에 포함되지 못했다.

[Collection API](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)

## List 인터페이스

중복을 허용하면서 저장순서가 유지되는 컬렉션을 구현하는데 사용된다.

[List API](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)

### ArrayList VS LinkedList

LinkedList와 ArrayList는 모두 List인터페이스를 구현한 것이기 때문에 내부구현방법만 다를 뿐 제공하는 메서드의 종류와 기능은 거의 같다.

대신 상황에 따라서 성능의 차이를 가진다.

1. 순차적으로 추가/삭제 하는 경우에는 ArrayList가 LinkedList보다 빠르다.

    순차적으로 삭제한다는 것은 마지막 데이터부터 역순으로 삭제해나간다는 것을 의미하며, ArrayList는 마지막 데이터부터 삭제할 경우 각 요소들의 재배치가 필요하지 않기 때문에 상당히 빠르다.

2. 중간 데이터를 추가/삭제하는 경우에는 LinkedList가 ArrayList보다 빠르다.

    중간 요소를 추가 또는 삭제하는 경우, LinkedList는 각 요소간의 연결반 변경해주면 되기 때문에 처리속도가 상당이 빠르다. 반면에 ArrayList는 각 요소들을 재배치하여 추가할 공간을 확보하거나 빈 공간을 채워야하기 때문에 처리속도가 늦다.

# Stack과 Queue

순차적으로 데이터를 추가하고 삭제하는 스택에는 ArrayList와 같은 배열기반의 컬렉션 클래스가 적합하지만, 큐는 데이터를 꺼낼 때 항상 첫 번째 저장된 데이터를 삭제하므로, ArrayList와 같은 배열기반의 컬렉션 클래스를 사용한다면 데이터를 꺼낼 때마다 빈공간을 채우기 위해 데이터의 복사가 발생하므로 비효율적이다. 그래서 큐는 ArrayList보다 데이터의 추가, 삭제가 간편한 LinkedList로 구현하는 것이 더 적합하다.

```python
Stack st = new Stack();
Queue q = new LinkedList();
```

자바에서는 스택을 Stack 클래스로 구현하여 제공하고 있지만 큐는 Queue인터페이스로만 정의해 놓았을 뿐 별도의 클래스를 제공하고 있지 않다. 대신 Queue인터페이스를 구현한 클래스들이 있어서 이 들 중의 하나를 선택해서 사용하면 된다.

그 외에도 Queue인터페이스를 구현한 몇가지 클래스들도 존재한다

- PriorityQueue

    저장한 순서에 관계없이 우선순위가 높은 것부터 꺼내게 된다.

- Deque

    양쪽 끝에 추가/삭제가 가능하다.

[Stack API](https://docs.oracle.com/javase/7/docs/api/java/util/Stack.html) 

[Queue API](https://docs.oracle.com/javase/7/docs/api/java/util/Queue.html)

# Iterator, ListIterator, Enumeration

컬렉션에 저장된 요소를 접근하는데 사용되는 인터페이스이다. Enumeration은 Iterator의 구버젼이며, ListIterator는 Iterator의 기능을 향상 시킨 것이다.

## Iterator

컬렉션 프레임웍에서는 컬렉션에 저장된 요소들을 읽어오는 방법을 표준화하였다. 컬렉션에 저장된 각 요소에 접근하는 기능을 가진 Iterator인터페이스를 정의하고, Collection 인터페이스에는 Iterator를 반환하는 iterator()를 정의하고있다.

컬렉션 클래스에 대해 iterator()를 호출하여 Iterator를 얻은 다음 반복문, 주로 while 문을 사용해서 컬렉션 클래스의 요소들을 읽어올 수 있다.

```java
List list = new ArrayList();
Iterator it = list.iterator();

while(it.hasNext()) {
	System.out.println(it.next());
}
```

[Iterator API](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html) 

Map 인터페이스를 구현한 컬렉션 클래스는 key와 value를 쌍으로 저장하기 때문에 `iterator()`를 직접 호출할 수 없고, 그 대신 `keySet()` 이나 `entrySet()`과 같은 메서드를 통해서 Set의 형태로 얻어 온 후에 다시 `iterator()`를 호출해야 Iterator를 얻을 수 있다.

## ListIterator

ListIterator는 Iterator를 상속받아서 기능을 추가한 것으로, 컬렉션의 요소에 접근할 때 Iterator는 단방향으로만 이동할 수 있는 데 반해 ListIterator는 양방향으로의 이동이 가능하다. 단, ArrayList나 LinkedList와 같이 List인터페이스를 구현한 컬렉션에서만 사용할 수 있다.

[ListIterator Api](https://docs.oracle.com/javase/7/docs/api/java/util/ListIterator.html)

# Arrays

Arrays 클래스에는 배열을 다루는데 유용한 메서드가 정의되어있다.

### 배열의 복사 - copyOf(), copyOfRange()

`copyOf()`는 배열 전체를, `copyOfRage()`는 배열의 일부를 복사해서 새로운 배열을 만들어 반환한다.

### 배열 채우기 - fill(), setAll()

`fill()`은 배열의 모든 요소에 지정된 값으로 채운다. `setAll()`은 배열을 채우는데 사용할 함수형 인터페이스를 매개변수로 받는다.

### 배열의 정렬과 검색 - sort(), binarySearch()

`sort()`는 배열을 정렬할 때, 그리고 배열에 저장된 요소를 검색할 때는 `binarySearch()`를 사용한다. `binarySearch()`는 배열에서 지정된 값이 저장된 index를 반환하며 반드시 배열이 정렬된 상태어야 한다.

### 문자열의 비교와 출력 - equals(), toString()

`equals()`와 `toString()`은 1차원 배열에서만 동작하며 다차원 배열에서 사용하려면 `deepToEquals()`와 `deepToString()` 를 사용해야 한다.

### 배열을 List로 변환 - asList(Object... a)

매개변수의 타입이 가변인수라서 배열 생성없이 저장할 요소들만 나열하는 것도 가능하다.

단 `asList()`가 반환한 List의 크기를 변경할 수 없다.

```java
List list = Arrays.asList(1, 2, 3, 4, 5);
list.add(6); // UnsupportedOperationException 예외 발생
```

만약 크기를 변경할 수 있는 List가 필요하다면 다음과 같이 하면 된다.

```java
List list = new ArrayList(Arrays.asList(1, 2, 3, 4, 5));
```

# HashSet

Set 인터페이스를 구현한 가장 대표적인 컬렉션이며, Set 엔터페이스의 특지앧로 HashSet은 중복된 요소를 저장하지 않는다.

HashSet에 새로운 요소를 추가할 때는 add메서드나 addAll 메서드를 사용하는데, 만일 HashSet에 이미 저장되어 있는 요소와 중복된 요소를 추가하고자 한다면 이 메서드들은 false를 반환함으로써 중복된 요소이기 때문에 추가에 실패했다는 것을 알린다.

ArrayList와 같이 List인터페이스를 구현한 컬렉션과 달리 HashSet은 저장순서를 유지하지 않으므로 저장순서를 유지하고자 한다면 LinkedHashSet을 사용해야 한다.

HashSet의 add 메서드는 새로운 요소를 추가하기 전에 기존에 저장된 요소와 같은 것인지 판별하기 위해 추가하려는 요소의 `equals()`와 `hashCode()`를 호출하기 때문에 이 두가지를 목적에 맞게 오버라이딩 해야한다.

이때 오버라이딩 하는 `hashCode()` 메서드는 다음과 같은 조건을 만족해야 한다.

1. 실행 중인 어플리케이션 내의 동일한 객체에 대해서 여러 번 `hashCode()`를 호출해도 동일한 int 값을 반환해야한다.
2. equals 메서드를 이용한 비교에 의해서 true를 얻은 두 객체에 대해 각각 `hashCode()`를 호출해서 얻은 결과는 반드시 같아야 한다.
3. equals메서드를 호출했을 때 false를 반환하는 두 객체는 `hashCode()` 호출에 대해 같은 int 값을 반환하는 경우가 있어도 괜찮지만, 해싱을 사용하는 컬렉션의 성능을 향상시키기 위해서는 다른 int값을 반환하는 것이 좋다.

# TreeSet

TreeSet은 이진 검색 트리라는 자료구조의 형태로 데이터를 저장하는 컬렉션 클래스이다.

이진 검색 트리는 정렬, 검색, 범위검색에 높은 성능을 보이는 자료구조이며 TreeSet은 이진 검색 트리의 성능을 향상시킨 Red - Black tree로 구현되어 있다.

또한 Set인터페이스를 구현했으므로 중복된 데이터의 저장을 허용하지 않으며 정렬된 위치에 저장하므로 저장순서를 유지하지도 않는다.

저장되는 객체는 Comparable을 구현하던가 아니면, Comparator를 제공해서 두 객체를 비교할 방법을 알려줘야 한다. 그렇지 않으면, TreeSet에 객체를 저장할 때 예외가 발생한다.

TreeSet은 정렬된 상태를 유지하기 때문에 단일 값 검색과 범위검색이 매우 빠르며 저장된 값에 대한 효율도 매우 뛰어나다.

데이터를 순차적으로 저장하는 것이 아니라 저장위치를 찾아서 저장해야하고, 삭제하는 경우 트리의 일부를 재구성해야하므로 링크드 리스트보다 데이터의 추가/삭제 시간은 더 걸린다. 대신 배열이나 링크드 리스트에 비해 검색과 정렬기능이 뛰어나다.

# HashMap & Hashtable

Hashtable과 HashMap의 관계는 Vector와 ArrayList와 관계가 같아서 Hashtable보다 HashMap을 사용할 것을 권한다.

HashMap은 Map을 구현했으므로 앞에서 키와 값을 묶어서 하나의 데이터로 저장한다는 특징을 갖는다. 그리고 해싱을 사용하기 때문에 많은 양의 데이터를 검색하는데 있어서 뛰어난 성능을 보인다.

```java
public class HashMap extends AbstractMap implements Map, Cloneable, Serializable {
	transient Entry[] table;
				...
	static class Entry implements Map.Entry{
		final Object key;
		Object value;
				...		
	}

}
```

HashMap은 Entry라는 내부 클래스를 정의하고, 다시 Entry타입의 배열을 선언하고 있다. 키와 값은 별개의 값이 아니라 서로 관련된 값이기 때문에 각각의 배열로 선언하기 보다는 하나의 크랠스로 정의해서 하나의 배열로 다루는 것이 데이터의 무결성적인 측면에서 바람직하기 때문이다.

[HashMap API](https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html)

## 해싱과 해시함수

해싱이란 해시함수를 이용해서 데이터를 해시테이블에 저장하고 검색하는 기법을 말한다. 해시함수는 데이터가 저장되어 있는 곳을 알려 주기 때문에 다량의 데이터 중에서도 원하는 데이터를 빠르게 찾을 수 있다.

해싱을 구현한 컬렉션 클래스로는 HashSet, HashMap, Hashtable 등이 있다.

Hashtable은 컬렉션 프레임웍이 도입되면서 HashMap으로 대체되었으나 이전 소스와의 호완성 문제로 남겨두고있다.

해싱에서 사용하는 자료구조는 배열과 링크드 리스트의 조합으로 되어 있다.

![hash]({{ site.baseurl }}/assets/images/java/collection_framework/hash.png) 
저장할 데이터의 키를 해시함수에 넣으면 배열의 한 요소를 얻게 되고, 다시 그 곳에 연결되어있는 링크드 리스트에 저장하게 된다. 

검색에 불리한 자료구조인 링크드 리스트와는 다르게 배열은 배열의 크기가 커져도, 원하는 요소가 몇 번재에 있는 지만 알면 빠르게 원하는 값을 찾을 수 있다.

그러나 해싱한 값인 키에 경우 중복이 일어날 수 있으며 중복이 일어나게 되면 같은 위치에 링크드 리스트로 값이 저장되게 된다. 이럴 경우 자료 검색에 대한 속도가 느려지므로 키를 해싱해주는 `hashCode()`메서드로 해시코드를 만들 때 다른 객체에 대해 다른 값이 나오도록 재정의해야 한다.

## TreeMap

TreeMap은 이진검색트리의 형태로 키와 값의 쌍으로 이루어진 데이터를 저장한다. 그래서 검색과 정렬에 적합한 컬렉션 클래스이다. HashMap과 같이 본다면 검색에 관한한 대부분의 경우에서 HashMap이 TreeMap보다 뛰어나고, 범위검색이나 정렬이 필요한 경우에는 TreeMap이 성능이 더 좋다

## Properties

Properties는 Hashtable을 상속받아 구현한 것으로, Hashtable이 키와 값을 (Object, Object)로 저장하는데 비해 Properties는 (String, String)의 형태로 저장하는 단순화된 컬렉션 클래스이다. 

데이터를 파일로 읽고 쓰는 편리한 기능을 제공하며, 간단한 입출력은 Properties를 활용하면 쉽게 구현할 수 있다.

# Collections

Arrays가 배열로 관련된 메서드를 제공하는 것처럼, Collections는 컬렉션과 관련된 메서드를 제공한다.

## 컬렉션의 동기화

멀티 쓰레드 프로그래밍에서는 하나의 객체를 여러 쓰레드가 동시에 접근할 수 있기 때문에 데이터 일관성을 유지하기 위해서는 공유되는 객체에 동기화가 필요하다.

Vector와 hashtable과 같은 구버젼의 클래스들은 자체적으로 동기화 처리가 되어 있는데, 멀티쓰레드 프로그래밍이 아닌 경우에는 불필요한 기능이 되어 성능을 떨어뜨리는 요인이 된다.

그래서 새로 추가된 ArrayList와 HashMap과 같은 컬렉션은 동기화를 자체적으로 처리하지 않고 필요한 경우에만 Collections클래스의 동기화 메서드를 이용해서 동기화처리가 가능하도록 변경하였다. Collections 클래스에서 `synchronized`로 시작하는 메서드를 사용하면 된다.

### 예시

- `static List synchronizedList(List list)`
- `static Collection synchronizedCollection(Collection c)`

## 변경불가 컬렉션

컬렉션에 저장된 데이터를 보호하기 위해서 컬렉션을 변경할 수 없게, 즉 읽기전용으로 만들어야할 때 `unmodifiable`로 시작하는 메서드를 사용하면 된다.

### 예시

- `static List unmodifiableList(List list)`
- `static Collection unmodifiableCollection(Collection c)`

## 그 외

이 외에도 싱글톤으로 만들기는 `singleton`으로 시작하는 메서드를, 한 종류의 객체만 저장하는 컬렉션을 만들 때는 `checked`로 시작하는 메서드를 사용하면 된다.

### 예시

- `static List singletonList(Object o)`
- `static List checkedList(List list, Class type)`

# Reference

[Collection (Java Platform SE 8 )](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)

[List (Java Platform SE 8 )](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)

[Iterator (Java Platform SE 8 )](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)