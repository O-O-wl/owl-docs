# `enum`
---
- **하나로 연관된 주제를 하나의 타입으로 묶어서 사용**
- **조금더 안전한 방식으로 다룰 수 있게 해준다.**
---
### `상황`
-   **제한된 값 만 입력받고 싶을 때**
-  **제한된 값만 선택할수 있게 제한하고싶을 때**

#### `예시`
```swift 

//   열거형 : rawValue
enum Unit : String {
    case    cm =   "cm"
    case    m =    "m"
    case inch = "inch"
    case yard = "yard"
    
    case g  = "g"
    case kg = "kg"
    case oz = "oz"
    case lb = "lb"
    
    
    // 함수도 내포할수 있다
    func defaultUnit ()->Unit{
        
        switch self {
        case .cm,.m,.inch,.yard:
            return .m
        case .g,.kg,.oz,.lb:
            return .kg
        case .error:
            return .error
        }
    }
}
```
- **`rawValue: 열거형 해당 case에 관련된 값`**

---
#### `Associated Values`
 - **열거형 케이스에 추가적인 정보를 내포할수있게한다.**

``` swift
    enum image{
        //              알파값이 필요하다
        case png(Int,Int,Double)
        case jpeg(Int,Int)
        ...
    }

```

#### enum 은 내부적으로 enum 을 내포할수 있다.

```swift
    enum Unit{
        enum Length{
            case cm,m,inch,yard(Double)
        }
        enum Volume{
            case L,pt,qt,gal(Double)
        }
        enum Weight{
            case g,kg,oz,lb(Double)
        }
    }

    let input = Unit.Volume.L(100)
```