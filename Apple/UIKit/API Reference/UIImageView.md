# UIImageView

---

>단일 이미지 또는 연속되는 이미지를  인터페이스에 디스플레이 해주는 객체

https://developer.apple.com/documentation/uikit/uiimageview

<br/>

### OverView

- 특정한 `UIImage`  객체를 사용하는 어떠한 이미지를 능률적으로 그릴수 있게 해준다.

- example

- ```
  UIImageView 클래스를 다양한 표준 이미지파일들(PNG,JPEG)의 컨텐트를 그리기위해서 사용할 수있다.
  ```

- 코드 또는 인터페이스빌더로 이미지를 설정할 수 있고, 런타임에서 이미지를 변화시킬 수도 있다.

- animation을 start, stop 하는 메서드를 사용할 수 있다.

  <br/>

  ---

  <br/>

  <br/>

### UnderStanding How Images Are Scaled ◾️◼️⬛️

- `ImageView`는 `contentMode` 라는 프로퍼티를 사용하여, 어떻게 이미지가 디스플레이 되어 질 지를  스스로 결정합니다.
- 지정된 이미지의 크기와 `ImageView`의 크기가 정확하게 일치하는 것이 가장 좋지만, 그렇지 않다면 `imageView`는 이미지의 비율을 조절한다.
- `ImageView`의 크기가 변하기 된다면 **필요에 따라  이미지의 크기도 같이 변해야할 수 있다.**
- `Cap inset` 이 없는 이미지는` ImageView`의 `contentMode`  홀로 이미지의 표현을 결정한다.



##### What is ContentMode ?🧐

> 뷰의 사이즈가 변할 때, 그들의 컨텐트를 어떻게 적응시킬 지 지정하는 옵션
>
> - `scaleToFil`
>
> <img width="200" alt="scaleFill" src="https://user-images.githubusercontent.com/39197978/62196543-3588f300-b3b9-11e9-9166-6bf5c9b26576.png" align = left> <br><br><br><br><br>	필요에 따라 이미지의 
>
> ​	aspect ratio(종횡비)를 바꾸면서
>
> ​	컨텐트의 사이즈를 맞추는 옵션
>
> ​	**이미지의 가로세로 비율이 변경되어 보여질 수있다** 
>
> <br><br><br><br>
>
> 
>
> <br>
>
> - `scaleAspectFit`
>
> <img width="200" alt="aspectFit" src="https://user-images.githubusercontent.com/39197978/62196542-3588f300-b3b9-11e9-9e7f-6669bc2b13b6.png" align = left> <br><br><br><br><br><br><br>	aspect ratio를 유지하면서, 
>
> ​	컨텐트의 사이즈를 알맞게 사이즈를 맞추는 옵션
>
> ​	**남는 부분은 투명하게 처리한다**.
>
> <br>
>
> <br>
>
> <br><br>`scaleAspectFill`
>
> <img width="200" alt="aspectFill" src="https://user-images.githubusercontent.com/39197978/62196540-3588f300-b3b9-11e9-8bc7-0aa9300d1a1a.png" align = left>	<br><br><br><br><br><br>	Aspect ratio 를 유지하면서, 
>
> ​	컨텐트를 뷰에 가득 채운다.
>
> ​	**뷰를 가득 채우기 위해, 컨텐트의 일부가 잘릴 수 있다.**
>
> <br><br><br><br><br><br>
>
> <br>
>
> ---

<br><br>

### Determining the Final Transparency of the Image ⬜️⬛️

<br>

- 이미지는 **ImageView의 background 와 합성된 이후**, window의 나머지와 합성된다.
- 이미지의 투명도는 이미지뷰의 배경을 볼 수 있게 해준다. 
- 이미지의 배경의 투명도는 이것을 보여주는 **`UIImage`의 투명도와 `UIImageView`의 투명도**에 의존한다.
  - 이미지는 이미지뷰의 배경에 합성이 된다.
  - `UIImageView`의 프로퍼티`isOpaque` 가 `true`이면 이미지의 픽셀들은 이미지뷰의 배경색의 위에 합성되고, 이미지의 `alpha` 값은 무시된다.
  - `UIImage`의 `isOpaque` 가 `false` 이면, 각각의 픽셀의  `alpha` 값은 imageView의 alpha값과 곱해진다. 그 결과값은 실제 그 픽셀의 투명도에 반영된다. 만약 이미지가 알파채널(jpg 같은 이미지파일)을가지지 않는다면 이미지의 알파를 1.0으로 가정한다.

