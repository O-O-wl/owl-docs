

# UITableView

https://developer.apple.com/documentation/uikit/uitableview

---

> 하나의 칼럼안에 정렬된 로우들을 사용해서 데이터를 표현하는 뷰

---

<br>

### Declaration

---

```swift
class UITableView: UIScrollView
```

<br>

<br>

### OverView

---

iOS에서의  테이블 뷰는 **수직**으로 스크롤하는 **컨텐트를 로우들로 나누어서 하나의 칼럼으로 디스플레이**한다.

테이블 내의 각각의 로우들은 앱 컨텐트의 하나의 조각을 포함하고 있다.

 *예를 들어, 연락처 앱은 분리된 하나의 로우안에 각각의 컨텐트들의 이름이 표시한다. 그리고 설정 앱의 메인 페이지에서는 설정의 그룹들을 표시한다.* 

개발자는 하나의 긴 로우들의 리스트를 화면에 보여주는 방법을 설정할 수 있다.

또는 관련된 로우들을  `section`  이라는 그룹으로 묶어 컨텐트를 쉽게 안내할 수 있다.

 



![1eb44f8d-1907-4949-9208-f2fb7f3ffd1b](https://user-images.githubusercontent.com/39197978/62716014-80031300-ba3c-11e9-983c-f28d10867471.png)



테이블은 앱의 데이터를 더 계층적으로 표시하거나, 잘 구조화되게 표현하기 위해서 사용되어진다.

**계층적인 데이터를 포함하고 앱**들은 테이블을 **navigation view controller** 와 함께 사용한다.

*예를 들어 설정 앱은 테이블과 네비게이션 컨트롤러를 시스템의 설정들을 조직화하기 위해 사용한다.*



`UITableView`는 테이블의 기본적인 외형를 관리한다. 그러나 앱은 실제 컨텐트를 보여주는 셀(`UITableViewCell`)들을 제공한다. 표준 셀 설정들은 이미지와 텍스트의 간단한 조합으로 설정한다. 그러나 필요에 따라 커스텀 셀을 정의할 수있다.

또 **헤더와 푸터 뷰를 추가적인 셀의 그룹에 대한 정보를 제공**하기 위해서 추가할 수있다.



<br>

<br>

---

<br>

### Adding a Table View to Your Interface

---

테이블 뷰를 앱의 인터페이스에 추가하기 위해서는 Table View Controller 를 스토리보드에 드래그하면 된다.

Xcode는 뷰 컨트롤러와 테이블 뷰 를 둘 다 포함하는 씬을 하나 생성한다. 

<br>

테이블 뷰들은 **타인에 의해 주도되어지는 데이터**이다. 일반적으로 그들의 데이터를 **데이터 소스** 역할을 하는 객체에게서 가져와서 사용한다.  **데이터 소스 객체는 앱의 데이터를 관리하고, 테이블의 셀을 생성하고 설정하는 책임을 가진다.** 

만약 앱의 테이블이 절대 변하지 않는 컨텐트라면, 컨텐트를 스토리보드 파일에서 대신 설정할 수있다.

*🦉 - 동적인 데이터를 표현하는 셀은, 스토리보드로 표현할 수 없고, 코드로 표현해야한다.* 

<br>

---

<br>

<br>

### Saving and Restoring the Table's Current State

---

테이블 뷰 들은 UIKit 앱의 복구를 지원한다. 테이블의 데이터를 저장하고 다시 복구하기 위해서는, 테이블 뷰의 `restorationIdentifier` 프로퍼티에 값을 할당 해야한다.  테이블 뷰를 표현해주는 뷰를 저장할 때, 테이블 뷰도 자동적으로 현재 선택되고 보여지는 로우들의 `IndexPath` 를 저장한다.  만약 테이블의 데이터 소스 객체가 `UIDataSourceModelAssociation` 프로토콜을 채택했다면, `IndexPath`대신에 개발자가 제공하는 ID를 저장한다.

<br>

---

<br>

<br>

### Topic

---

<br>

<br>

#### Creating a Table View - 테이블 뷰 생성하기

---

- **`init(frame: CGRect, style: UITableView.Style)`**
  - 주어진 frame 과 style 을 가지는 테이블 뷰를 생성하고 반환한다.
- **`init?(coder: NSCoder)`**

---

<br>

<br>

#### Providing the Table's Data and Cells - 테이블의 데이터와 셀들 제공하기

---

- **`var dataSource: UITableViewDataSource?`**
  - 테이블 뷰의 데이터 소스로서 역할을 하는 객체
- **`var prefetchDataSource: UITableViewDataSourcePrefetching?`**
  - 선인출 데이터 소스로써 역할을 하는 객체
  - 곧 있을 셀의 데이터 요구사항을 Noficication으로 받는 객체

<br>



- **`protocol  UITableViewDataSource`**
  - 데이터를 관리하고 테이블 뷰에 데이터를 제공하는 객체로서 채택되는 메서드들
- **`protocol UITavleViewDataSourcePrefetching`**
  - 테이블 뷰의 데이터 요구를 이전 경고를 제공하는 프로토콜
  - 이 프로토콜은 잠재적으로 연산이 오래걸리는 데이터를 일찍이 시작할 수있게 해줍니다. - *미리 데이터소스연산을 하는 듯?*

---

<br>

<br>

#### Recycling Table View Cells - 테이블 셀들 재사용하기 

---

- **`func register(UINib?, forCellReuseIdentifier: String)`**

  - 명시된 식별자로 테이블뷰와 셀을 포함하는 nib 객체를 등록하는 메서드

- **`func register(AnyClass?, forCellReuseIdentifier: String)`**

  - 새로운 테이블 셀들을 생성하기 위해 사용할 클래스를 등록하는 메서드

  <br>

- **`func dequeueReuseableCell(withIdentifier: String, for: IndexPath) -> UITableViewCell`**

  - 명시된 Reuse ID 에 대한 재 사용가능한 테이블 뷰 셀 객체를 테이블에 더하는 메서드 

- **`func dequeueReuseableCell(withIdentifier: String) -> UITableViewCell?`**

  - 특정 식별자로 지정된 재사용 가능한 테이블뷰 셀을 반환하는 메서드

---

<br>

<br>

#### Recycling Section Headers and Footers - 색션의 헤더와 푸터 재사용하기

---

- **`func register(UINib?, forHeaderFooterViewReuseIdentifier: String)`**
  - 명시된 식별자의 테이블 뷰와 헤더(머리글) 또는 푸터(바닥글) 를포함하는 nib객체를 등록하는 메서드
- **`func register(AnyClass?, forHeaderFooterViewReuseIdentifier: String)`**
  - 새로운 헤더 또는 푸터뷰 를 생성하기위해 사용하는 클래스를 등록하는 메서드
- **`func dequeueReuseableHeaderFooterView(withIdentifier: Stirng) -> UITableViewHeaderFooterView?`**
  - 식별자로 지정된 재사용 가능한 헤더 또는 푸터 뷰를 반환하는 메서드

---

<br>

<br>

#### Managing Interactions with the Table - 테이블 관리 상호작용하기

---

- **`var delegate: UITableViewDelegate?`**

  - 테이블 뷰의 델리게이트로써 역할 하는 객체

  <br>

- **`protocol UITableViewDelegate`**

  - 섹션 해더와 푸터를 설정하고, 셀들을 재정렬하고, 삭제하고, 테이블 뷰의 다른 액션들을 수행하는 메소드들

---



<br>

#### Customizing the Separator Appearance -  테이블 분리공간(섹션) 커스터마이징하기

---

- **`separatorStyle: UITabelViewCell.SeparatorStyle`**
  - 해당 테이블 셀들에 대해 구분자로써 사용되는 스타일

<br>

- **`enum UITableViewCell.SeparatorStyle`**
  - 셀들에 대해 구분자로써 사용되는 스타일
- **`var separatorColor: UIColor?`**
  - 테이블 뷰 내의 로우들의 구분자의 색상
- **`var separatorEffect: UIVisualEffect?`**
  - 테이블 구분자에 적용되는 효과
- **`var separatorInsetReference: UITableView.SeparatorInsetReference`**
  - 어떻게 구분자 inset이 해석될지를 가리키는 객체

<br>

- **`enum UITableView.SeparatorInsetReference`**
  - 테이블 뷰의 구분자의 inset을 해석하는 방법을 가리키는 상수



---

<br>

<br>

#### Getting the Number of Rows and Sections - 섹션과 로우들의 수를 가져오기

---

<추후 업데이트 예정>

---

<br>

<br>

#### Getting Cells and Section-Based Views - 뷰들을 기반한 섹션과 셀들 가져오기

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Selecting Rows - 로우 선택하기

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Inserting, Deleting, and Moving Rows and Sections - 로우들과 섹션들의 삽입, 삭제, 이동

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Performing Batch Updates to Rows and Sections - 로우들과 섹션들의 단체 업데이트 수행하기

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Configuring the Table Index - 테이블 인덱스 설정하기

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Reloading the Table View - 테이블 뷰 다시 불러오기



<추후 업데이트 예정>

<br>

------

<br>

<br>

####Managing Drag Interacions - 드래그 상호작용 관리하기 

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Managing Drop Interactions - 드롭 상호작용 관리하기

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Scrolling the Table View - 테이블 뷰  스크롤링 하기



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Scrolling the Table View - 테이블 뷰  스크롤링 하기



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Putting the Table into Edit Mode - 테이블을 수정모드로 진입시키기



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Getting the Drawing Areas for the Table - 테이블에 대한 도면 가져오기



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Remembering the Last Focused Cell - 마지막으로 포커싱된 셀 기억하기



<추후 업데이트 예정>

<br>

------

