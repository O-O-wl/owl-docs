# 싱글턴 패턴

---

> 세상에 단 하나뿐인 특별한 객체 
>
> 어떤 상황에서 필요한 패턴일까?
>
> 전역변수와의 차이는 무엇일까?

정보를 한곳에 모아두고 하나의 객체만이 그 정보를 관리해아하는 경우가 있다.

적당한 예로는 수강신청프로그램 이 있다.

수강신청이란 치열한 전쟁과도 같은데 성공적인 수강신청을 위해 1분1초를 다투며 마우스를 클릭한다.

만약 여러 수강신청플랫폼을 통해서 수강신청을 할 수 있다고 가정을  해보자.



수강신청알고리즘은 이런식으로 수강신청을 할 수 있다고 가정하자.

```java
int 정원 = 1
int 현재인원 = 0
public void 수강신청() {
  if ( 정원 > 현재인원 ) {
    	현재인원++;
      수강성공출력();
  }
  else {
    	수강실패출력();
  }
}
```

별 문제 없어보이는 데 어떤 문제가 생길까?

수강신청은 병렬로 처리된다는 것이다.

학생1 , 학생2 는 서로의 상태를 모르고 동시에 수강신청을 누를수 있다.

이 과정에서 

```java
// 학생1
 if ( 정원 > 현재인원 ) // true 
   현재인원++;
	 // Print : 수강신청 성공 😍
// 학생2
 if ( 정원 > 현재인원 ) // false 
   // Print : 수강신청 실패 😂
```

이렇게 되기를 예상했을 것이다 .

하지만 예상과 다르게

```java
// 학생1
 if ( 정원 > 현재인원 ) // true   
// 학생2
 if ( 정원 > 현재인원 ) // true 
// 학생1
   현재인원++;
		// Print : 수강신청 성공 😍
// 학생2
	 현재인원++;
		// Print : 수강신청 성공 😍
```

실행순서가 이렇게 `if` 문은 통과했지만 **현재인원을 증가시키기전**에 학생2가 `if`문을 통과해버린다면 아래와 같은 문제가 생길 수 있다.

그렇다면 저 메소드를 접근할 객체를 **단 하나**로 하면 된다.

그러기위해서 코드의 개선을 어떻게 해가야할까 ?

```java
class Singleton {
  public Singleton () { }
}
```

일단 어디서든 생성할 수 있는 저 `이니셜라이저`에 제한이 필요할 것같다.

계속적으로 객체가 생성되서는 안되니까

```java
class Singleton {
  private Singleton () { }
}
```

생성자를 감추어 버렸다.

우리는 한개가 필요한거였지 아예없는 걸 원한 건 아니다.

그러면 하나의 통로를 만들어주자. 

```java
class Singleton {
  private static Singleton intance ;
    
  private Singleton () { }
  public static Singleton getInstance() {
    if  ( intance == null ) {
      intance = new Singleton()
    }
    return intance
  }
}
```

이렇게 `getInstance()`라는 메소드로만 객체를 가져올 수 있고,  호출 시점에 객체가 없다면 생성하고 , 있다면 있는 객체를 그대로 반환하는 

메소드로 유일한 객체를 만들게 되었다.

하지만 문제가 수강신청때와 같이  동시에 `getInstance()` 내부의 `if` 문을 통과하게 된다면 , 유일해야하는 객체가 하나 더 생길 수 있다는 걸 알 수 있다.

이럴 경우 `이니셜라이저`에 `synchronized`키워드를 붙여준다면 말끔히 해결된다.

### 하지만! 이건 그렇게 권장되지 않는다 , synchronized 는 상대적으로 꽤나 큰 성능 저하를 일으킨다.

대략 100배 정도 성능이 저하된다고 한다.

**`getInstance() `메소드에서 `if`문은 동기화가 필요한 영역**이지만 

메소드 자체를 동기화할 필요가 있을까? 

전역에서 인스턴스를 가져오는 부분이 아닌 **생성하는 부분을 블럭을 동기화하여 사용**하면 될 것같다.



```java
class Singleton {
  private volatile static Singleton intance ;
    
  private Singleton () { }
  public static Singleton getInstance() {
    if  ( intance == null ) {
            synchronized (Singleton.class) {
                if(instance == null) {
                    instance = new Singleton();
            }
        }
    }
    return instance
}
```

`DCL ( Double Checking Lock )` 기법을 이용한 구현방법이다.

동기화영역을 축소하여 성능저하를 감소시킬 수 있다.