<br>

---

<br>

<br>

### Animating a Sequence of Images 🛬🛬🛬🛫🛫

<br>

**Image view**는 움직이는 연속되는 이미지를 저장할수 있다.  그리고 전체 또는 일부를 재생할 수있다.

**`UIImage`객체의 배열**로 연속되는 이미지를 명시하고, `animationImages` 프로퍼티에 그것들을 할당하면된다.

일단 한번 할당되면, `UIImageView`의 애니메이션 타이밍과 시작,취소를 설정하는 메소드와 프로퍼티들을 사용할 수 있다.  



#### 움직이는 이미지들을 디스플레이 할 때, 고려해야할 사항 Tip💭 ####

- **모든 연속되는 이미지는 같은 크기를 가져야한다.** 비율의 조정이 필요할 때, 이미지뷰는 연속되는 이미지를 각각 분리적으로 비율을 조정한다. 만약 이미지들의 크기가 다를 경우, 원하는 결과와는 다른 결과를 산출할 수 있다. 
- **모든 연속되는 이미지는 같은 컨텐트 스케일(비율) 요소를 사용해야한다.** 각각의 이미지의 `scale` 프로퍼티가 같은 값을 포함하는 지 확실히 해두자.

<br>

---

<br>

<br>

### Responding to Touch Events 👈

<br>

이미지뷰는 기본적으로는 유저의 이벤트들을 무시한다.  일반적으로는, 이미지뷰는 단지 시각적인 컨텐트를 인터페이스에 표현하기 위해서 사용한다. 만약 그뿐아니라, **유저와의 상호작용을 핸들링 하기위해서 이미지뷰를 사용하기를 원한다면,**

`isUserInteractonEnabled` 프로퍼티값을 `true`로 하면 가능하다. 그 이후 **gesture recognizer** 를 붙이거나, 

다른 기술로 이벤트 핸들링을 이용할 수 있다.

<br>

---

<br>

<br>

### Tips for Improving Performance ⬆️

<br>

이미지 **scaling** 과 **alpha blending** 은 앱의 성능에 영향을 끼칠 수있는 비용이 비싼 연산이다.

이미지뷰 코드의 성능을 최대치로 끌어올리기 위해서 아래의 사항을 고려해보자.



- **빈번하게 사용되는 이미지의 스케일된 버전을 캐시하자.** 거대한 이미지를 작게 스케일된 썸네일 이미지를 빈번하게 보여주는 게 예상되는 경우, 작게 스케일된 이미지들을 미리 만들고 그 이미지들을 썸네일 캐시로 저장하는 것을 고려해 볼 수 있다. 각각의 이미지뷰에서 그것들을 스케일하는 요구를 줄일 수 있다.
- **가능한 한 이미지뷰를 불투명하게 하자.**  만약 의도적으로 이미지가 투명도를 포함할 경우가 아니라면, `isOpaque`프로퍼티를 `true`  로 해두자. 그러면 투명도 연산을 피할 수있다.

<br>

---

<br>

<br>

### Debuging Issues with Your Image View

<br>

만약 이미지 뷰가 예측한 대로 보여지지 않는다면, 아래의 팁으로 문제를 진단해보자.

- **올바른 메소드로 이미지를 불러오고 있는가?**  `UIImage` 의 `init(named:in:compatible:with:)` 이니셜라이저는 **asset catalog 또는 앱의 번들 에서 이미지들을 불러온다.** 만약 앱 번들의 외부에서 이미지를 불러오려면, `imageWithContentsOfFile:` 메소드를 사용하자.

- **사용자 정의 드로잉을 위해서 이미지뷰들을 사용하지 말자.** `UIImageView `클래스는 그들의 컨텐트를 `draw(:)` 메소드를 이용해서 그리지 않는다.  **오로지 이미지뷰는 이미지를 표현하기 위해 사용해라**.  이미지들과 연관된 드로잉을 하기위해서는, UIView를 직접 상속하고, 그 곳에 이미지를 그려라.

  <br>

---

<br><br>

### Interface Builder Attributes

<br>

