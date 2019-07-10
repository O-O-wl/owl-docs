

# Factory - 팩토리

```
구체적이면 구체적일 수록 변화에 취약하다.
인스턴스를 생성하는 코드는 되게 구체적인 부분이라 유연하지 않다.
그 부분을 분리하기 하여 관리하려는 노력에서 나온 패턴이다.
```

---

- ### 변화에 취약한 부분을 분리하자

  - 구체적이면 구체적인 부분일수록 변화에 취약하다.
  - 특정 구상 클래스의 인스턴스를 생성하는 부분은 굉장히 구체적인 부분이므로 , 분리해서 관리하는 것이 좋다.
  - 구체적인 클래스에 직접적으로 접근하는 것은 **팩토리** 만으로 한정 짓는다.

- ### 생성할 객체를 추상화하자

  - 팩토리메소드는 구상클래스의 인스턴스를 반환한다 . 
  - 하지만 생성된 인스턴스를 받을 구체적인 타입을 알지 못한다.

- ### 좀더 세분화하여 구체적인 객체를 생성해야 한다면 생성 자체를 추상화하자

  ```
  객체를 생성함에 있어서 단계를 나눌 수 있다.
  
  *게임오브젝트 
    - 플레이어
   		  + 전사
   		  + 마법사
    - 몬스터
   	  	+ 슬라임
   		  + 좀비
  이렇게 2단계적으로 구체화를 거쳐가는 구조인 예에서,
  게임오브젝트를 생성하는 클래스는 추상클래스로 정의하고 , 생성 메소드는 추상메소드로 정의한다.
  게임오브젝트팩토리를 상속받는 플레이어팩토리,몬스터팩토리 에서 생성메소드를 구체화하면 책임을 더 분리할수 있다.
  ```

  - 예

    - `GameObjectFactory.java`

      - ```java
        public abstract class GameObjectFactory {
            public abstract GameObject createGameObject(String type);
        }
        ```

    - `PlayerFactory.java`

      - ```java
        public class PlayerFactory extends GameObjectFactory{
            @Override
            public GameObject createGameObject(String type) {
                switch (type){
                    case "Warrior":
                        return new Warrior();
                    case "Wizard":
                        return new Wizard();
                }
                return null;
            }
        }
        ```

    - `MonsterFactory.java`

      - ```java
        public class MonsterFactory extends GameObjectFactory{
            @Override
            public GameObject createGameObject(String type) {
                switch (type){
                    case "Slime":
                        return new Slime();
                    case "Zombie":
                        return new Zombie();
                }
                return null;
            }
        }
        ```

    ---

    ### 객체의 구성을 통해 행동을 동적으로 관리하자

    - 동작은 상태에 의존하여 결과를 뱉는다

    - 객체의 구성을 이용하면 특정 추상화된 타입의 실체를 변경함으로서 , 동작을 변경할 수있다.

    - 예)

      - `Generator.java`

        - ```java
          
          public class Generator {
              GameObjectFactory factory;
            
              public void setFactory(GameObjectFactory factory) {
                  this.factory = factory;
              }
              public GameObject generate(String type){
                  GameObject gameObject = factory.createGameObject(type);
                  gameObject.hello();
                  return gameObject;
              }
          }
          ```

      - `Game.java`

        - ```java
          
          public class Game {
              public static void main(String [] arg){
                  Generator generator = new Generator();
                  generator.setFactory(new PlayerFactory());
                  generator.generate("Warrior"); 	// 안녕하세요!전사가 생성되었습니다
                  generator.generate("Wizard");	 	// 안녕하세요!마법사가 생성되었습니다
          
                  generator.setFactory(new MonsterFactory());  
                				// 객체의 구성을 변경을 통한 동작을 동적 제어
                  generator.generate("Slime"); 		//안녕하세요!슬라임가 생성되었습니다
                  generator.generate("Zombie");		//안녕하세요!좀비가 생성되었습니다
              }
          }
          ```

      ---

      ### UML

      <img width="883" alt="스크린샷 2019-04-29 오후 4 11 24" src="https://user-images.githubusercontent.com/39197978/56881822-a52ad180-6a9c-11e9-99b8-71d7dc8a917b.png">