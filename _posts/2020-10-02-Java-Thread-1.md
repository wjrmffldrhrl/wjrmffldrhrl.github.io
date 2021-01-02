---
title:  "Java Thread -1-"
categories:
  - Java
tags:
  - java
header:
  teaser: /assets/images/java/java.jpg
  image: /assets/images/java/java.jpg
---  

자바에서 쓰레드 동작에 대해 알아보자

# 프로세스와 쓰레드

## 프로세스

간단히 말해서 "실행 중인 프로그램"이다.

프로그램을 실행하면 OS로부터 실행에 필요한 자원을 할당받아 프로세스가 된다.

프로세스는 자원과 쓰레드로 이루어져있으며 프로세스의 자원을 이용해서 작업을 수행하는 것이 바로 쓰레드이다.

## 쓰레드

모든 프로세스는 최소한 하나 이상의 쓰레드가 존재하며, 둘 이상의 쓰레드를 가진 프로세스를 "멀티쓰레드 프로세스(multi-threaded process)"라고 한다.

## 멀티태스킹과 멀티쓰레딩

대부분의 OS는 멀티태스킹을 지원하기 때문에 여러 개의 프로세스가 동시에 실행될 수 있다.

이와 마찬가지로 멀티쓰레딩은 하나의 프로세스 내에서 여러 쓰레드가 동시에 작업을 수행하는 것이다.

그러나 프로세스의 성능이 단순히 쓰레드의 개수에 비례하는 것은 아니며, 하나의 쓰레드를 가진 프로세스 보다 두 개의 쓰레드를 가진 프로세스가 오히려 더 낮은 성능을 보일 수 있다.

## 멀티쓰레딩의 장단점

### 장점

- CPU의 사용률을 향상시킨다
- 자원을 보다 효율적으로 사용할 수 있다.
- 사용자에 대한 응답성이 향상된다.
- 작업이 분리되어 코드가 간결해진다.

만일 싱글쓰레드로 서버 프로그램을 작성한다면 사용자의 요청 마다 새로운 프로세스를 생성해야 하는데 프로세스를 생성하는 것은 쓰레드를 생성하는 것에 비해 더 많은 시간과 메모리 공간을 필요로 하기 때문에 많은 수의 사용자 요청을 서비스하기 어렵다.

### 단점

- 동기화(synchronization), 교착상태(deadlock)와 같은 문제를 고려해야 한다.

# 쓰레드의 구현과 실행

쓰레드를 구현하는 방법은 Thread 클래스를 상송받는 방법과 Runnable 인터페이스를 구현하는 방법, 모두 두 가지가 있다. 별 차이는 없지만 Thread 클래스를 상속받으면 다른 클래스를 상속받을 수 없기 때문에, Runnable 인터페이스를 구현하는 방법이 일반적이다.

Runnable 인터페이스를 구현하는 방법은 재사용성이 높고 코드의 일관성을 유지할 수 있기 때문에 보다 객체지향적인 방법이라 할 수 있다.

```java
// Thread 클래스 상속
class MyTHread extends Thread {
	public void run() { /* work */} // Thread class run method overriding
}
```

```java
// Runnable 인터페이스 구현
class myThread implements Runnable {
	public void run() { /* work */ } // implements Runnable interface 
}
```

Thread 클래스를 상속받은 경우와 Runnable 인터페이스를 구현한 경우의 인스턴스 생성 방법이 다르다.

```java
class ThreadEx1 extends Thread {
	public void run() {
		for(int i = 0 ; i < 5 ;  i++) {
			System.out.println(getName()); // 조상 클래스 Thread의 함수
		}
	}
}

class ThreadEx2 implements Runnable {
	public void run() {
		for(int i = 0 ; i < 5 ; i++) {

			// Thread.currentThread() 현재 실행중인 쓰레드 반환
			System.out.println(Thread.currentThread().getName());
		}
	}
}

class ThreadEx {
	public static void main(String args[]) {
		ThreadEx1 t1 = new ThreadEx1();
		
		Runnable r = new ThreadEx2();
		Thread t2 = new Thread(r);
		// Thread t2 = new Threae( new ThreadEx2()); 한줄로 축약	

		t1.start();
		t2.start();
	
	}
}
```