|    Attribute    |                          Discussion                          |
| :-------------: | :----------------------------------------------------------: |
|    **Image**    | **디스플레이 하기위한 이미지속성.** Xcode 프로젝트 내의 특정이미지를 명시할 수 있다.   코드상에서 이 속성을 설정하려면, `image` 또는 `animationImages` 프로퍼티를 이용하자. |
| **Highlighted** | **Image view 가 강조되었을 때, 보여줄 이미지에 관한 속성.** 코드상에서 설정하기를 원한다면, `highlightedImage` 또는 `highlightedAnimationImages` 프로퍼티를 이용하자. |
|    **State**    | **이미지의 초기 상태를 나타내는 속성.** 이 속성을 이미지가 강조 또는 비강조를 표현하기위해서 사용하자. 코드상에서 `isHighlighted` 프로퍼티를 이용하자 |



<br>

---

<br>

<br>

### Internationalization

<br>

< 추가 업데이트 예정 >

<br>

---

<br>

<br>

### Accecssibility

<br>

이미지 뷰들은 기본적으로는 접근이 가능하다.  이미지뷰의 기본 접근성 속성은 이미지와 유저와의 상호작용 여부이다.

[Accessibility Programming Guide for iOS](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/iPhoneAccessibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008785). 

<br>

<br>

---

<br>

### State Preservation



이미지 뷰의 `restorationIdentifier` 프로퍼티 값 을 할당할 때, **보여질 이미지의 프레임을 보존하기위한 노력을 해야한다.**  특히,  `bounds, center, transform` 과 같은 뷰의 프로퍼티의 값 과 `anchorPoint` 과 같은 레이어의 본질적인 프로퍼티 들을 보존해야한다. 



---





### Topic



#### Creating an Image View 

- **`init(image: UIImage?)`**  

  - 명시된 이미지로 초기화된 이미지뷰를 메소드

- **`init(image: UIImage? , highlightedImage: UIImage?)` ** 

  -  명시된 보통이미지와 강조된 이미지 들을 초기화된 이미지뷰를 반환하는 메소드 

    <br>

  ---

#### <br>Accessing the Displayed Images 

- **`image: UIImage?`** 

  - 이미지 뷰 내에 보여질 이미지 

- **`highlightedImage: UIImage?`**

  - 이미지 뷰 내에 보여질 강조된 이미지

  ---

<br>

#### Animating a Sequence of Images

- **`animationImages: [UIImage]?`**

  - 애니메이션에 사용할 `UIImage` 객체들의 배열

- **`highlightedAnimationImages: [UIImage]?`**

  - 뷰가 강조된 상태일 때, 애니메이션에서 사용할 `UIImage`의 배열

- **`animationDuration: TimeInterval`**

  - 이미지들의 한번의 순환에 걸리는 시간의 양

- **`animationRepeatCount: Int`**

  - 애니메이션을 반복하기하는 횟수를 명시하는 수

- **`startAnimating()`** 

  - 이미지의 애니메이션을 시작하는 메소드

- **`stopAnimating()`**

  - 이미지의 애니메이션을 중단하는 메소드

- **`isAnimating: Bool`**

  - 애니메이션이 동작하고 있는 지 아닌지를 가리키는 `Boolean` 속성

  ---

<br>

#### Configuring the Image View

- **`isUserInteractionEnableds: Bool`**

  - 유저의 이벤트를 이벤트 큐로 부터 제거하고 무시할 것인가를 결정하는 `Boolean` 값

- **`isHighlighted: Bool`**

  - 이미지가 강조되는 지 아닌지를 표현하는 `Boolean` 값

- **`tintColor: UIColor!`**

  - 뷰 계층안에서 이미지들을 색조를 더하는 데 사용되는 색상

  ---

#### Managing Focus-Related Behaviors

- **`adjustsImageWhenAncestorFocused: Bool`**

  - 상위 뷰가 포커싱 되었을때 이미지뷰가 응답할 수 있게 해주는 속성

- **`focusedFrameGuide: UILayoutGuide`**

  - 이미지뷰가 포커싱될 때 사용되는 레이아웃 가이드

- **`makeFocusEffectToContents: Bool`**

  - 강조된 이미지에서 alpha 값을 사용할 지 유무를 나타내는 `Boolean` 값

  ---

#### Layering Content on Top of the Image View

- **`overlayContentView: UIView`**
  - 이미지 뷰의 위의 층층이 이루어진 컨텐트를 관리하는 뷰