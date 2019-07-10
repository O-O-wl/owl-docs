# Design Pattern) 데코레이터 패턴

---

>### 기본샌드위치🥙를 맛있게 꾸며보자
>
>서브웨이 샌드위치 가게에 가보자.
>
>여러가지 기본 샌드위치 메뉴가 있는데  특별한 토핑을 추가할 수 있고, 사용자가 원하는 대로 더할 수 있거나 뺄 수 있다. 
>
>또 추가금액을 내고 토핑을 추가하는 경우도 있다. 토핑의 종류가 하나씩 추가됨에 따라서 ,우리가 만들 수 있는 샌드위치는 더더욱 다양해진다. 
>
>어떻게 하면 계속적으로 늘어나는 메뉴를 감당할 수 있을까?

---

| 아보카도 🥑 | 에그오믈렛 🍳 | 베이컨 🥓 | 더블치즈 🧀 |
| :--------: | :----------: | :------: | :--------: |
|     X      |      X       |    X     |     X      |
|     X      |      X       |    X     |     O      |
|     X      |      X       |    O     |     X      |
|     X      |      X       |    O     |     O      |
|     X      |      O       |    X     |     X      |
|     X      |      O       |    X     |     O      |
|     X      |      O       |    O     |     X      |
|     X      |      O       |    X     |     O      |
|     O      |      X       |    X     |     X      |
|     O      |      X       |    X     |     O      |
|     O      |      X       |    O     |     X      |
|     O      |      X       |    O     |     O      |
|     O      |      O       |    X     |     X      |
|     O      |      O       |    X     |     O      |
|     O      |      O       |    O     |     X      |
|     O      |      O       |    O     |     O      |

이렇게 토핑의 종류가 4개만 되어도 만들어질 수 있는 결과물이 16가지나 된다.

이걸 표현함에 있어서 방법이 어떤게 있을까?

```java
class 치킨로티세리샌드위치 {
  boolean 아보카도유무;
  boolean 에그오믈렛유무;
  boolean 베이컨유무;
  boolean 더블치즈유무;
	
} 
```

이렇게 표현해도 될거 같다.

그런데 기본 샌드위치가 여러개 이면 어떻게 될까??

```java
class 에그마요샌드위치 {
  boolean 아보카도유무;
  boolean 에그오믈렛유무;
  boolean 베이컨유무;
  boolean 더블치즈유무;
} 
```

이렇게 메뉴를 지속적으로 추가해 나갈 수 있을 것 같다.

하지만 문제가 발생한다. 

토핑의 종류가 추가 될 수도 있지 않을까?

그러면 기존 모든 샌드위치에 변수를 추가해주어야할 것이다.

음 상속을 이용한 방법으로 해결할 수 있을 것 같다.

```java
abstract class 샌드위치 {
  boolean 아보카도유무;
  boolean 에그오믈렛유무;
  boolean 베이컨유무;
  boolean 더블치즈유무;
} 
```

이러면 `샌드위치 추상클래스`를 상속받아서, 실제 사용할 샌드위치를 만들어내는 것도 방법일 거 같다.

**근데 프로그래밍을 할 때에 언제나 일어날 수 있는 최악의 경우의 수를 생각해보는 것이 좋다.**

같은 토핑을 2번추가할 수도 있고 , 또 특정 샌드위치는 특정 토핑을 추가 못할 수도 있다.

 `에그마요샌드위치` 는 에그 오믈렛을 추가 하지못하게 할 수 있다.

이런 상황은 충분히 일어날 수 있고 , 실제 일어나고 있다.

**카페를 가면 녹차에 샷을 추가할 수 있나? 아니면 카페에서  샷을 두개 추가하는 게 가능하지 않은가?**



### 객체의 구성을 이용하되 , 런타임에서 겍체를 구성하는 효과를 기대하는 방식으로 데코레이터 패턴은 꾀나 매력적일 것이다.

<img width="1171" alt="스크린샷 2019-06-07 오전 3 31 10" src="https://user-images.githubusercontent.com/39197978/59057167-c3be8c80-88d4-11e9-8624-6c32fba63ef6.png">



일단 구조는 이렇다.

샌드위치를 상속받은 기본샌드위치가 존재하고,

**샌드위치**를 **래핑**하는 **옵션이추가된샌드위치**가 있다.

**옵션이추가된샌드위치** 는 **샌드위치** 를 상속하기에, **중첩적인 래핑**이 가능하다.

