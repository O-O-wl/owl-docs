# UIStoryboardSegue

https://developer.apple.com/documentation/uikit/uistoryboardsegue

---

>  두개의 뷰 컨트롤러 사이의 시각적인 전환을 수행하고, 수행을 위한 준비를 하는 객체.

---

<br><br>

### Declaration

```swift
class UIStoryboardSegue: NSObject
```

<br>

<br>

### Overview

---

`UIStoryboardSegue` 클래스는 UIKit에서 사용가능한 시각적인 표준 전환들을 제공한다. 스토리보드 파일내에 뷰 컨트롤러간의 전환을 커스텀으로 정의하기 위해서 서브클래싱을 할 수 있다.

세그웨이 객체는 전환에 관련된 뷰 컨트롤러들의 정보를 포함하고 있다.

세그웨이가 발생했지만, 시각적인 전환은 발생하기 이전에 스토리보드 런타임은 를 곧 디스플레이 될 뷰 컨트롤러에서 필요로하는 정보를 전하기위해서 `prepare(for:sender)`  메소드 호출한다.

<br>

세그웨이를 객체를 직접 생성하지 말자. 대신, 스토리보드 런타임은 두개의 뷰 컨트롤러 사이의 세그웨이가 수행되어야만 할 때, 생성한다. 코드상으로 `performSegue(withIdentifier:sender:)`  메소드를 사용하여서 초기화 할 수는 있다. 프로그래밍으로 초기화해서 인터페이스 빌더에서 추가히기 어려운 경우 사용하자.

<br>

---



<br>

<br>

### Subclassing Notes

---

앱 내에서 두개의 뷰 컨트롤러간의 커스텀한 전환을 제공하기를 원할 떄, `UIStoryboardSegue` 를 서브 클래스할 수 있다. 

커스텀 세그웨이를 사용하기 위해 인터페이스 빌더안의 적절한 뷰 컨트롤러들 사이의 세그웨이 라인을 만들어라. 그리고 inspector 내에 세그웨이 클래스 명을 커스텀 세그웨이 클래스 명으로 바꿔자.

커스텀 세그웨이를 런타임에서 인지하면, 새로운 인스턴스를 생성하고, 뷰 컨트롤러객체들을 구성하고, 뷰 컨트롤러의 소스에 세그웨이를 준비할 것을 요청합니다.

그리고 세그웨이를 수행합니다.

<br>

---

<br><br>

### Methods to Override

---

커스텀 세그웨이에 대해서, 재정의해야하는 메소드는  `perform()` 메소드이다.

`source`  에서  `destination`  전환할 때,뷰 컨트롤러의  스토리보드 런타임은 해당 메소드를 호출한다.  만약 세그웨이의 서브 클래스의 변수들을 초기화할 필요가 있다면, `init(identifier:source:destination:)` 	를 재정의할 수 있고, 그 변수를 커스텀한 구현 내에서 초기화 할 수 있다. 

<br><br>

### Alternatives to Subclassing 

---

만약 `perform()` 메소드 외에 다른 추가적인 정보  를 저장할 필요가 있는 게 아니라면, 서브 클래싱대신 `init(identifier: source: destination: performHandler:)` 메소드를 이용해보세요

```
🦉
perform() 의 재정의 만을 위한 서브클래싱은 피하자.
이런 경우 perform을 바꿀 수있는 기본 이니셜라이저가 제공되고 있따
```



<br>

<br>

### Topic

---

<br>

#### Initializing a Storyboard Segue

---

- **`init(identifier: String?, source: UIViewController, destination: UIViewController)`**
  - 스토리보드 세그웨이 객체를 반환한다.

<br>

---

<br>

<br>

#### Accessing the Segue Attributes

---

- **`var source: UIViewController`**
  - 세그웨이의 시작 점 뷰 컨트롤러
- **`var destination: UIViewController`**
  - 세그웨이의 도착 점 뷰 컨트롤러
- **`var identifier: String?`**
  - 세그웨이 객체의 식별자

<br>

---

<br>

<br>

### Performing the Segue

---

- **`func perform()`**
  - 세그웨이의 시각적인 전환을 수행하는 메서드

<br>

---

<br>

<br>

### Creating a Custom Segue

---

- **`init(identifier: String?, source: UIViewController, destination: UIViewController, performHandler: () -> Void)`**
  - 세그웨이 전환을 수행하기 위한 세그웨으를 만드는 메서드