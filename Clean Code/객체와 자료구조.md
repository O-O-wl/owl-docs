# 객체와 자료구조

---

> 객체는 일을 한다 .
>
> 자료구조는 자료를 저장한다.
>
> 이 둘을 적절한 상황에 사용하기 위해 한번 공부해보자



우리는 객체를 디자인함에 있어서 , 객체의 내부의 변수를 `private`로 감추어둔다.

외부의 객체가 내 변수에 의존하지 않게 하기 위해서 이다.

변수의 수정에 외부에 영향을 주지 않게 하기 위해서, 외부에서는 내 변수에 대해 아무것도 모르게 하려고 

더 좋은 구조를 위해서 변수의 수정에 자유로울 수 있기 위해서이다.

* **객체**는 내부의 구현을 숨기고 **객체가 무슨 일을 하는지 ** 만을 공개한다.  

* **자료구조**는 **내부의 변수(자료)를 공개**하고 별다른 동작을 하지 않는다



```swift
struct MyPoint {
  private var x: Int
  private var y: Int
  
	func getX() -> Int { return self.x }
  func getY() -> Int { return self.y }
}
```



위의 `MyPoint` 는 객체인가 자료구조인가??

변수는 감추어 두고 메소드만 공개되어 있는 걸로 볼 때, 객체로 보일 수 있으나, 

별다른 동작이 없이, 변수에 접근하는 동작을 메소드를 거쳐 할 뿐인 객체가 되었다.

객체는 구현을 숨겨야하는데  메소드명에 구현이 공개되어있다. 

이건 자료구조에 더 가까운 형태라고 볼 수 있겠다.



분명히 자료구조와 객체는 각각의 이점이 있을 것이다.

자료구조/객체는 어느 상황에 사용해야 더 효율적인 코드를 작성할 수 있을까?



자료구조는 자료를 제어하는 어떠한 메소드도 제공하지 않는다.

여러 자료구조를 제어할 수 있는 별도의 객체로 자료를 제어한다.

```swift
struct Square {
    var topleft: MyPoint
    var size: CGSize
}

struct Circle {
    var center: MyPoint
    var radius: Double
}

struct Geometry {
    func area(object: Any) throws -> Double {
        var area: Int
        switch object {
        case let circle as Circle:
            area = 3.14 * circle.radius * circle.radius
        case let square as Square:
            area = square.size.width * square.size.height
        default:
        throw Error.unsupported
        }
        return area
    }
}
```

자료구조가 여럿 존재하고, 자료구조를 이용하여 연산하는 별도의 객체로 자료를 사용한다.

지금의 경우 `Geometry`가 하는 연산의 종류는 1개이지만, 만약 연산의 종류가 많아진다면 

새로운 자료구조가 추가될 때마다 모든 함수가 수정되어야 할 것이다.

하지만 새로운 연산을 추가하는 것에는 상대적으로 자유로움을 볼 수 있다.

연산은 함수가 담당한다, 연산을 하나의 객체가 모두 수행함으로써 연산이 추가될 때 연산을 담당하는 객체의 수정만 있으면 된다.

하지만 연산의 대상이 추가된다면, 모든 연산에 케이스를 추가해야 하므로 수정할 영역이 엄청나게 커지게 된다.

```swift
protocol Shape {
  func area() -> Double
}

struct Square: Shape {
    private var topleft: MyPoint
    private var size: CGSize
  
  	func area() -> Double {
      return self.size.width * self.size.height
    }
}

struct Circle: Shape {
    private var center: MyPoint
    private var radius: Double
  
  	func area() -> Double {
      return 3.14 * self.radius * self.radius
    }
}
```

자료구조가 아닌 객체로 코드를 작성한다면, 앞으로 추가될 객체에 대해 자유로울 수 있다.

하지만 객체의 종류가 많아졌을 때,

`Shape`인터페이스에 함수를 하나 추가하게 되는 경우  모든 객체들이 줄줄이 수정되야 해야할 것이다.



**자료구조**는 자료를 공개해두고 다른 한곳에서 구조별 연산을 하기에 **타입을 추가하게 되면 모든 연산에 영향**을 주게되는 단점이 있고

**객체는 새로운 연산을 추가하게되면 모든 객체에 해당 연산을 추가**해야 하는 단점이 있다.



타입이 계속적으로 추가될 가능성이 농후한 상황에서는 객체로 작성하고, 연산이 추가로 계속적으로 생길 수 있다면 자료구조로 코드를 작성하는 것도 좋은 선택일 수 있다.

### 

여기서 객체를 설계함에 있어서 하나의 법칙이 존재한다.

>  디미터의법칙 - 모듈들은 자신이 조작하는 객체에 내부사정을 몰라야 한다.
>
> 객체는 자료를 숨기고, 무엇을 하는지만 공개한다. 



추가로, 객체는 다음과 같은 객체의 메소드만을 호출해야 한다.

> - 자신 클래스의 메소드
> - 자신 내부에서 생성한 객체의 메소드
> - 인자로 받은 객체의 메소드 - **연관관계**
> - 내부에 변수로 가지고 있는 객체의 메소드 - **합성관계**



그렇다면 이런 코드는 어떨까?

```swift
var userName = self.getApplicationContext().getCurrentUser().getUserName()
```

객체는 내부의 구현을 숨겨야 하는데 훤히 보이는 상황이다.

이러한 표현을 **기차충돌**이라고 하는데 위의 4가지의 경우가 아닌 객체의 메소드를 호출하고 있다.

너무 많은 지식을 알고 있어야 하는 코드이다.



#### 다른 객체에서 자료를 가져와서 처리하기보다는, 다른 객체에게 일을 시켜라.

이렇게 줄줄이 타고 와서 자료를 가져오기보다는, 자신의 자료 input으로 넘기고 output으로 결과를 얻어오는 방법이 적절할 수 있을 것이다.





 