한 번 실행이 종료된 쓰레드는 다시 실행할 수 없다.

즉, 하나의 쓰레드에 대해 start()가 한 번만 호출될 수 있다는 뜻이다.

만일 쓰레드의 작업을 한 번 더 수행해야 한다면 새로운 쓰레드를 생성한 다음에 start()를 호출해야 한다.

# start()와 run()

main 메서드에서 run()을 호출하는 것은 생성된 쓰레드를 실행시키는 것이 아니라 단순히 클래스에 선언된 메서드를 호출하는 것일 뿐이다.

![stack1]({{ site.baseurl }}/assets/images/java/thread/stack1.png) 

반면에 start()는 새로운 쓰레드가 작업을 실행하는데 필요한 호출스택  (call stack)을 생성한 다음에 run()을 호출해서, 생성된 호출스택에 run()이 첫 번째로 올라가게 한다.

모든 쓰레드는 독립적인 작업을 수행하기 위해 자신만의 호출스택을 필요로 하기 때문에, 새로운 쓰레드를 생성하고 실행시킬 때마다 새로운 호출스택이 생성되고 쓰레드가 종료되면 작업에 사용된 호출스택은 소멸된다.

![stack2]({{ site.baseurl }}/assets/images/java/thread/stack2.png)

```java
class ThreadEx {
	public static void main(String args[]) throws Exception {
		ThreadEx_1 t1 = new ThreadEx_1();
		t.start();
	}
}

class ThreadEx_1 extends Thread {
	public void run(){
		throwException();
	}

	public void throwException() {
		try {
			throw new Exception();
		} catch(Esxception e) {
			e.printStackTrace();
		}
	}
}
```

```java
Java.lang.Exception
	at ThreadEx_1.throwException(ThreadEx.java:15)
	at ThreadEx_1.run(ThreadEx.java:10)
```

새로 생성한 쓰레드에서 예외를 발생시키고 `printStackTrace()`를 이용해서 예외가 발생한 당시의 호출 스택을 출력하는 예제이다.

호출스택의 첫 번째 메서드가 main메서드가 아니라 run 메서드인 것을 확인하고 보자

한 쓰레드가 예외가 발생해서 종료되어도 다른 쓰레드의 실행에는 영향을 미치지 않는다.

예외가 발생한 호출스택의 출력에서 main 쓰레드가 없는 이유는 main 쓰레드가 종료되었기 때문이다.

```java
class ThreadEx {
	public static void main(String args[]) throws Exception {
		ThreadEx_1 t1 = new ThreadEx_1();
		t.run();
	}
}

class ThreadEx_1 extends Thread {
	public void run(){
		throwException();
	}

	public void throwException() {
		try {
			throw new Exception();
		} catch(Esxception e) {
			e.printStackTrace();
		}
	}
}
```

```java
Java.lang.Exception
	at ThreadEx_1.throwException(ThreadEx.java:15)
	at ThreadEx_1.run(ThreadEx.java:10)
	at ThreadEx.main(ThreadEx.java:4)
```

ThreadEx_1클래스의 run()이 호출되었을 뿐이기 때문에 쓰레드는 새로 생성되지 않는다.

# 싱글쓰레드와 멀티쓰레드

하나의 쓰레드로 두 작업을 처리하는 경우는 한 작업을 마친 후에 다른 작업을 시작하지만, 두 개의 쓰레드로 작업 하는 경우에는 짧은 시간동안 2개의 쓰레드가 번갈아 가면서 작업을 수행해서 두 작업이 처리되는 것과 같이 느끼게 한다.

![schedule]({{ site.baseurl }}/assets/images/java/thread/schedule.png)

하나의 쓰레드로 두개의 작업을 수행한 시간과 두개의 쓰레드로 두 개의 작업을 수행한 시간은 거의 같다.

오히려 두 개의 쓰레드로 작업한 시간이 싱글쓰레드로 작업한 시간보다 더 걸리게 되는데 그 이유는 쓰레드간의 **작업전환**(Contexts Switching)에 시간이 걸리기 때문이다.

