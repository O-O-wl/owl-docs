# View Controller Programming Guide for iOS



[Using Segues - 세그웨이 사용하기](#-Using-Segues)



## Using Segues
---

앱의 인터페이스의 흐름을 정의하기위해서 세그웨이를 사용하자.

세그웨이는 스토리보드 파일안에 두개의 뷰 컨트롤러간의 전환을 정의하는 방법이다.

세그웨이의 시작 지점은 **button, table row, gesture recognizier**이 있다.

세그웨이의 끝나는 지점은 우리가 표시하길 원하는 뷰 컨트롤러이다.

세그웨이는 항상 새로운 뷰 컨트롤러를 보여준다. 그러나 뷰 컨트롤러를  없애기 위해서는 unwind segue 를 사용할 수있다. 

<img width="490" alt="segue_defined_2x" src="https://user-images.githubusercontent.com/39197978/62738008-30d4d680-ba6c-11e9-95b1-966d7ec3c7c8.png">



코드상에서, 세그웨이를 발생시키는 발생시킬 필요가 없다.

런타임에서, UIKit은 세그웨이와 연관된 뷰 컨트롤러를 로드한다. 그리고 그 뷰 컨트롤러를 해당하는 요소에 연결한다. 유저가 요소와 상호작용 할 때, UIKit은 적절한 뷰 컨트롤러를 로드하고, 세그웨이가 발생하기 직전에 앱에 알려준다. 그리고 전환을 실행한다. 

UIKit이 보낸 Notification을 사용해서, 데이터를 새로운 뷰 컨트롤러에게 전달하거나, 세그웨이가 일어나지 않게 막을 수 있다.



#### Creating a Segue Between View Controllers

<br>

같은 스토리보드의 파일 안에서 뷰 컨트롤러들간의 세그웨이를 만들기 위해서는, Control + Click 해서 첫번째 요소에서 해당 목표 뷰 컨트롤러로 드래그하자.

**세그웨이의 시작지점은 뷰 또는 액션이 정의된 객체 (UIControl 과 같은)여야한다.**  

세그웨이를 셀 기반의 뷰들에도 만들수 있다. 아래의 그림은 테이블의 로우가 탭 되었을 때, 새로운 뷰 컨트롤러를 보여주는 세그위에를 생성하는 방법을 보여준다.

<img width="515" alt="segue_creating_2x" src="https://user-images.githubusercontent.com/39197978/62738641-c329aa00-ba6d-11e9-8238-eda3a81ac630.png">





마우스 버튼을 놓았을 때, 인터페이스 빌더는 두개의 뷰 컨트롤러 사이 생성하기를 원하는 관계의 타입을 보여준다. 원하는 전환의 타입을 선택하면 된다.

<img width="124" alt="segue_creating_relationships_2x" src="https://user-images.githubusercontent.com/39197978/62738802-29163180-ba6e-11e9-9ad8-d0ee4668c878.png">

세그웨이의 관계타입을 선택할 때, 가능한 한 adaptive 세그웨이를 선택하자.

Adaptive 세그웨이는 그들의 행동을 현재 환경을 기반으로 자동으로 조정한다.

예를 들어, **Show**세그웨이의 행동은 presenting View Controller를 기반으로 행동이 변한다. 



#### Adaptive Segue Types

|        Segue Type         |                           Behavior                           |
| :-----------------------: | :----------------------------------------------------------: |
|      **Show (Push)**      | 해당 세그웨이는 타겟 뷰 컨트롤러의` show(_ vc: UIViewController, sender: Any?)   `  메소드를 사용해서 새로운 컨텐트를 보여준다. **대부분의 뷰 컨트롤러 경우,  이 세그웨이는 기존  뷰 컨트롤러의 위로 모달하게 새로운 컨텐트를 보여준다.** 몇몇의 뷰 컨트롤러들은 특별하게 메소드를 재정의하고, 다른 행동을 하게 구현한다. 예를 들어, **navigation controller 는 새로운 뷰 컨트롤러를 navigation stack 에 push 한다.**  **UIKit은 source view controller를 알아내기 위해**서  `targetViewController(forAction action: Selector, sender: Any?) -> UIViewController?` 메소드를 이용한다. |
| **Show Detail (Replace)** | 해당 세그웨이는  새로운 컨텐트를 타겟 뷰 컨트롤러의  `showDetail(_ vc: UIViewController, sender: Any?)`  메소드를 사용해서 보여준다.  이 세그웨이는 단지 `UISplitViewController`  를 내장하고 있는 뷰 컨트롤러와만 관계 있다. 이 세그웨이는 split view controller 를 그 새로운 컨텐트를 가진 자식 뷰 컨트롤러로 대치시킨다. 대부분의 다른  뷰컨트롤러들은 새로운 컨텐트를 모달하게 보여준다. |
|    **Present Modally**    | 해당 세그웨이는 특정한 전환과 프레젠테이션 스타일로 모달하게 뷰 컨트롤러를 보여준다. |
|  **Present as Popover**   | 수평으로된 적당한 크기의 환경에서 뷰 컨트롤러는 popover안에 나타난다. 수평이고 작은 환경에서는 전체화면에 모달하게 프레젠테이션된다. |



세그웨이를 만든 이후, **attributes inspector를 이용해서 세그웨이 객체를 선택하고 Identifier를 할당하자**.

세그웨이 동안에,  특히 여러 개의 세그웨이들을 지원하는 뷰 컨트롤러의 경우 Identifier를 사용해서 유용하게, 발동하는 세그웨이를 결정할 수있다.  **Identifier는 세그웨이가 수행될 때 인자로 전달되는  UIStoryBoardSegue 객체 내에 포함되어 있다.**  

<br><br>

#### Modifying a Segue's Behavior at Runtime

<br>

아래의 그림은 세그웨이가 발생할 떄 어떤 일이 일어나는 지를 보여준다.  **대부분의 동작은 `presenting view controller` 내에서  일어난다.** 새로운 뷰 컨트롤러의 설정은 뷰 컨트롤러를 스스로 만들고 보여줄 때와 같은 프로세스를 따른다. 세그웨이는 스토리로부터 설정되기 때문에 , 세그웨이에 관련된 두개의 뷰 컨트롤러가 같은 스토리보드안에 존재해야한다.

![VCPG_displaying-view-controller-using-segue_9-4_2x](https://user-images.githubusercontent.com/39197978/62740353-94620280-ba72-11e9-9e2e-2f26a93b7389.png) 

<br>



```
🦉
1. 현재 뷰 컨트롤러에서 세그웨이 발생
2. 세그웨이의 id와 sender를 인자로 받고, 세그웨이가 진행될 지 여부를 물어본다.
3. UIStoryBoard와의 상호작용으로 Viewcontroller 를 생성한다.
 3-1. UIStoryBoard 는 nib 또는 스토리보드 파일로 아카이빙되어있는 view controller를 초기화한다.
4. prepare 메소드로 준비를 한다. -> 이 과정에서 새로운 뷰 컨트롤러의 프로퍼티를 설정한다.
5. 새로운 뷰 컨트롤러를 보여준다.
```

<br>세그웨이가 일어나는 동안, UIkit은 세그웨이의 결과에 영향을 줄 기회를 주기 위해, 현재 뷰컨트롤러의 메소드를 호출한다.



- `shouldPerformSegue(withIdentifier: String, sender: Any?) -> Bool`  메소드는 **세그웨이를 일어나지 않게 할 기회를 준다.** 

- `prepare(for segue: UIStoryBoardSegue, sender: Any?) `메소드는 **source view controller**가 데이터를 **destination view controller**로 전달하게 해준다.  `UIStoryBoardSegue` 객체는 destination 을 전달한다.

   <br><br>

  

#### Creating an Unwind Segue

<br>

**Unwind segue** 는 현재 보여지고있는 뷰 컨트롤러**들**을 dissmiss 하게 해준다.  **Unwind segue를 인터페이스 빌더에서 적절한 객체를 현재 뷰 컨트롤러의 `Exit 객체`에 연결시킴으로써 생성할 수 있다.** 

 유저가 버튼을 누르거나, 적절한 객체와 상호작용을 할 때, UIkit은 뷰 컨트롤러의 계층에서 unwind segue 를 핸들링할 수있는 객체를 찾는다. **그 후 현재 뷰 컨트롤러가 dissmiss 되고 unwind의 타겟 중간에 있는 뷰 컨트롤러들을 모두 dissmiss 한다.**

<br>

##### To create an unwind segue

1. unwind 이후 보여줄 뷰 컨트롤러를 선택한다.
2. 선택한 뷰 컨트롤러안에 action 메소드를 정의한다.
   - `@IBAction func myunwind(unwindSegue: UIStoryboardSegue)` 

3. Unwind 를 시작할 뷰 컨트롤러로 간다.
4. 해당 뷰 컨트롤러에서 unwind 를 시작하기 위한 트리거 객체(UIControl) 정한다.
5. 정한 트리거 객체를 드래그해서 Exit 객체에 올려 놓는다.

<img width="213" alt="segue_unwind_linking_2x" src="https://user-images.githubusercontent.com/39197978/62741354-8792de00-ba75-11e9-9928-8f4860e4eb70.png">

6. 관계 패널로 부터 방금 정의한 unwind 메소드를 선택한다. 







#### Initiating a Segue Programmatically

<br>

세그웨이는 스토리보드 파일안에서 생성한 연결로 인해 발생한다.

그러나 스토리보드에 세그웨이를 생성할 수 없을 때( 예를 들어 도착할 뷰 컨트롤러를 아직 스토리보드에서는 생성하지 않을 때) 가 있다.

이런 경우에는 `perfoemSegue(with identifier: String, sender: Any?)`  메소드를 현재 뷰 컨트롤러에 코드로 구현해서 발생시킬 수 있다.



<br>

<br>

#### Creating a Custom Segue

<br>

인터페이스 빌더는 하나의 뷰 컨트롤러에서 다른 뷰 컨트롤러의 전환하는 방법을 제공한다. 그러나 , 만약 그 세그웨이들중 하나가 원하는 대로 동작하지 않는다면, 커스텀한 세그웨이를 만들어야한다.

<br>

<br>

#### The Life Cycle of a Segue

<br>

커스텀한 세그웨이의 동작을 이해하기 위해서, 세그웨이 객체의 생명주기를 이해할 필요가 있다. 세그웨이 객체는 `UIStoryboardSegue` 클래스의 인스턴스이다.

앱은 세그웨이 객체를 직접 생성하지는 않는다. UIKit는 세그웨이 객체를 세그웨이가 발생했을 때 생성된다.

1. **화면에 뜰 뷰 컨트롤러는 생성되고 초기화 된다.**
2. **세그웨이의 초기화 메소드가 호출되고, 세그웨이 객체가 생성된다.**
3. 화면에 떠있는 뷰 컨트롤러의  `prepare(for segue:sender:)`가 호출된다. 
4. 세그웨이 객체의 `perform()`메소드가 호출된다. 이 메소드는 새로운 뷰컨트롤러를 스크린에 보여주는 전환을 수행한다.
5. 세그웨이 객체가 메모리에서 반환된다.

```
🦉
세그웨이 객체는 트리거가 눌렸을 때 바로 생기지 않고, 
shouldPerform() 메소드로 세그웨이 전환이 있을 지 확인을 한뒤
화면에 뜰 뷰컨트롤러가 생성된 이후에 생성된다.
```

<br><br>

