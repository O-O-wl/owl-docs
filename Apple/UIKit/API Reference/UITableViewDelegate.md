

# UITableViewDelegate

---

> 선택 관리, 색션의 해더와 푸터를 설정하고, 셀들을 재정렬, 삭제 그리고 테이블뷰내의 다른 수행을 설정할 수 있는 메소드들
>
> ---

<br>

<br>

### Declaration

<br>

```swift
protocol UITableViewDelegate
```

<br><br>

### OverView

<br>아래의 기능들을 관리기위해서는 이 프로토콜의 메소드를 사용하자.

- 커스텀한 헤더와 푸터 뷰들을 생성하고, 관리하기
- 헤더들, 푸터들 ,로우들의 높이를 사용자 정의로 명시하기
- 더 나은 스크롤링을 위한 높이 예측을 제공하기
- 로우의 컨텐트들을 들여쓰기
- 로우의 선택이 응답하기
- 테이블 로우들의 다른 액션과 스와이프에 응답하기
- 테이블의 컨텐트를 수정하기



테이블 뷰는 `NSIndexPath`  를 사용해서 로우와 색션을 명시한다. 

<br>

---

<br><br>

### Topic

<br>

#### Configuring Rows for the Table View - 테이블 뷰의 로우 설정하기

<br>

- **`func tableView(UITableView: willDisplay: UITableViewCell, forRowAt: IndexPath)`**

  - 델리게이트 객체에게 테이블 뷰가 특정 로우에 셀을 그리기 직전에 통보메소드

- **`func tableView(UITableView, indentationLevelForRowAt: IndexPath) -> Int`**

  - 델리게이트 객체에게 주어진 색션의 로우의 인덴트읠 레벨을 물어보는 메소드

- **`func tableView(UITableView, shouldSpringLoadRowAt: IndexPath, with: UISpringLoadedInteractionContext) -> Bool`**

  - 테이블 안의 로우의 스프링로드 행동을 더 좋게 만드는 메소드

    <br>

---

<br>

<br>

### Responding to Row Selections - 로우의 선택에 응답하기

- **`func tableView(UITableView, wilSelectRowAt: IndexPath) -> IndexPath?`**
  - 특정한 로우가 선택되기 직전에 델리게이트에게 알려주는 메소드 , 반환형으로 다른 인덱스가 선택되게 할 수있다.
- **`func tableView(UITableView, willSelectRowAt: IndexPath: Indexpath)`**
  - 지금 선택된 로우를 델리게이트에게 통보하는 메소드
- **`func tableView(UITableView, willDeselectRowAt: IndexPath) -> IndexPath?`**
  - 선택을 취소하기 직전에 델리게이트에게 알리는 메소드
- **`func tableView(UITableView, didDeselectRowAt: IndexPath)`**
  - 선택을 취소한 직후 델리게이트에게 통보하는 메소드

<br>

---

<br><br>

#### Providing Custom Header and Footer views - 커스텀한 헤더와 푸터 뷰 제공하기



<추후 업데이트 예정>

<br>

---

<br><br>

#### Providing Header, Footer, and Row Heights - 헤더와 푸터, 로우의 높이 제공하기

<추후 업데이트 예정>

<br>

------



<br><br>

#### Estimating Heights for the Table's Content - 테이블의 컨텐트에 대한 높이 어림하기

<추후 업데이트 예정>

<br>

------

<br><br>

#### Managing Accessory Views - 액세사리 뷰 관리하기

<추후 업데이트 예정>

<br>

------

<br><br>

#### Responding to Row Actions - 로우의 액션 응답하기

- **`func tableView(UITableView, leadingSwipeActoinsConfiguratoinForRowAt: IndexPath) -> UISwipeActionsConfiguration?`**
  - 로우의 leading edge에 표시할 스와이프 액션을 반환하는 메서드
- **`func tableView(UITableView, trailingSwipeActionsConfigurationForRowAt: IndexPath) -> UISwipeActionsConfiguration?`**
  - 로우의 trailing edge에 표시할 스와이프 액션을 반환하는 메서드

<br>

------

<br><br>

####  Managing Table View Highlights - 테이블 뷰의 강조 관리하기

<추후 업데이트 예정>

<br>

------

<br><br>

####  Editing Table Rows - 테이블 로우들 수정하기

<추후 업데이트 예정>

<br>

------

<br><br>

####  Reordering Table Rows - 테이블 로우들 재정렬하기

<추후 업데이트 예정>

<br>

------

<br><br>

####  Tracking the Removal of Views - 뷰드르이 제거를 추적하기

<추후 업데이트 예정>

<br>

------

<br><br>

#### Managing Table View Focus - 테이블 뷰의 포커스 관리하기

<추후 업데이트 예정>

<br>

------

<br><br>

#### Instance Methods - 인스턴스 메소드들

<추후 업데이트 예정>

<br>

------