```java
public abstract class 샌드위치 {
    abstract public int 가격을가져오다();
}
```

```java
public class 치킨로티세리샌드위치 extends 샌드위치 {
    @Override
    public int 가격을가져오다() {
        return 5990;
    }

    @Override
    public String toString() {
        return "치킨로티세리샌드위치";
    }
}

public class 에그마요샌드위치 extends 샌드위치 {
    @Override
    public int 가격을가져오다() {
        return 4300;
    }

    @Override
    public String toString() {
        return "에그마요샌드위치";
    }
}

public class BLT샌드위치 extends 샌드위치 {

    @Override
    public int 가격을가져오다() {
        return 5100;
    }

    @Override
    public String toString() {
        return "BLT샌드위치";
    }
}
```

```java
public abstract class 옵션이추가된샌드위치 extends 샌드위치 {
    샌드위치 샌드위치;
    int 추가금액;

    public 옵션이추가된샌드위치(샌드위치 샌드위치) {
        this.샌드위치 = 샌드위치;
    }
}
```

```java
public class 아보카도추가된샌드위치 extends 옵션이추가된샌드위치{
    int 추가금액 = 1100;

    public 아보카도추가된샌드위치(샌드위치 샌드위치) {
        super(샌드위치);
    }

    @Override
    public int 가격을가져오다() {
        return this.샌드위치.가격을가져오다() + this.추가금액;
    }

    @Override
    public String toString() {
        return 샌드위치.toString() + ", 아보카도";
    }
}
public class 에그오믈렛추가된샌드위치 extends 옵션이추가된샌드위치 {
    int 추가금액 = 1100;

    public 에그오믈렛추가된샌드위치(샌드위치 샌드위치) {
        super(샌드위치);
    }

    @Override
    public int 가격을가져오다() {
        return this.샌드위치.가격을가져오다() + this.추가금액;
    }

    @Override
    public String toString() {
        return 샌드위치.toString() + ", 에그오믈렛";
    }
}
public class 베이컨추가된샌드위치 extends 옵션이추가된샌드위치 {
    int 추가금액 = 900;

    public 베이컨추가된샌드위치(샌드위치 샌드위치) {
        super(샌드위치);
    }

    @Override
    public int 가격을가져오다() {
        return this.샌드위치.가격을가져오다() + this.추가금액;
    }

    @Override
    public String toString() {
        return 샌드위치.toString() + ", 베이컨";
    }
}
public class 더블치즈추가된샌드위치 extends 옵션이추가된샌드위치 {
    int 추가금액 = 800;

    public 더블치즈추가된샌드위치(샌드위치 샌드위치) {
        super(샌드위치);
    }

    @Override
    public int 가격을가져오다() {
        return this.샌드위치.가격을가져오다() + this.추가금액;
    }

    @Override
    public String toString() {
        return 샌드위치.toString() + ", 더블치즈";
    }

}
```

```java
public class main {
    public static void main(String [] args) {
        샌드위치 나의샌드위치 = new 치킨로티세리샌드위치();
        나의샌드위치 = new 아보카도추가된샌드위치(나의샌드위치);
        나의샌드위치 = new 아보카도추가된샌드위치(나의샌드위치);
        나의샌드위치 = new 베이컨추가된샌드위치(나의샌드위치);

        System.out.println(나의샌드위치);
       // Print : 치킨로티세리샌드위치, 아보카도, 아보카도, 베이컨
    }
}
```

위와 같이 계속적으로 중첩되게 데코레이터로 감싸서 , 유연한 확장을 취할 수 있다.



하지만 주의해야할 사항이 있다.

- 구체클래스 타입을 알아야하는 경우가 많을 때 데코레이터는 적절하지않을 수 있다. 

- 런타임이나 메인에서 겹겹히 감싸기 때문에, 중간에 하나를 빼먹을 수 도 있는 큰 단점이 있다. 

  - [팩토리패턴](https://o-o-wl.tistory.com/),빌더 패턴에대해 공부해보자

- 내부에 래핑된 객체에 타고타고 접근을 하여 얻어내야 하는 경우가 있다.  일단 이런 건 적합하지 않다.

- 이런 경우에는 반환하는 타입을 ArrayList와 같은 콜렉션을 하고 요소를 채워줘가면서 하면 다시 직접 안쪽으로 타고내려갈 필요가 없다.

  