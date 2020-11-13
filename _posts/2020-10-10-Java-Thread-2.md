---
title:  "Java Thread -2-"
categories:
  - Java
tags:
  - java
header:
  teaser: /assets/images/java/java.jpg
  image: /assets/images/java/java.jpg
---  

# 쓰레드의 동기화

싱글쓰레드 프로세스의 경우 프로세스 내에서 단 하나의 쓰레드만 작업하기 때문에 프로세스의 자원을 가지고 작업하는데 별 문제가 없지만, 멀티쓰레드 프로세스의 경우 여러 쓰레드가 같은 프로세스 내의 자원을 공유해서 작업하기 때문에 서로의 작업에 영향을 주게 된다.

만일 쓰레드A가 작업하던 도중에 다른 쓰레드B에게 제어권이 넘어갔을 때, 쓰레드 A가 작업하던 공유데이터를 쓰레드 B가 임의로 변경하였다면, 다시 쓰레드A가 제어권을 받아서 나머지 작업을 마쳤을 때 원래 의도했던 것과는 다른 결과를 얻을 수 있다.

이러한 일이 발생하는 것을 방지하기 위해서 한 쓰레드가 특정 작업을 마치기 전에 다른 쓰레드에 의해 방해받지 않도록 하는 것이 필요하다. 이것을 '임계 영역(critical section)'과 '잠금(lock)'이다.

공유 데이터를 사용하는 코드 영역을 임계 영역으로 지정해놓고, 공유 데이터가 가지고 있는 lock을 획득한 단 하나의 쓰레드만 이 영역 내의 코드를 수행할 수 있게 한다. 그리고 해당 쓰레드가 임계 영역 내의 모든 코드를 수행하고 벗어나서 lock을 반납해야만 다른 쓰레드가 반납된 lock을 획득하여 임계 영역의 코드를 수행할 수 있다.

이처럼 **한 쓰레드가 진행중인 작업을 다른 쓰레드가 간섭하지 못하도록 막는 것을 '쓰레드의 동기화(Synchronizaition)**'라고 한다.

## synchronized를 이용한 동기화

먼저 가장 간단한 synchronized 키워드를 이용한 동기화 방식이 있다.

```java
// 1. 메서드 전체를 임계 영역으로 지정
public synchronized void calcSum(){
	/* 내부 처리 코드 */
}

// 2. 특정한 영역을 임계 영역으로 지정
// 이때 참조 변수는 락을 걸고자 하는 객체
synchronized(객체의 참조변수 ex) this) {
	/* 내부 처리 코드 */
}
```

### 1. 메서드 앞에 `synchronized`

`synchronized`를 붙이면 메서드 전체가 임계 영역으로 설정된다.

쓰레드는 `synchronized`메서드가 호출된 시점부터 해당 메서드가 포함된 객체의 lock을 얻어 작업을 수행하다가 메서드가 종료되면 lock을 반환한다.

### 2. 블럭으로 감싸기 `{}`

메서드 내의 코드 일부를 블럭 `{}`으로 감싸고 블럭 앞에 `synchronized(참조변수)`를 붙이는 것인데, 이때 참조변수는 락을 걸고자 하는 객체를 참조하는 것이어야한다.

 이 블럭의 영역 안으로 들어가면서부터 쓰레드는 지정된 객체의 lock을 얻게 되고, 이 블럭을 벗어나면 lock을 반납한다.

## wait()과 notify()

특정 쓰레드가 객체의 lock을 가진 상태로 오랜 시간을 보내지 않도록 하는 것도 중요하다.

이때 사용하는 것이 `wait()`과 `notify()`이다.

동기화된 임계영역의 코드를 수행하다가 작업을 더 이상 진행할 상황이 아니면, 일단 `wait()`을 호출하여 쓰레드가 lock을 반납하고 기다리게 한다. 그러면 다른 쓰레드가 lock을 얻어 해당 객체에 대한 작업을 수행할 수 있게 된다. 나중에 작업할 수 있는 상황이 되면 `notify()`를 호출해서, 작업을 중단했던 쓰레드가 다시 lock을 얻어 작업을 진행할 수 있게 된다.

`wait()`에 의해 lock을 반납했다가, 다시 lock을 얻어 임계 영역에 들어오는 것을 재진입(reentrance)이라고 한다.

이때 오래 기다린 쓰레드가 lock을 얻는다는 보장은 없으며 `wait()`이 호출되어 대기중이던 쓰레드는 `notify()`가 호출될 때 waiting poll에 있던 쓰레드 중 임의의 쓰레드만 통지를 받는다.

- `void wait()`
- `void wait(long timeout)`
- `void wait(long timeout, int nanos)`
- `void notify()`
- `void notifyAll()`

해당 함수들은 Object에 정의되어 있으며 동기화 블럭 내에서만 사용할 수 있다.

`wait()`은 `notify()`또는 `notifyAll()`이 호출될 때까지 기다리지만, 매개변수가 있는 `wait()`은 지정된 시간동안만 대기한다.

## Lock과 Condition을 이용한 동기화

`synchronized`외에 동기화할 수 있는 방법은 java.util.concurrent.locks 패키지가 제공하는 lock 클래스를 이용하는 방법이 있다.  

