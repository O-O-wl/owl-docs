# Swift) Extension 익스텐션

---

- ## 사용하는 이유

  - **기존 타입의 기능확장**
    - **확장된 코드 영역은 비교적 `side effect` 에서 자유로움**
    - **기능별로 단위를 묶어서 표현하기 쉬움**

- ## 특징

  - **열거형, 구조체, 클래스, 프로토콜 확장 가능 **
  - **재정의는 불가능**
  - **연산프로퍼티 추가가능**
  - **상속과는 달리 수평적 기능 추가의 느낌이 있음**

- ## 사용 예시

  - **기존의 기본클래스(구조체)의 기능적 확장**

    - ```swift
      extension Int{
        func changeToString ->(String){
          return "\(self)"
        }
      }
      ```

  - **Default Implement - 프로토콜 기본구현**

    - ```swift
      protocol runable{
        func run()
      }
      extension runable{
        func run(){	print("뒤뚱뒤뚱")	}
      }
      ```

  - **TypeAlias 와 함께 사용**

    - ```swift
      typealias Age = Int
      extension Age{
        mutating func aging(){
          self += 1
        }
      }
      ```





