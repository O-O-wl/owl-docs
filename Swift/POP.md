# Swift) POP - Protocol Oriented Programming

---

- ## 상속의 한계

  - **상속의 계층 구조가 깊어질수록 복잡도가 올라간다.**

  - **값 타입 (ex - struct)는 상속이 불가능**

  - **프로퍼티 추가 불가능**

  - **추상화된 클래스를 구체화했을 때 공통적인 책임을 가지는 여러 클래스가 존재할 수 있고, 중복된 코드를 야기한다.**

    - **중복된 상황  - 억지로 만들어낸 예시 ** 

      - ```swift
        class Bird{
           // 모든 새가 날수있는건 아니어서 동작 추가 불가능 
        }
        class Owl : Bird { 
          func fly(){
            print("훨~ 훨~") } // 비행한다 - 중복 
          }
        }
        class Pigeon : Bird {
          func fly(){ 
           print("훨~ 훨~") } // 비행한다 - 중복 
          }
        }
        class Penguin : Bird{
        }
        ```

    - **프로토콜로 구현**

      - ```swift
        protocol Flyable{
          func fly()
        }
        extension Flyable{
          func fly(){ print("훨~ 훨~") }
        }
        class Bird{
           // 모든 새가 날수있는건 아니어서 동작 추가 불가능 
        }
        class Owl : Bird,Flyable{ 
        }
        class Pigeon : Bird,Flyable{
        }
        class Penguin : Bird{
        }
        ```

      

    ****

- ## Default Implement

  - 구현에 대해서 조금 **더 구체적인 규격화된 기능**을 구현하길 원하나, 상속은 적절하지 않을 때  ,`extension`을 통한 프로토콜의 메소드 기본 구현이 가능하다.

  - 프로토콜을 준수하는 클래스는 **기본구현한 메소드에 대해서 재정의가 가능**하고 재정의된 메소드가 우선순위를 가진다.

  - 예시

    - ```swift
      protocol Runable{
        func run()
      }
      extension Runable{
        // Default Implement
        func run(){
          print("슝")
        }
      }
      // 프로토콜을 체택한 클래스 정의
      struct Tiger:Runable{
      }
      struct Rabbit : Runable{
        func run(){
          print("깡총 깡총")
        }
      }
      let tiger = Tiger()
      let rabbit = Rabbit()
      tiger.run() // "슝"
      rabbit.run() // "깡총 깡총"
      ```

---

제정의가 우선순위인건 너무 좋았습니다.

```swift
extension Animal: Bird{
  func description(){
    print("새야!")
  }
}

extension Animal: Bird Where Self:Flyable{
  func description(){
    print("나는 날수 있는 새야!")
  }
}
```

위 두개의 차이가 있을까요.

둘다 `Animal`이라는 클래스를 확장과 동시에 . `Bird `라는 프로토콜을 구현하고 있습니다.

하지만 날수있는 새와 날수없는새가 존재하는거 같습니다.

위의 코드는 특정 프로토콜또는 클래스타입을 상속,구현 헀다면 확장을 하곘다는 코드로 둘은 다른 코드입니다.

이로써 좀더 다형성을 프로토콜로도 다채롭게 표현 가능해졌습니다.

새들은 거의다 나는 것 같습니다