작업 전환을 할 때는 현재 진행 중인 작업의 상태 정보를 저장하고 읽어 오는 시간이 소요된다.

(프로세스 스위칭이 쓰레드 스위칭보다 시간이 많이 걸린다.)

그래서 싱글 코어에서 단순히 CPU만을 사용하는 계산작업이라면 멀티쓰레드보다 싱글쓰레드로 프로그래밍하는 것이 더 효율적이다.

싱글 코어인 경우에는 멀티쓰레드라도 하나의 코어가 번갈아가면서 작업을 수행하는 것이므로 두 작업이 절대 겹치지 않는다. 그러나, 멀티 코어에서는 멀티쓰레드로 두 작업을 수행하면, 동시에 두 쓰레드가 수행될 수 있어서 자원을 놓고 두 쓰레드가 경쟁하게 되는 상황이 일어난다.

두 쓰레드가 서로 다른 자원을 사용하는 작업의 경우에는 싱글쓰레드 프로세스보다 멀티쓰레드 프로세스가 더 효율적이다.

# 쓰레드의 우선순위

쓰레드는 우선순위(Priority)라는 속성을 가지고 있는데, 이 우선순위의 값에 따라 쓰레드가 얻는 실행시간이 달라진다.

## 쓰레드의 우선순위 지정하기

쓰레드의 우선순위와 관련된 메서드와 상수는 다음과 같다.

- `void setPriority(int newPriority)` → 쓰레드의 우선순위를 지정한 값으로 변경한다.
- `int getPriority()` → 쓰레드의 우선순위 반환
- `public static final int MAX_PRIORITY = 10` → 최대 우선순위
- `public static final int MIN_PRIORITY = 1` → 최소우선순위
- `public static final int NORM_PRIORITY = 5` → 보통 우선순위

 

쓰레드가 가질 수 있는 우선순위 범위는 1~10이며 숫자가 높을수록 우선순위가 높다.

쓰레드의 우선 순위는 쓰레드를 생성한 쓰레드로부터 상속받는다.

그러므로 main메서드를 수행하는 쓰레드는 우선순위가 5이므로 main메서드 내에서 생성하는 쓰레드의 우선순위는 자동적으로 5가 된다.

# 쓰레드 그룹

쓰레드 그룹은 서로 관련된 쓰레드를 그룹으로 다루기 위한 것으로, 폴더를 생성해서 관련된 파일들을 함께 넣어서 관리하는 것처럼 쓰레드 그룹을 생성해서 쓰레드를 그룹으로 묶어서 관리할 수 있다.

모든 쓰레드는 반드시 쓰레드 그룹에 포함되어 있어야 하기 때문에, 쓰레드 그룹을 지정하는 생성자를 사용하지 않은 쓰레드는 기본적으로 자신을 생성한 쓰레드와 같은 쓰레드 그룹에 속하게 된다.

```java
class ThreadGroupTest {
    public static void main(String[] args) throws Exception {
        ThreadGroup main = Thread.currentThread().getThreadGroup();
        ThreadGroup grp1 = new ThreadGroup("Group1");
        ThreadGroup grp2 = new ThreadGroup("Group2");

        ThreadGroup subGrp1 = new ThreadGroup(grp1, "SubGroup1");

        grp1.setMaxPriority(3);

        Runnable r = new Runnable(){

            @Override
            public void run() {
                // TODO Auto-generated method stub
                try {
                    Thread.sleep(1000);
                } catch(InterruptedException e) {}
            }

            
        };

				// Thread(ThreadGroup tg, Runnable r, String name)
        new Thread(grp1, r, "th1").start();
        new Thread(subGrp1, r, "th2").start();
        new Thread(grp2, r, "th3").start();

        System.out.println("List Of THreadGroup : " + main.getName()
                    + ", Active ThreadGroup : " + main.activeGroupCount()
                    + ", Active Thread : " + main.activeCount());
    
        main.list();
        
    
    }
}
```

