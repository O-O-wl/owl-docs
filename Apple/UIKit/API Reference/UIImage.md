# UIImage

> 앱 내에서 이미지 데이터를 관리하는 객체



### OverView

- 이미지 객체는 **Immutable**

- 하나의 이미지객체는 하나의 이미지 또는 여러개의 연속적인 이미지를 포함한다. 

  ---

  </br>

  </br>

  ### Creating Image Objects 

  </br>

- **앱의 메인 번들내에 위치한 이미지 에셋과 이미지 파일로 부터 이미지를 만들기**

  ```swift
  init?(named name: String,  
     in bundle: Bundle?, 		 
  compatibleWith traitCollection: UITraitCollection?)
  // 이름이 있는 이미지 에셋을 사용하여 이미지 객체를 만드는 실패가능한 이니셜라이저
  ```

  - `name` -  이미지 에셋의 이름,	png만 확장자를 생략할 수 있다.

  - `bundle` - 이미지 파일 또는 에셋을 포함하고 있는 번들,  `nil` 이면 `main bundle`

  - `traitCollection` - 이미지와 연관된 환경의 특징, `nil` 이면 `main screen` 과 연관된 특징

    </br>

- **초기에 앱의 번들에 존재하지 않고, disk로 부터 load 해서 이미지 만들기**

  ```swift
  init?(contentsOfFile path: String)
  // 파일의 경로로 이미지를 로드해오는 실패가능한 이니셜라이저
  // ❗️매번 디스크에서 이미지를 로드하기 때문에, 반복적으로 같은 이미지의 로드시 사용하지말자.
  ```

  - `path` - 파일의 경로

    </br>

- **여러개의 연속된 이미지로 하나의 이미지 만들기**

- ```swift
  class func animatedImageNamed(_ name: String, 
                       duration: TimeInterval) -> UIImage?
  ```

  - `name` - 이미지 파일 로의 부분적 혹은 전체 경로 , name에 0~1024를 덧붙이고 모든 이미지를 더한다.

     만약 `'image'` 가 `name` 파라미터에 들어오면 `image0`…`image1024` 에 해당하는 파일을 가져오고

     같은 스케일(배율)과 사이즈를 공유해야한다.

  - `duration` - 애니메이션의 기간



***Note***

```
이미지 객체는 immutable 이기 때문에 생성이후에 그들의 프로퍼티를 변경할 수 없다.

이미지객체가 왜 immutable일까?
대부분의 이미지 프로퍼티는 이미지파일이나 이미지데이터에 따라 메타데이터가 설정된다.
```

</br>

##### What is Image asset? 

> 이미지 에셋은 이미지들을 앱과 함께 관리하는 가장 쉬운 방법이다.
>
> 각각의 Xcode 프로젝트는 `asset library`를 포함하고 있으며, 이 곳에는 여러개의 `image set`을 더할 수 있다.
>
> 하나의 `image set` 은 하나의 이미지의 변화와 여러 플랫폼에서의 사용을 고려한 여러형태의 버전을 제공한다.

---





### Defining a Stretchable Image

</br>

