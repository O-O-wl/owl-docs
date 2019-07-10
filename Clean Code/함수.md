# 함수

---

- ### 함수는 작게 작게 작게

  - 길어지면 읽기힘들다
  - 인덴트가 2단 이상이면 읽기 힘들다

- ### 한가지만 해라

  - 큰개념을 하는 메소드는 더 작은 개념을 수행하는 메소드들로 나누어야한다.
  
  - 내부에서 의미있는 이름의 동작로 분할해 낼 수있다면 , 한가지 일을 하지 않는다는 증거이다.
  
    - ```swift
      // 안좋은 예
      func getCoffee()->Coffee{
        let coffeeMenu = readLine() // 커피 메뉴를 입력 받는 의미있는 동작을 내부에 포함하고있다.
        let coffee = Coffee(menu:coffeeMenu)
        return coffee
      }
      // 사실상 어떤 커피를 만들지 입력을 받고 출력을 하고있다.
      ```
  
  - TO문단으로 확인해보자
  
    - 메소드명 하기위해서 , 코드 의 동작을 해야한다.
  
  - ```swift
    func getCoffee(menu:String)->Coffee{
      let coffee = order(menu:menu)
      return coffee
    }
    // To 커피를 얻기위해서 , 커피를 주문한다.
    ```
  
- ### 추상화 수준을 일정하게 해라

  - 하나의 메소드에서 호출되는 메소드들은 일정한 단계의 추상화 수준을 가져야한다.

  - ```swift
    // 안좋은 예
    func getCoffee()->Coffee{
      // 커피를 만드는 과정을 여러 과정으로 추상화했다.
    	let hotWater = Water().heatUp() 
      let shot = Shot()
      let coffee = Coffee(base:hotWater,shot:Shot)
      // 커피를 포장하는 과정을 하나의 메소드로 추상화했다.
      coffee.package()
      return coffee
    }
    ```

- ### 하나의 함수는 더 작은 추상화단계로 분리해라

  - ```swift
    func getCoffee()->Coffee{
     		let coffee = makeCoffee() // 커피를 만든다.
      	coffee = packageCoffee(coffee:coffee) // 커피를 포장한다
        return coffee
    }
    func makeCoffee()->Coffee{
    	let hotWater = Water().heatUp()  // 물을 끓인다 - 커피를 만드는 동작을 조금더 낮은 추상화 수준으로 
      let shot = Shot() // 샷을 만든다
      let coffee = Coffee(base:hotWater,shot:Shot) //커피를 생성한다
      return coffee
    }
    func packageCoffee(coffee:Coffee){
      coffee.put(holder())
      return coffee
    }
    ```

- ### Switch 문을 피해라

  - `Switch`는 근본적으로 N가지 일을 하려고 한다.

  - 다형성을 이용하면 충분히 피할 수있고 , 변화에 유연하지 않다.

  - 다형성을 적용한 객체를 생성하는 추상팩토리에서는 사용해도 괜찮다.

  - 예시

    - `다형성`

      - ```swift
        // 다형성을 적용하지 않음
        func drink(_ :Americano){}
        func drink(_ :Latte){}
        func drink(_ :Cappuccino){}
        /// 다형성을 적용하여 유연하게 할수 있다.
        func drink(_:Coffee){}
        ```

    - `팩토리에서의 switch 적용`

      - ```swift
        struct CoffeeFactory{
          func createCoffee(type:String)->Coffee{
            switch(type){
              case .americano:
              	return Americano()
              case .latte:
              	return Latte()
              case .cappuccino:
              	return Cappuccino()
            }
          }
        }
        ```

- ### 서술적인 이름을 사용하자

  - 코드는 시스템을 풀어가는 **이야기**이다.
  - 길어도 좋다 . 이해하기 좋게 네이밍을 하자
  - 일관성 , 동일한 기능에 같은 문구 , 명사 ,동사를 적용하자

- ### 인수는 적은게 좋다

  - **단항 함수**

    - 질문 / 처리 두가지중 하나의 동작을 하게한다.

    - 플래그는 내부에서 여러개의 동작을 한다는 것을 명시하는 것이다 - > 사용하지말자

      - ```swift
        // 안좋은 예
        func change(mode:Mode){
          if (mode == "채널")
          	// 채널 변경
          if (mode == "음량")
          	// 음량 변경
        }
        // 여러가지 일을 하고있다.
        ```

  - **이항 함수**

    - 이항 함수는 순서가 자연스럽지 않다.

      - ```swift
        func move( _ to:Location , _ from: Location){
        }
        move(출발지,도착지)?
        move(도착지,출발지)?
        // 자연적인 순서가 아니라 헷갈린다
        ```

    - 이항 함수면 인수의 순서가 드러나는 함수가 더 좋은 이름의 함수이다

      - ```swift
        func moveStartToDestination( _ to:Location , _ from: Location){
        }
        // 인수의 순서가 이름에 드러난다
        ```

  - **삼항 함수**

    - 가능 하면 인수를 묶을 수 있는 클래스로 바꾸자

      - ```swift
        func makeCircle( x:Int , y:Int, radius:Int)->Circle {}
        // 의미상으로 x,y 묶을 수 있다
        
        func makeCircle( point:Point, radius:Int)->Circle {}
        // x,y -> Point 로 
        ```

- ### 부수효과

  - 이름에 보이지 않는 효과는 옳지 않다.
  - 시간적 결합 / 순서적 종속성 에 얽히지 마라

- ### 출력 인수

  - 출력인수보다는 상태를 이용해라

    - ```swift
      protocol Drinkable{
        var amount:Int { get set }
      }
      struct Coffee:Drinkable{
      }
      func drink(target:Drinkable,amount:Int){
        target.amount-=amount
      }
      // 수정할 객체를 인수로 집어 넣는 것보다는 상태로 표현해보자
      ```

    - ```swift
      protocol Drinkable{
        var amount:Int { get set }
      }
      struct Coffee:Drinkable{
        func drink(amount:Int){	
       		 self.amount-=amount
      	}
      }
      // self/this로 출력인수를 대신할 수 있다.
      ```

- ### 명령과 조회의 분리

  - ```swift
    func reserve(seat:Seat,name:String)->Bool{
      if(seat.isEmpty){
        seat.setReservedPerson(name:name)
        seat.isEmpty = false
        return true
      }
      return false
    }
    
    if(reserve(seat:A , name:"부엉이")){} // 이상한 코드가 생성된다
    
    ```

- ### 오류

  - **오류코드 보다는 예외처리를 이용하자**
    - 오류코드는 바로 핸들링 해야하지만 , 예외처리로 전파하면 최상단에서의 핸들링이 가능하다
  - **예외처리에 대한 핸들링은 하나의 동작이다.**