```
List Of THreadGroup : main, Active ThreadGroup : 3, Active Thread : 4
java.lang.ThreadGroup[name=main,maxpri=10]
    Thread[main,5,main]
    java.lang.ThreadGroup[name=Group1,maxpri=3]
        Thread[th1,3,Group1]
        java.lang.ThreadGroup[name=SubGroup1,maxpri=3]
            Thread[th2,3,SubGroup1]
    java.lang.ThreadGroup[name=Group2,maxpri=10]
        Thread[th3,5,Group2]
```

# 데몬 쓰레드

데몬 쓰레드는 다른 일반 쓰레드의 작업을 돕는 보조적인 역할을 수행하는 쓰레드이다.

(ex → 가비지 컬렉터, 워드프로세서의 자동 저장, 화면 자동 갱신)

일반 쓰레드가 모두 종료되면 데몬 쓰레드는 강제적으로 자동종료 되는데, 그 이유는 데몬 쓰레드는 일반 쓰레드의 보조역할을 수행하므로 일반 쓰레드가 모두 종료되고 나면 데몬 쓰레드의 존재의 의미가 없기 때문이다.

데몬 쓰레드는 무한루프와 조건문을 이용해서 실행 후 대기하고 있다가 특정 조건이 만족되면 작업을 수행하고 다시 대기하도록 작성한다.

- `boolean isDaemon()` → 쓰레드가 데몬 쓰레드인지 확인한다.
- `void setDaemon(boolean on)` → 쓰레드를 데몬 쓰레드로 또는 사용자 쓰레드로 변경한다.

```java
public class DaemonThread implements Runnable{
    static boolean autoSave = false;

    public static void main(String[] args) {
        Thread t = new Thread(new DaemonThread());
        t.setDaemon(true);
        t.start();

        for(int i = 1 ; i <= 10 ; i++) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                //TODO: handle exception
                
            }
            System.out.println(i);

            if(i == 5) {
                autoSave = true;
            }
        }

        System.out.println("System out");
    }    
    
    @Override
    public void run() {
        // TODO Auto-generated method stub
        while(true) {
            try {
                Thread.sleep(3 * 1000);
            } catch (InterruptedException e) {
                //TODO: handle exception

            }

            if(autoSave) {
                autoSave();
            }
        }
    }    
    
    public void autoSave() {
        System.out.println("save!");
    }
}
```

만일 해당 쓰레드를 데몬 쓰레드로 설정하지 않았다면, 이 프로그램은 강제종료하지 않는 한 영원히 종료되지 않을 것이다.

# 쓰레드의 실행 제어

쓰레드의 스케줄링을 잘하기 위해서는 쓰레드의 상태와 관련 메서드를 잘 알아야 한다.

## 쓰레드 스케줄링 메서드

- `static void sleep(long millis)`, `static void sleep(long millis, int nanos)`
    - 지정된 시간동안 쓰레드를 일시정지시킨다.
    - 지정한 시간이 지나고 나면, 자동적으로 다시 실행대기상태가 된다.
- `void join()`, `void join(long milllis)`, `void join(long millis, int nanos)`
    - 지정된 시간동안 쓰레드가 실행되도록 한다.
    - 지정된 시간이 지나거나 작업이 종료되면 `join()`을 호출한 쓰레드로 다시 돌아와 실행을 계속한다.
- `void interrupt()`
    - `sleep()` 이나 `join()`에 의해 일시정지상태인 쓰레드를 깨워서 실행대기상태로 만든다.
    - 해당 쓰레드에서는 `interruptedException`이 발생 함으로써 일시정지상태를 벗어나게 된다.
- `void stop()`
    - 쓰레드를 즉시 종료시킨다.
- `void suspend()`
    - 쓰레드를 일시정지시킨다.
    - `resume()`을 호출하면 다시 실행대기상태가 된다.
- `void resume()`
    - `suspend()`에 의해 일시정지상태에 있는  쓰레드를 실행대기상태로 만든다.
- `static void yield()`
    - 실행 중에 자신에게 주어진 실행시간을 다른 쓰레드에게 양보하고 자신은 실행대기상태가 된다.