> **Stretchable image 란 ?**
>
> 표현할 데이터(이미지)가 미적으로 아름답게 복사될 구역을 정의하는 것이다.
>
> Stretchable image 는 일반적으로는 백그라운드의 공간에 컨텐츠를 늘리거나 줄여서 가득 채우기 위해 사용된다.
>
> </br>
>
> <img width="707" alt="스크린샷 2019-07-28 오후 11 28 51" src="https://user-images.githubusercontent.com/39197978/62159082-df319b00-b34b-11e9-851c-9d7edd56161c.png">
>
> 기존 이미지에 `inset`을 [`resizableImage(withCapInsets:)`](https://developer.apple.com/documentation/uikit/uiimage/1624102-resizableimage) 메소드로 추가해줌으로써 **Stretchable image** 를 정의한다.
>
> `inset`은 두개 또는 그 이상의 부분으로 이미지를 재분할 한다. 위의 그림은 9개로 분할 했다.
>
> `inset`은 신축되지 않을 이미지의 부분이다.
>
> `inset`을 제외한 부분이 늘어나고 줄어듬으로써, 이미지의 신축성을 관리할 수 있다.
>
> <img width="699" alt="스크린샷 2019-07-28 오후 11 28 57" src="https://user-images.githubusercontent.com/39197978/62159084-dfca3180-b34b-11e9-9ccb-4b5fdfe525ea.png">
>
> ---

</br>

</br>

### Comparing Images

</br>

> 캐시된 같은 이미지로 초기화한 두개의 이미지 객체는 서로 다를 것이다.
>
> isEqual 메소드를 사용하면 동등성의 기준을 재정의 할 수 있다.
>
> [isEqual(_:)](https://developer.apple.com/documentation/objectivec/nsobjectprotocol/1418795-isequal)
>
> - 서로 다른 이미지 데이터를 포함하는 지 확일할 수있는 유일한 메소드
> -   [`NSData`](https://developer.apple.com/documentation/foundation/nsdata), [`NSDictionary`](https://developer.apple.com/documentation/foundation/nsdictionary), [`NSArray`](https://developer.apple.com/documentation/foundation/nsarray), [`NSString`](https://developer.apple.com/documentation/foundation/nsstring) 클래스에는 기본적으로 제공
>
> ---

</br>

</br>

### Accessing the Image Data

</br>

> 이미지 객체는 이미지 데이터 자체에 직접 접근하는 방법을 제공하지는 않는다.
>
> 그러나 앱 사용 중에 다른 포맷의 이미지데이터를 가져올 수는 있다.
>
> `ciImage`,`cgImage`  프로퍼티를 이용해 Core Image / CoreGrapic 
>
> [`pngData()`](https://developer.apple.com/documentation/uikit/uiimage/1624096-pngdata) and [`jpegData(compressionQuality:)`](https://developer.apple.com/documentation/uikit/uiimage/1624115-jpegdata) 를 이용해서 



---



< 진행중...>



**Reference by https://developer.apple.com/documentation/uikit/uiimage**







### Loading and Caching Images

- **`init?(named: String, in: Bundle?, compatibleWith: UITraitCollection?)`**

  > 지정된 특징들에 부합하는 이름이 있는 asset 을 사용하는 이미지 객체를 생성한다.

  </br>

  - **Declaration**

  - ```swift
    init?(named name: String, 
       in bundle: Bundle?, 
    compatibleWith traitCollection: UITraitCollection?)
    ```

    - `name`  -  **image asset** 또는 **file** 의 이름을 나타내는 인자, png만 확장자를 생략할 수 있다.

    - `bundle` - **image file** 또는 **image asset** 을 포함하는 번들,  `nil` 이면  **app's main bundle**을 지정한다.

    - `traitCollection` - **이미지 환경에 연관된 특성들**, 이미지의 올바른 변형을 보장하기 위해 사용한다. `nil` 이면 **main screen**에 연관된 특징들을 사용한다.  

      </br>

  - **Discussion**

    >이 메서드는 명시된 `name`을 가진 이미지 객체를 시스템 캐시에서 검사하고 , 명시된  `traitCollection`  에 가장 잘 매칭되는 이미지의 변형을 반환한다.
    >
    >만약 캐시에서 매칭되는 이미지 객체가 존재하지 않는다면, asset catalog 또는 disk 로부터 이미지를 생성한다.
    >
    >시스템은 언제든지 캐시된 이미지 데이터를 메모리에서 해제할 수도 있다.
    >
    > 다만 캐시에는 사용되지 않고 있는 이미지 데이터에 대해서만 해제한다.
    >
    >***자주 사용되지 않은 이미지를 캐시에 저장하지 않기 위해서는  [`imageWithContentsOfFile:`](https://developer.apple.com/documentation/uikit/uiimage/1624123-imagewithcontentsoffile) 와 같은 메소드를 권장한다.***

  

- **`init(imageLiteralResourceName name: String)`**

  > 지정된 자원과 연관된 이미지 객체를 반환하는 메서드

  </br>

  - **Declaration**

  - ```swift
    required convenience init(imageLiteralResourceName name: String)
    ```

    - `imageLiteralResourceName` - image asset 또는 file 의 이름

      <br/><br/>

---

<br/>

### Creating and Initializing Image Objects



****