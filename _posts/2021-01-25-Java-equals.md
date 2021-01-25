---
title:  "Java equals()와 == 연산자"
categories:
  - Java
tags:
  - java
header:
  teaser: /assets/images/java/java.jpg
  image: /assets/images/java/java.jpg
---  
Java에서의 논리적 동치성 비교

# == 연산자

우리는 두 개의 값을 비교할 때 `==`연산자를 사용한다. 

### Equals Test 1

```java
public static void main(String[] args) {
	int a = 3;
	int b = 4;
	int c = 3;
	
	System.out.println(a == b);
	System.out.println(b == c);
	System.out.println(c == a);
}

```

### Equals Test Result 1

```java
false
false
true
```

하지만 `==` 연산자는 원시타입에서 값을 비교하는 용도로는 사용 가능하지만, 참조 타입의 객체의 동치성을 확인할 수 없다. 그저 메모리에서 같은 위치에 존재하는지만 판단해줄 뿐이다.  

### Student

```java
class Student {
	String name;
	int age;

	public Student(String name, int age){
		this.name = name;
		this.age = age;
	}

}
```

### Equals Test 2

```java
public static void main(String[] args) {
	Student s1 = new Student("jsh", 26);
	Student s2 = new Student("jsh", 26);

	System.out.println(s1 == s2);
}
```

### Equals Test Result 2

```java
false
```

우리가 보기에 학생 객체 s1과 s2는 같은 이름과 같은 나이를 가졌으므로 같은 객체라고 판단할 수 있다. 그러나 `==`연산자는 이러한 것들은 판단하지 않고 오직 해당 객체가 저장된 위치만을 비교해 판단할뿐이다. s1과 s2 각각의 객체는 `new` 연산자를 통해 새로운 인스턴스를 할당했으므로 메모리의 다른 위치에 저장되며 그저 **같은** 값을 가진 **다른**객체일 뿐이다.  

그렇다면 우리는 참조 타입의 객체에서 논리적 동치성을 확인하기 위해서는 어떻게 해야하는가?  

# equals(Object)

모든 클래스의 조상 클래스인 Object에는 두 객체가 같은지 확인할 수 있는 메서드인 `equals(Object)`가 정의되어 있다.

### Object equals(Object)

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

그런데 Object도 단순하게 `==` 연산자를 통해 매개변수로 받은 Object 객체와 해당 객체를 비교하고있다.  이 메서드로는 우리가 선언한 Student 클래스의 논리적 동치성을 확인할 수 없다. 

그래서 개발자들은 객체의 논리적인 동치성을 확인하기 위해 Obejct 클래스 내부의 `equals(Object)` 메서드를 재정의한다.

### String equals(Object)

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

먼저 쓸모없는 계산을 피하기 위해 만약 해당 객체와 매개변수로 넘어온 객체가 같은 메모리를 참조하고 있으면 같은 값을 가지고 있다고 판단한다.  

그게 아니라면 매개변수 객체가 String 인스턴스가 맞는지 확인한 후 String 클래스 내부에 존재하는 value char array의 값을 하나씩 비교한다. 비교한 값이 모두 맞으면 두 객체가 논리적으로 같다고 판단하고 `true`를 반환한다.

> 그런데  
> `String a = "aaa";`  
> `String b = "aaa";`  
> `System.out.println(a == b);`  
> 이렇게 하면 `true`가 나오는데 String도 `==`연산자로 논리적 동치성을 검증할 수 있는거 아닌가요?

아니다. 위의 방식대로 비교한 것은 `"aaa"`의 메모리 주소값을 비교한 것이다. 

만약 우리가 문자열 값을 불러온다면 JVM은 힙 메모리 내부에 생성된 문자열 값들이 들어있는 string pool을 확인하고 이미 존재하는 값이라면 string pool에서 해당 문자열의 주소값을 가져오고 없다면 새로 생성해서 할당해준다.  

![string1]({{ site.baseurl }}/assets/images/java/equals/string1.png) 

그러므로 `String a`와 `String b`는 같은 주소값에 있는 `"aaa"`를 참조하고있는 것이므로 `==`연산자로 비교했을 때 `true`가 반환되는 것이다.

만약 아래와 같이 같은 값을 가진 문자열의 인스턴스를 새롭게 생성해주면 `==`연산자가 `false`를 반환할 것이다.

```java
String a = "aaa";
String b = new String("aaa");

System.out.println(a == b);
```

그 이유는 `String a`와 `String b`가 가리키고 있는 메모리 위치가 다르기 때문이다.

![string2]({{ site.baseurl }}/assets/images/java/equals/string2.png) 

그래서 String 객체를 비교할 때 equals(Object) 메서드를 사용하는 것이다.

# Overriding

그렇다면 우리가 정의한 Student 클래스는 `equals(Object)` 메서드를 어떻게 재정의하는게 좋을까?

이때 우리는 아래와 같은 일반 규악을 따라야 한다.

### **재정의 일반 규약**

- 반사성
    - `null`이 아닌 모든 참조 값 x에 대해, `x.equals(x)`는 `true`다.
- 대칭성
    - `null`이 아닌 모든 참조 값 x, y에 대해, `x.equals(y)`가 `true`면 `y.equals(x)`도 true다.
- 추이성
    - `null`이 아닌 모든 참조 값 x, y, z에 대해, `x.equals(y)`가 `true`이고 `y.equals(z)`가 true`면` x.equals(z)`도` true`다.
- 일관성
    - `null`이 아닌 모든 참조 값 x, y에 대해, `x.equals(y)`를 반복해서 호출하면 항상 `true`를 반환하거나 항상 `false`를 반환한다.
- `null` 아님
    - `null`이 아닌 모든 참조 값 x에 대해, `x.equlas(null)`은 false다.

꼭 필요한 경우가 아니면 재정의 하지 않아야 하며, 필요한 경우에는 위의 규약을 확실히 지켜가면서 재정의 해야 한다.

위 규약을 지키면서 `equals(`를 재정의 하면 아래와 같은 Student 클래스가 완성된다.

### Student

```java
class Student {
	String name;
	int age;

	public Student(String name, int age){
		this.name = name;
		this.age = age;
	}

	@Override
	public boolean equals(Object o) {
	    if (this == o) return true;
	    if (o == null || getClass() != o.getClass()) return false;
	    Student student = (Student) o;
	    return age == student.age && name.equals(student.name);
	}

}
```

# Reference

[System.identityHashCode() Method in Java With Examples - GeeksforGeeks](https://www.geeksforgeeks.org/system-identityhashcode-method-in-java-with-examples/)

[Equality in Java: Operators, Methods, and What to Use When](https://stackify.com/equality-in-java-operators-methods-and-what-to-use-when/)

[Difference between == and equals() method in Java - String Object](https://www.java67.com/2012/11/difference-between-operator-and-equals-method-in.html)

[How "==" works on primitive types](https://stackoverflow.com/questions/38956043/how-works-on-primitive-types)

[How Do I Compare Strings in Java? - DZone Java](https://dzone.com/articles/how-do-i-compare-strings-in-java)