`resume()`, `suspend()`, `stop()`는 쓰레드를 교착상태로 만들기 쉽기 때문에 deprecated되었다.

## 쓰레드의 상태

- `NEW`
    - 쓰레드가 생성되고 아직 `start()`가 호출되지 않은 상태
- `RUNNABLE`
    - 실행 중 또는 실행 가능한 상태
- `BLOCKED`
    - 동기화블럭에 의해서 일시정지된 상태
    - lock이 풀릴 때까지 기다리는 상태
- `WAITING`, `TIMED_WAITING`
    - 쓰레드의 작업이 종료되지는 않았지만 실행가능하지 않은 일시정지상태.
    - `TIMED_WAITING`은 일시정지시간이 지정된 경우를 의미한다.
- `TERMINATED`
    - 쓰레드의 작업이 종료된 상태

![states]({{ site.baseurl }}/assets/images/java/thread/states.png)

## 쓰레드 실행제어 예시

### sleep(long mills) 일정시간동안 쓰레드 정지

`sleep()`에 의해 일시정지 상태가 된 쓰레드는 지정된 시간이 다 되거나 `interrupt()`가 호출되면, `InterruptedException`이 발생되어 실행대기상태가 된다. 

`sleep()` 메서드는 `th1.sleep(2000)`과 같이 호출해도 실제로 영향을 받는 것은 현재 실행중인 쓰레드이다. 그래서 `sleep()`은 static으로 선언되어 있으며 참조변수를 이용해서 호출하기 보다는 `Thread.sleep(2000)`과 같이 해야 한다.

### interrupt()와 interrupted() 쓰레드의 작업을 취소한다.

`interrupt()`는 쓰레드에게 작업을 멈추라고 요청한다. 단지 멈추라고 요청만 하는 것일 뿐 쓰레드를 강제로 종료시키지는 못한다.

`interrupt()`는 그저 쓰레드의 interrupted 상태(인스턴스 변수)를 바꾸는 것일 뿐이다.

그리고 `interrupted()`는 쓰레드에 대해 `interrupt()`가 호출되었는지 알려준다.

`interrupt()`가 호출되지 않았다면 false 를, 호출되었다면 true를 반환한다.

`boolean isInterrupted()`메서드를 통해 현재 쓰레드의 interrupted상태를 반환받을 수 있다.

쓰레드가 `sleep()`, `wait()`, `join()`에 의해 일시정지 상태(WAITING)에 있을 때, 해당 쓰레드에 대해 `interrupt()`를 호출하면 `sleep()`, `wait()`, `join()` 에서 Interrupted Exception이 발생하고 쓰레드는 실행대기 상태(RUNNABLE)로 바뀐다. 즉, 멈춰있던 쓰레드를 깨워서 실행가능한 상태로 만드는 것이다.

```java
import javax.swing.JOptionPane;

class ThreadEx {
	public static void main(String[] args) throws Exception {
		ThreadEx_1 th1 = new ThreadEx_1();
		th1.start();

		String input = JOptionPane.showInputDialog("input data");
		th1.interrupt();
		System.out.println(" isInterrupted ");

	}
}

class ThreadEx_1 extends Thread {
	public void run() {
		int i = 10;
		
		while(i != 0 && !isInterrupted()) {
			System.out.println(i--);
			try {
				Thread.sleep(1000);
			} catch(InterruptedException e) {}
		}

		System.out.println("count end");

	}
} 
```

위와 같은 코드에서 th1에 interrupt를 발생시켜도 카운트 다운은 멈추지 않는다.

왜냐하면 `Thread.sleep(1000)`에서 InterruptedException이 발생되어 쓰레드의 interrupted 상태는 false로 자동 초기화되기 때문이다.

아래와 같이 코드를 수정하면 정상적으로 동작 할 것이다.

```java
try {
				Thread.sleep(1000);
	} catch(InterruptedException e) {
		interrupt();
	}
```

### suspend(), resume(), stop()

`suspend()`는 `sleep()`처럼 쓰레드를 멈추게 한다.