`synchronized`블럭으로 동기화를 하면 자동적으로 lock이 잠기고 풀리기 때문에 편리하다. 심지어 블럭 내에서 예외가 발생해도 lock은 자동으로 풀린다. 그러나 같은 메서드 내에서만 lock을 걸 수 있다는 제약이 불편하기도 하다.

이럴 때 lock 클래스를 사용하며 종류는 아래 3가지가 있다.

- ReentrantLock
    - 재진입이 가능한 lock
    - 가장 일반적인 배타 lock
- ReentrantReadWriteLock
    - 읽기에는 공유적이고, 쓰기에는 베타적인 lock
- StampedLock
    - ReentrantReadWriteLock에 낙관적인 lock 기능을 추가

### ReentrantLock

가장 일반적인 lock이다.

Reentrant(재진입할 수 있는) 이라는 단어가 앞에 붙은 이유는 `wait()` & `notify()`에서 배운 것 처럼, 특정 조건에서 lock을 풀고 나중에 다시 lock을 얻고 임계영역으로 들어와서 이후의 작업을 수행할 수 있기 때문이다.

### ReentrantReadWriteLock

이름에서 알 수 있듯이, 읽기를 위한 lock과 쓰기를 위한 lock을 제공한다.

`ReentrantLock`은 베타적인 lock이라서 무조건 lock이 있어야만 임계 영역의 코드를 수행할 수 있지만, `ReentrantReadWriteLock`은 읽기 lock이 걸려있으면, 다른 쓰레드가 읽기 lock을 중복해서 걸고 읽기를 수행할 수 있다. 

읽기는 내용을 변경하지 않으므로 동시에 여러 쓰레드가 읽어도 문제가 되지 않는다. 그러나 읽기 lock이 걸린 상태에서 쓰기 lock을 거는 것은 허용되지 않는다. 반대의 경우도 마찬가지이다.

### StampedLock

lock을 걸거나 해지할 때 스탬프(long 타입의 정수값)를 사용하며, 읽기와 쓰기를 위한 lock외에 낙관적 읽기 lock(optimistic reading lock)이 추가된 것이다.

읽기 lock이 걸려있으면, 쓰기 lock을 얻기 위해서는 읽기 lock이 풀릴 때까지 기다려야 하는데 비해 낙관적 읽기 lock은 쓰기 lock에 의해 바로 풀린다. 그래서 낙관적 읽기에 실패하면, 읽기 lock을 얻어서 다시 읽어와야 한다.

**무조건 읽기 lock을 걸지 않고, 쓰기와 읽기가 충돌할 때만 쓰기가 끝난 후에 읽기 lock을 거는 것이다.**

```java
int getBalnace() {
	long stamp = lock.tryOptimisticRead(); // 낙관적 읽기 lock을 건다.
	int curBalance = this.balance; // 공유 데이터인 balance를 읽어온다.

	if(lock.validate(stamp)) { // 쓰기 lock에 의해 낙관적 읽기 lock이 풀렸는지 확인
		stamp = lock.readLock(); // lock이 풀렸으면, 읽기 lock을 얻으려고 기다린다.
		try {
			curBalance = this.balance; // 공유 데이터를 다시 읽어온다.
		} finally {
			lock.unlockRead(stamp); // 읽기 lock을 푼다.
		}
	}
	
	return curBalance; // 낙관적 읽기 lock이 풀리지 않았으면 곧바로 읽어온 값을 반환
}
```

## ReentrantLock

ReentrantLock은 다음과 같은 생성자를 가지고 있다.

- `ReentrantLock()`
- `ReentrantLock(boolean fair)`

생성자의 매개변수를 true로 주면, lock이 풀렸을 때 가장 오래 기다린 쓰레드가 lock을 획득할 수 있게, 공정(fair)하게 처리한다. 그러나 공정하게 처리하려면 어떤 쓰레드가 가장 오래 기다렸는지 확인하는 과정을 거칠 수밖에 없으므로 성능은 떨어진다.

대부분의 경우 굳이 공정하게 처리하지 않아도 문제가 되지 않으므로 공정함보다 성능을 선택한다.

자동적으로 lock의 잠금과 해제가 관리되는 `synchronized`블럭과 달리, `ReentrantLock`과 같은 lock클래스들은 수동으로 lock을 잠그고 해제해야 한다.

- `void lock()`
    - lock을 잠근다.
- `void unlock()`
    - lock을 해지한다.
- `boolean isLocked()`
    - lock이 잠겼는지 확인한다.

`synchronized(lock){ ... }` → `lock.lock(); /* 임계 영역 */ lock.unlock();`

임계 영역 내에서 예외가 발생하거나 return문으로 빠져나가게 되면 lock이 풀리지 않을 수 있으므로 `unlock()`은 try-finally문으로 감싸는 것이 일반적이다.

```java
lock.lock(); // ReentrantLock lock = new ReentrantLock();
try {
	// 임계영역
} finally {
	lock.unlock();
}
```

# Reference
[자바의 정석](http://www.yes24.com/Product/Goods/24259565)
