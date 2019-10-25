# iOS Swift : MVP Architecture

https://medium.com/@saad.eloulladi/ios-swift-mvp-architecture-pattern-a2b0c2d310a3

[![Saad El Oulladi](https://miro.medium.com/fit/c/96/96/1*039wGBXzAHCxg1UtIYX1KQ.jpeg)

You might be familiar with MVC: The most common application architecture pattern. Whether in mobile web or desktop applications. you can find it everywhere with its three main components (View, Controller, Model).

우리는 아마도 MVC에 친숙하다. 가장 흔한 앱 아키텍처 패턴이다.

우리는 이것이 View Model Controller 3개의 메인 컴포넌트를 많은 곳에서 사용합니다.

![img](https://miro.medium.com/max/60/1*K54FxRTRhpjKih4ZxH6g5Q.png?q=20)



MVC was adopted by Apple as an official architectural pattern for iOS. Where :

애플은 iOS 공식 아키텍처 패턴으로 MVC를 채택헀습니다. 



- View: 는 xib파일 입니다. 또는 UIView의 서브클래스입니다.

- Controller:는 액션과 이벤트를 View로 부터 받고 그것을 업데이트 하는 UIViewController의 서브클래스입니다.

- Model: 은 우리 앱의 데이터를 나타낸 객체입니다.

  

![img](https://miro.medium.com/max/60/1*xoRTTRc4ARkBL_YdlThY-g.png?q=20)



# **The problematic :**

MVC의 초기의도는 앱의 컴포넌트들을 분리된 조각으로 나누기위해서였습니다. 그러나 점직적으로 이런 결과를 낳게됩니다.

**Lack of Distribution** :  컨트롤러는 거의 모든 작업을 갖습니다. 유저와의 상호작용에 대한 핸들링, 네트워크 요청, 데이터 파싱 등  ...

이것이 우리가 아는 Massive View Controller 입니다



**Low test coverage** : 

단일책임원칙을 가볍게 부셔버린다. controller는 매우 강하게 view life cycle에 연관되있다.

그래서 view controller는 매우 작업을 테스트하기 힘들어진다,.



**MVP As** an alternative :

**MVP 아키텍처**는 MVC에 **Presenter**라는 메인 컴포넌트를 더함으로써 이러한 상황을 개선한다. 

![img](https://miro.medium.com/max/60/1*es5q02G0YfjnNi5POob2nQ.png?q=20)



![img](https://miro.medium.com/max/36/1*7pZ1fFfQwtnehTS8i2DLrA.jpeg?q=20)



But this is the same architecture as before !!!!!

> **잠깐! MVC와 비슷해보이지만 다른 중요 차이점을 가졌다.** 

Now the **viewController is considered as a view**.

현재 ViewController는 View로써 다뤄질 것이다.

View와 관련된 코드만을 포함할 것이라는 것을 의미한다.

긜고 다른 모든 로직은 presenter에 구현한다.



Then components description becomes as the following :

- **View** : 는 view  와  UI setup , event 를 다루는 view controller 로 구성된다 
- **Presenter** : 는 유저 액션에 반응하는 UI업데이트를 책임지는 모든 로직을 책임질 것이다.  그리고, 여기서의 중점은 Presenter는 UIKit에 의존하지 않는다는 것이다. 이건 테스트 가능함을 의미하기도 한다.
- **Model** : 은 MVC의 모델과 동일하다.

> **MVP 는  passive View pattern을 사용한다는 것이 중요하다.**
>
> 이 패턴은 모든 액션이 presenter 에게 전송될 것임을 의미한다. 그래서 view는 단지 액션을 전달하고 presenter의 업데이트에 귀 기울일 뿐이다.

## **No more talking. let’s get hands dirty !!**





한번 프로젝트를 해보자. 우리는 먼저 Model을 생성할 것이다.

`Traffic Light `  라는 color/ status 만을 가지는 단순한 구조체를 만들자. 

```swift
struct TrafficLight {
    let colorName: String
    let description: String
}
```



<iframe src="https://medium.com/media/ee41ac064ce25537eb53e15afa13e6a9" allowfullscreen="" frameborder="0" height="170" width="680" title="" class="ga n o fz ag" scrolling="auto" style="box-sizing: inherit; top: 0px; left: 0px; width: 680px; height: 170px; position: absolute;"></iframe>



**Service 클래스는 데이터 제공자 역할을 하는 객체이다.**

 우리는 실제 앱에서 주로 웹서비스를 사용한다. 단순히 모델을 반환한다.

```swift
class TrafficLightService {
    
    func getTrafficLight(colorName:(String), callBack:(TrafficLight?) -> Void) {
        let trafficLights = [TrafficLight(colorName: "Red", description: "Stop"),
                             TrafficLight(colorName: "Green", description: "Go"),
                             TrafficLight(colorName: "Yellow", description: "About to change to red")
        ]
        
        if let foundTrafficLight = trafficLights.first(where: {$0.colorName == colorName}) {
            callBack(foundTrafficLight)
        } else {
            callBack(nil)
        }
    }
}
```



<iframe src="https://medium.com/media/b2f414f94ca21a8e46ce980f12f877b2" allowfullscreen="" frameborder="0" height="412" width="680" title="" class="ga n o fz ag" scrolling="auto" style="box-sizing: inherit; top: 0px; left: 0px; width: 680px; height: 412px; position: absolute;"></iframe>

**TrafficLightViewDelegate** 프로토콜은 **view / viewcontroller 내에 구현된다.** 

```swift
protocol TrafficLightViewDelegate: NSObjectProtocol {
    func displayTrafficLight(description:(String))
}
```

우리는 presenter에서 view로 정보를 전달하기 원할 때, 사용할 것이다. view controller에게 정보를 전달하는 걸 가능케 한다.





그 후 우리는 메인 컴포넌트 **Presenter를** 구현한다.

```swift
class TrafficLightPresenter {
    private let trafficLightService:TrafficLightService
    weak private var trafficLightViewDelegate : TrafficLightViewDelegate?
    
    init(trafficLightService:TrafficLightService){
        self.trafficLightService = trafficLightService
    }
    
    func setViewDelegate(trafficLightViewDelegate:TrafficLightViewDelegate?){
        self.trafficLightViewDelegate = trafficLightViewDelegate
    }
    
    func trafficLightColorSelected(colorName:(String)){

        trafficLightService.getTrafficLight(colorName: colorName) { [weak self] traficLight in

            if let traficLight = traficLight {
                self?.trafficLightViewDelegate?.displayTrafficLight(description: traficLight.description)
            }
        }
    }
}
```



Presenter는 2개의 프로퍼티를 가졌다.

- 첫번쨰는  `TrafficLightService` 
- 두번쨰는  presenter는 view로 부터 소유 될 것이기 때문에  **weak 참조로 `view delegate`**를 가집니다.

그 후 `trafficLightColorSelected` 는 view로 부터 선택된 색상을 presenter에 전달하는 이벤트에서 사용된다.

그 다음은 **main storyboard**

View controller 스크린에 3개 색상 버튼과 하나의 레이블을 더하자.

![img](https://miro.medium.com/max/44/1*FYmm2ILC6AcgtNwm_Cs9aA.png?q=20)



버튼들은 3가지 신호등 상태를 표현한다. 그리고 레이블은 현재 상태를 화면에 보여준다.

마지막으로 우리의 `TrafficViewController` 를 작성해보자. 뷰말고는 다른 어떠한 것도 없다.

```swift
import UIKit

class TrafficLightViewController: UIViewController {
    
    // MARK: - IBOutlets
    @IBOutlet weak var descriptionLabel: UILabel!
    
    // MARK: - Properties
    let presenter = TrafficLightPresenter(TrafficLightService())
    
    // MARK: - Methods
    override func viewDidLoad() {
        super.viewDidLoad()
        presenter.delegate = self
        // Do any additional setup after loading the view.
    }
    
    // MARK: - IBActions
    @IBAction func greedLightButtonDIdTap(_ sender: Any) {
        presenter.trafficLightDidSelected(color: "Green")
    }
    
    @IBAction func yellowLightButtonDIdTap(_ sender: Any) {
        presenter.trafficLightDidSelected(color: "Yellow")
    }
    
    
    @IBAction func redLightButtonDIdTap(_ sender: Any) {
        presenter.trafficLightDidSelected(color: "Red")
    }
    
    
}
// MARK: - + TrafficLightViewDelegate
extension TrafficLightViewController: TrafficLightViewDelegate {
    func displayTrafficLight(description: (String)) {
        self.descriptionLabel.text = description
    }
}
```



And finally here is our **traficViewController**. which is now nothing more than a view.

<iframe src="https://medium.com/media/08a1466716c2a323091c6499bc077f2e" allowfullscreen="" frameborder="0" height="632" width="680" title="" class="ga n o fz ag" scrolling="auto" style="box-sizing: inherit; top: 0px; left: 0px; width: 680px; height: 632px; position: absolute;"></iframe>





# **Conclusion :**

presenter가  UIKit을 사용하지 않고 메인 액터로 역할을 한다. 그리고 massive view controller 를 없앨 수 있다.

동시에 단위 테스트를 수행하기 쉬운 방식으로 뷰 수명주기에서 로직을 분리했다.

그러나 presenter 너무 많이 일을 하는 것에 대한 완벽한 해결책이 없다 . 우리는 단일책임을 지키지는 못한다.

더 많은 개선된 아키텍처는 이러한 이슈를 풀기위해서   VIPER, CLEAN, MVVM… 같은 것들이 이런 개선책들이다. 

---