`suspend()`에 의해 정지된 쓰레드는 `resume()`을 호출해야 다시 실행대기 상태가 되며, `stop()`은 호출되는 즉시 쓰레드가 종료된다.

이 메서드들은 쓰레드의 실행을 제어하는 가장 손쉬운 방법이지만 교착상태를 일으키기 쉽게 작성되어있으므로 사용이 권장되지 않는다.

### yield() 다른 쓰레드에게 양보한다.

`yield()`는 쓰레드 자신에게 주어진 실행시간을 다음 차례의 쓰레드에게 양보한다.

예를 들어 스케쥴러에 의해 1초의 실행시간을 할당받은 쓰레드가 0.5초의 시간동안 작업한 상태에서 `yield()`가 호출되면, 나머지 0.5초는 포기하고 다시 실행대기상태가 된다.

`yield()`와 `interrupt()`를 적절히 사용하면, 프로그램의 응답성을 높이고 보다 효율적인 실행이 가능하게 할 수 있다.

### join() 다른 쓰레드의 작업을 기다린다.

쓰레드 자신이 하던 작업을 잠시 멈추고 다른 쓰레드가 지정된 시간동안 작업을 수행하도록 할 때 join()을 사용한다.

- `void join()`
- `void join(long millis)`
- `void join(long millis, int nanos)`

시간을 지정하지 않으면, 해당 쓰레드가 작업을 모두 마칠 때까지 기다리게 된다. 작업 중에 다른 쓰레드의 작업이 먼저 수행되어야할 필요가 있을 때 `join()`을 사용한다.

```java
try{
	th1.join(); // 현재 실행중인 쓰레드가 쓰레드 th1의 작업이 끝날때까지 기다린다.
} catch(InterruptedException e) {}
```

`join()`도 `sleep()`처럼 `interrupt()`에 의해 대기상태에서 벗어날 수 있으며, `join()`이 호출되는 부분은 try - catch문으로 감싸야 한다. `join()`은 여러모로 `sleep()`과 유사한 점이 많은데, `sleep()`과 다른 점은 `join()`은 현재 쓰레드가 아닌 특정 쓰레드에 동작하므로 static메서드가 아니라는 것이다.

```java
public class ThreadJoin {
    public static void main(String[] args) {
        ThreadEx gc = new ThreadEx();
        gc.setDaemon(true);
        gc.start();

        int requiredMemory = 0;

        for(int i = 0 ; i < 20 ; i++) {
            requiredMemory = (int) (Math.random() * 10) * 20;

            if(gc.freeMemory() < requiredMemory
                || gc.freeMemory() < gc.totalMemory() * 0.4) {
                    gc.interrupt(); // gc Thread의 Thread.sleep()에 interrupt를 건다.

                    try {
                        gc.join(100); // gc Thread가 동작할 시간을 제공해준다.
                    } catch (Exception e) {
                        //TODO: handle exception
                    }
            }

            gc.usedMemory += requiredMemory;
            System.out.println("usedMemory : " + gc.usedMemory);
        }
        
    }

}

class ThreadEx extends Thread {
    final static int MAX_MEMORY = 1000;

    int usedMemory = 0;

    public void run() {
        while (true) {
            try {
                Thread.sleep(10 * 1000);
            } catch(InterruptedException e) { // interrupt 발생 시
                System.out.println("Awaken by interrupt()");
            }

            gc();
        }
    }

    public void gc() {
        usedMemory -= 300;
        if(usedMemory < 0 ) 
            usedMemory = 0;    
    }
    public int totalMemory() {
        return MAX_MEMORY;
    }
    public int freeMemory() {
        return MAX_MEMORY - usedMemory;
    }

}
```

가비지콜랙터의 동작을 구현해본 코드이다.

데몬쓰레드를 생성하여 주기적으로 동작하게 하고 특정한 조건일 때 `interrupt()`를 호출해서 즉시 실행시킬 수 있고 `join()`을 함께 사용하여 해당 데몬쓰레드가 동작할 시간을 제공해준다.

# Reference
[자바의 정석](http://www.yes24.com/Product/Goods/24259565)