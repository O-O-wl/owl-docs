# Swifty Tips ⚡️

출처 : https://theswiftpost.co/swifty-tips/



25 Apr 2017

![Swifty Tips ⚡️](https://theswiftpost.co/assets/images/apple-swift-logo.jpg)

**Subtle best practises that Swift developers are keeping secret.**



내가 iOS 개발자를 시작할 때, 나는 대기업에서 사용되는 best practises에 대해서  항상 궁금했다.

그들의 프로젝트 구조는 어떻게 생겼을까?

그들이 사용하는 아키텍처는 뭘까?

어떤 서드파티 라이브러리가 인기 있을까?

다른이들의 경험으로 부터 해결하고, 이미 해결된 문제에 대해서 시간을 낭비하지 않고 싶은 열망이 있었다.

시작한 이후 4년이 흐르고, 나는 현재 많은 클라이언트와 함께 일을 하고 있고, coding practises에 대해서 우리팀은 많은 영리한 사람들과 논의한다.

이 게시글에서, 나는  현재 내가 사용하고 있는 명확하지는 않은 practises들에 대해서 이야기하려한다.

이것을 사용하고, 비판하고 또 개선하는 것에 대해서 나는 매우 환영한다.



시작해보자.🚀

 

## 1- Avoid overusing reference types

우리는 살아있는 객체들에 대해서 참조타입만을 사용해야한다. 여기서 "살아있는"이 의미하는 게 뭘까?

 아래의 예제를 보자.

```swift
struct Car {
  let model: String
}
class CarManager {
  private(set) var cars: [Car]
  func fetchCars()
  func registerCar(_ car: Car)
}
```

🚗 자동차는 그저 값이다.

이건 마치 `0` 과 같은 데이터를 표현한다.  이것은 죽어있다. 이건 어떤 것도 관리하지 않는다.

그래서 이것들은 살아있을 필요가 없습니다. 참조타입으로서 이것을 정의하는 것은 적절하지 않다.



반면에

`CarManager` needs to be a live object.

`CarManager` 는 살아있는 객체일 필요가 있습니다. 왜냐하면  응답을 기다리고, 모든 자동차들을 가져와서 저장하는 네트워크 요청을 시작하는 객체이기 때문입니다. 우리는 값타입에서 비동기 작업을 할 수 없습니다. 왜냐하면 그들은 죽어 있기 때문입니다.

우리는 마치 진짜 자동차 매니저 처럼  `CarManager` 객체가 `cars` 를 서버로 부터 가져오고, `new cars` 를 등록하기를 기대할 것입니다.

<br>

## 2- Never(?) use implicitly unwrapped properties

우리는 묵시적 언래핑을 기본적으로는 사용해서는 안됩니다. 우리는 많은 경우에 이것을 까먹습니다.

그러나 어떤 경우에서는 컴파일러를 기쁘게 해주기 위해서는 사용하기는 합니다.  그러나 숨어있는 로직들을 이해하는 것이 중요합니다.

기본적으로, 만약 프로퍼티가 초기화중에는 `nil`이어야하지만, 그 이후 값이 바로 할당 될 것이라면, 우리는 이런 프로퍼티는 명시적옵셔널 언래핑 프로퍼티로 선언할 수 있다. 왜냐하면 우리는 이것이 설정되기 전에는 접근할 수 없기때문에 컴파일러에게 nil에대한 경고를 받지 않기를 원할 수 도 있기 때문이다.

view의 경우를 보자.  

```swift
class SomeView: UIView {
  @IBOutlet let nameLabel: UILabel
}
```

만약 위와 같이 정의되어 있다면, 컴파일러는 초기화시 nil이 아닌 값을 할당하라고 우리를 압박할 것이다.

SomeView는 항상 nameLabel를 가지고 있는 것이라고 우리가 정의해두었기 때문이다. 그러나 우리는 값을 할당 할 수 없다.

왜냐하면 바인딩은 `initWithCoder` 보다 뒤에서 되기 때문이다.  우리는 nil이 아님을 확신한다. 그래서 nil 체크는 필요없다. 그러나 어쩔수 없이 해야하는 경우가 있다.

이경우에, 우리는 이것을 암시적 언래핑 프로퍼티로 선언하자. 이것은 컴파일러와 계약하는 것과 같다.

> **You**:  "이거 절대 nil 아니야, 그러니까 이것에 대해서 자꾸 나한테 경고하지마"
>
> **Compiler**: “OK.”

```swift
class SomeView: UIView {
  @IBOutlet var nameLabel: UILabel!
}
```

**Popular question**: tableview로부터 디큐되는 동안 명시적 옵셔널 언래핑을 사용해야만하나?

**Not very popular answer**: No. At least crash with a message:

```swift
guard let cell = tableView.dequeueCell(...) else {
  fatalError("Cannot dequeue cell with identifier \(cellID)")
}
```

![swifty-tips-1](https://theswiftpost.co/assets/images/swifty-tips-1.gif)

## 3- Avoid AppDelegate overuse

`AppDelegate` 는 우리의 `PersistentStoreCoordinator`, 전역 객체, 도우미 메소드, 매니저 등 을 유지하는 공간이 아닙니다. 단지 protocol을 구현한 클래스 입니다. 왠만하면 그대로 두어야합니다.

 우리가  `applicationDidFinishLaunching` 안에 해야할 중요한 것들을 가지게 구현하기도 한다. 하지만 이건 프로젝트가 거대해짐에 따라 통제하기 어려워집니다. 항상 다른 책임을 관리하는 분리된 클래스를 만들기위해 노력해야합니다.



**👎 Don’t:**

```swift
let persistentStoreCoordinator: NSPersistentStoreCoordinator
func rgb(r: CGFloat, g: CGFloat, b: CGFloat) -> UIColor { ... }
func appDidFinishLaunching... {
  Firebase.setup("3KDSF-234JDF-234D")
  Firebase.logLevel = .verbose
  AnotherSDK.start()
  AnotherSDK.enableSomething()
  AnotherSDK.disableSomething()
  AnotherSDK.anotherConfiguration()
  persistentStoreCoordinator = ...
  return true
}
```

![swifty-tips-2](https://theswiftpost.co/assets/images/swifty-tips-2.gif)

Developer in AppDelegate.swift

**👍 Do:**

```swift
func appDidFinishLaunching... {
  DependencyManager.configure()
  CoreDataStack.setup()
  return true
}
```

**#FreeAppDelegate**

## 4- Avoid overusing default parameters

You can set default values to parameters in a function.

우리는 함수의 파라미터에 기본값을 줄 수 있습니다.

이건 매우 편리합니다. 위의 코드를 아래와 같은 형태로 구현이 가능합니다.

```swift
func print(_ string: String, options: String?) { ... }
func print(_ string: String) {
  print(string, options: nil)
}
```

With default parameters, it becomes:

```swift
func print(_ string: String, options: String? = nil) { ... }
```

![swifty-tips-3](https://theswiftpost.co/assets/images/swifty-tips-3.gif)

쉽죠, 인정?  사용자 정의 UI 컴포넌트에 기본 색상을 설정하는 것, 기본도 매우 쉽고, 기본 옵션, 기본 타임아웃을 걸수 도 있다.

그러나 **의존성 주입**에 대한 부분에서는 매우 조심스럽다.

Let’s look at the following example.

```swift
class TicketsViewModel {
  let service: TicketService
  let database: TicketDatabase
  init(service: TicketService,
       database: TicketDatabase) { ... }
}
```

Usage in `App` target:

```swift
let model = TicketsViewModel(
  service: LiveTicketService()
  database: LiveTicketDatabase()
)
```

Usage in `Test` target:

```swift
let model = TicketsViewModel(
  service: MockTicketService()
  database: MockTicketDatabase()
)
```

`TicketService` 와 같은 **service** 와, `TicketDatabase` 같은 **dataBase** 는 어떤 구체타입으로 부터 추상화된 타입입니다.

이것들은 우리가 `TicketsViewModel` 에 원하는 구현을 주입할 수 있습니다. 그래서 만약 우리가 를 기본적으로 `TicketsViewModel` 에 기본 값을  `LiveTicketService` 로 주입한다면, 구체타입인  `LiveTicketService` 에 의존하게된다.  우리가 처음 달성하려던 구체타입에 의존하지 않으려던 목적과 충돌합니다.

**Not convinced yet?**

`TicketsViewModel` 는 일반적으로 두개의 타겟들에 더해질 것이다. 그때 우리는 App과 Test 타겟에 모두 TicketsViewModel을 구현한 구현체가 각각 더해지고, 각각에 다른 구현을 할텐데, 서로 컴파일못하게 됩니다.





## 5- Use variadic parameters

여러개의 값을 가질 수 있는 파라미터를 구현하면 좋다. ㅎ ㅡㅎ

이건 되게 쉽고, 강력한 구현이다. 

```swift
func sum(_ numbers: Int...) -> Int {
  return numbers.reduce(0, +)
}
sum(1, 2)       // Returns 3
sum(1, 2, 3)    // Returns 6
sum(1, 2, 3, 4) // Returns 10
```

## 6- Use nested types

![swifty-tips-4](https://theswiftpost.co/assets/images/swifty-tips-4.gif)

Swift supports inner types so you can (should) nest types wherever it makes sense.

**👎 Don’t:**

```swift
enum PhotoCollectionViewCellStyle {
  case default
  case photoOnly
  case photoAndDescription
}
```



우리는 `PhotoCollectionViewCell`  `enum` 을   외부에서 사용하지 않는다. 그래서 우리는 이걸 전역 스코프에 넣을 필요가 없다.

**👍 Do:**

```swift
class PhotoCollectionViewCell {
  enum Style {
    case default
    case photoOnly
    case photoAndDescription
  }
  let style: Style = .default
  // Implementation...
}
```

이건 `PhotoCollectionViewCell` 의 일부로서 사용하는 enum을 구현하자 그러면 Type명이 더 짧아지기까지 한다.

![swifty-tips-5](https://theswiftpost.co/assets/images/swifty-tips-5.gif)

## 7- Go final by default 🏁

기본적으로 클래스들은 `final` 이어야한다. 왜냐하면 우리는 일반적으로 그걸 확장가능하게 설계하지 않는다.

그래서 `final`로 만들지 않은 것은 실제로 에러라고 봐도됩니다. 예를들어 우리는 얼마나 우리의 `PhotoCollectionViewCell` 를 서브클래싱할까요?



**Bonus**: `final`로 구현하면 실제로 약간의 컴파일타임에서의 이득을 볼 수 있습니다.

## 8- Namespace your constants

못생긴 prefix를 가지는 전역 상수를 사용하지말고 적절하게 네임스페이스를 사용하자.

**👎 Don’t:**

```swift
static let kAnimationDuration: TimeInterval = 0.3
static let kLowAlpha = 0.2
static let kAPIKey = "13511-5234-5234-59234"
```

**👍 Do:**

```swift
enum Constant {
  enum UI {
    static let animationDuration: TimeInterval = 0.3
    static let lowAlpha: CGFloat = 0.2
  }
  enum Analytics {
    static let apiKey = "13511-5234-5234-59234"
  }
}
```

나의 개인적인 취향은 `C` 를  `Constant` 대신 사용한다. 왜냐하면 이 걸로 충분히 명확하기 때문이다. 

**Before**: `kAnimationDuration` or `kAnalyticsAPIKey`

**After**: `C.UI.animationDuration` or `C.Analytics.apiKey`

## 9- Avoid `_` misuse

`_` 는 사용되지 않는 값을 플레이스 홀더 변수이다.  이 값들에 대해 신경쓰지 않겠다는 걸 컴파일러에게 알리는 것입니다.

**👎 Don’t:**

```swift
if let _ = name {
  print("Name is not nil.")
}
```

`Optional` 은 상자 같은 것입니다. 값을 엿보면 비어있는 지아닌 지 확인할 수 있습니다. 우리는 안에 값을 꼭 다른 곳에 할당해야하는 것은 아닙니다.

**👍 Do:**

- Nil-check:

```swift
if name != nil {
  print("Name is not nil.")
}
```

- **Unused return:**

```swift
_ = manager.removeCar(car) // Returns true if successful.
```

- **Completion blocks:**

```swift
service.fetchItems { data, error, _ in
  // Hey, I don't care about the 3rd parameter to this block.
}
```

## 10- Avoid ambiguous method names

이 원칙인 실제로 사람으로 부터 이해될 수 있는 프로그래밍언어에서는 어디든 적용할 수 있다. 사람은 안그래도 컴퓨터 언어라서 이해하기 어려워한다. 그래서 우리가 말하는 것을 이해하기위해서는 추가적인 노력이 필요하다. 

For example, check this method call:

```swift
driver.driving()
```

What does it really do? My guesses would be:

- `driver` 는  `driving` 을 담당합니다
- 만약 `driver`가 `driving` 중이라면` true`를 반환합니다.

![swifty-tips-6](https://theswiftpost.co/assets/images/swifty-tips-6.gif)

If someone needs to see the implementation to understand what a method does, it means you failed naming it. 

만약 어떤 이가 메소드가 뭘 하는 지 이해하기 위해서 구현을 볼 필요가 있다면, 우리는 사실 네이밍에 실패한 것입니다.

특히, 만약 우리가 팀에서 일하고 있다면, 또 오래된 프로젝트를 다룩 있다면, 우리는 우리가 쓰는 코드 보다 읽는 코드가 많을 것입니다. 그래서 우리의 코드를 읽는 사람들에게 고통을 받게 하지 않기 위해서 이름은 명확해야한다.





### 밑에 번역은 생략~~





## 11- Avoid extensive logging



우리가 받는 작은 에러와 응답에 대해서 print하는 것을 멈추자.  진지하게, 이건 아무것도 print하지 않는 것과 동일합니다. 

왜냐하면 어떤 프린트이든 우리는 불필요한 정보의 로그를 볼 수 있을 것입니다.

![swifty-tips-7](https://theswiftpost.co/assets/images/swifty-tips-7.gif)

**👍 Do:**

- Use `error` [log level](https://www.tutorialspoint.com/log4j/log4j_logging_levels.htm) in frameworks you use.
- Use logging frameworks (or implement it yourself) which let you set log levels. Some popular frameworks: [XCGLogger](https://github.com/DaveWoodCom/XCGLogger), [SwiftyBeaver](https://github.com/SwiftyBeaver/SwiftyBeaver)
- Stop using logging as a primary source for debugging. Xcode provides powerful tools to do that. Check [this blog post](https://www.objc.io/issues/19-debugging/debugging-case-study/) to learn more.

## 12- Avoid disabling unused code



Stop commenting-out code pieces. If you don’t need it, just remove it! That simple. I have never solved a problem by enabling legacy code. So clean up your mess and make your codebase readable.

------

### What if I told you…

![swifty-tips-8](https://theswiftpost.co/assets/images/swifty-tips-8.png)

…that you can achieve most of it with automation? See [Candost](https://twitter.com/candostEN)’s post on [Using SwiftLint and Danger for Swift Best Practices](https://theswiftpost.co/using-swiftlint-and-danger-for-swift-best-practices/).

------

**Thanks for scrolling all the way!**