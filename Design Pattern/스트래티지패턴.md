# 스트래티지 패턴

###  `스트래티지 패턴에서는 알고리즘군을 정의하고 각각을 캡슐화하여 교환해서 사용할 수 있도록 만든다. 스트래티지패턴을 활용하면 알고리즘을 사용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있다.`
---
###  문제점
  - #### 상속
    - **일부 적합하지 않은** 서브클래스가 존재할 수 있다.
  - #### 인터페이스 
    - 인터페이스를 채택한 클래스에서 구현이 이루어진다. **코드의 중복**이 야기된다.
---
### 디자인 원칙
 - #### 달라지는 부분과 달라지지 않는 부분을 분리 - **달라지는 부분 캡슐화**
 - #### 인터페이스(상위형식)에 맞추어 프로그래밍
 - #### 상속보다는 구성(Composition)
---

 개발기간보다 **개발이 끝난 후**에 코드에 더 많은 시간을 쓴다.
 재사용성도 중요하지만 **확장성,관리의 용이성**이 더 중요하다.
 **동작이 유연**하게 관리되어야 하는 설계에서는 스트래티지 패턴은 좋은 선택이다.

---
 ### 예제코드
- `Character.java`
```java
 public abstract class Character {

    AttackStrategy attackStrategy;
    MoveStrategy moveStrategy;

    public Character(AttackStrategy attackStrategy, MoveStrategy moveStrategy) {
        this.attackStrategy = attackStrategy;
        this.moveStrategy = moveStrategy;
    }

    public void setMoveStrategy(MoveStrategy moveStrategy) {
        this.moveStrategy = moveStrategy;
    }

    public void move(){
        moveStrategy.move();
    }
    public void attack(){
        attackStrategy.attack();
    }
}
```
- `Gunner.java`
```java
 public class Gunner extends Character {

    public Gunner() {
        super(new AttackStrategyWithGun(),new RunStrategy());
    }

 }
```
- `Warrier.java`
```java
 public class Warrior extends Character{
    public Warrior() {
        super(new AttackStrategyWithKnife(), new RunStrategy());
    }
}
```
---

- `AttackStrategy.java`

```java
public interface AttackStrategy {
    public void attack();
}
```
  - `AttackStrategyWithGun.java`
```java
public class AttackStrategyWithGun implements AttackStrategy{

    @Override
    public void attack() {
        shoot();
    }
    // 변화가 있을수 있는 영역 
    private void shoot(){
        System.out.println("총을 쏩니다.");
    }
}
```
- `AttackStrategyWithKnife.java`
```java
 public class AttackStrategyWithKnife implements AttackStrategy {

    @Override
    public void attack() {
       swing();
    }
    // 변화가 있을수 있는 영역 
    private void swing(){
        System.out.println("칼로 공격합니다.");
    }
}
```
---
- `MoveStrategy.java`
```java
public interface MoveStrategy {
    public void move();
}
```
- `RunStrategy.java`
```java
 public class RunStrategy implements MoveStrategy{
    @Override
    public void move() {
      run();
    }
    // 변화가 있을수 있는 영역 
    private void run(){
        System.out.println("뛰어서 이동합니다.");
    }
}
```
- `DriveStrategy.java`
```java
 public class DriveStrategy implements MoveStrategy {

    @Override
    public void move() {
        drive();
    }
    // 변화가 있을수 있는 영역 
    private void drive(){
        System.out.println("차를 타고 이동합니다.");
    }
}
```
----
- `Game.java`
```java
 public class Game {
    public static void main(String[] arg){
        Gunner gunner = new Gunner();
        gunner.move();      // 뛰어서 이동합니다.
        gunner.setMoveStrategy(new DriveStrategy()); // 동작의 변경
        gunner.move();      // 차를 타고 이동합니다.
        gunner.attack();    // 총으로 공격합니다.

        Warrior warrior = new  Warrior();
        warrior.attack();   // 칼로 공격합니다.
    }
}
```
캐릭터가 걷다가 차를 탈수도 있다. 그럴때 구체적인 클래스를 멤버변수 타입으로 지정했다면 , 유연하게 대처할 수 없었을 것이다.

캐릭터는 계속적으로 추가 될 것이고 , 공격방식이 다양해 질 것이다. 이동방법또한 추가될 수 있다.

그때마다 캐릭터 클래스가 수정되어야 하는가 ? 

위와 같은 전략패턴을 이용하면 새로운 전략이 생길 경우 해당 전략의 인터페이스를 구현한 구체클래스를 만들고 인스턴스를 **setter**를 이용해서 설정만 해주면 새로운 변화에 대해서 유연하게 대처할 수 있다.

---

 ### UML
<img width="756" alt="스크린샷 2019-04-28 오전 7 51 42" src="https://user-images.githubusercontent.com/39197978/56855883-791f2b80-698a-11e9-9a1c-28ab95b4ee48.png">