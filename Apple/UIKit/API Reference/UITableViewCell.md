# UITableViewCell

 https://developer.apple.com/documentation/uikit/uitableviewcell



---

>  테이블 뷰내의 하나의 로우의 시작적인 표현
>
> ---

<br>

<br>



### Declaration

---

```swift
class UITableViewCell: UIView
```

<br>

<br>



---

<br>

### OverView

---

`UITableViewCell` 객체는 하나의 테이블 로우의 컨텐트를 관리하는 뷰의 타입이다.

셀들은 주로 앱의 커스텀한 컨텐트를 표시하고, 조직화하기 위해 사용한다.

그러나 `UITabvleViewCell`은 테이블과 관련된 행동을 위해서 몇몇 특별한 커스터마이징을 제공한다 .

- 셀에 선택 또는 강조색상을 적용한다.
- 셀을 수정가능한 상태에 진입시킨다.
-  표준 Accessory view 를 더한다.
- 테이블안의 계층을 만들기 위해서 셀들의 컨텐트를 인덴팅한다.



앱의 컨텐트는 셀의 구역의 대부분을 차지한다. 그러나 셀은 다른 컨텐트 공간을 만들기 위해 해당 공간을 조정할 수 있다. 셀들은 **trailing edge에 액세사리뷰**를 보여준다. **수정 모드**에 진입 시, 셀은 **leading edge에 삭제를 제어하는 컨트롤**이 더해진다. 그리고 추가로 **Accessory view 는 정렬 컨트롤**로 변한다. 

![a27538d0-bc9a-4972-aa83-8616889d7959](https://user-images.githubusercontent.com/39197978/62724329-b72df000-ba4d-11e9-8ac1-279ff1692727.png)



모든 테이블 뷰는 적어도 하나의 컨텐트를 보여줄 셀의 타입을 가져야한다.

그리고 테이블들은 다른 타입의 컨텐트를 보여줄 수 있는 다중의 셀 타입을 가질 수 있다.

테이블의 데이터 소스 객체는 스크린에 나타나기 직전에 , 셀들의 설정과 생성을 핸들링한다.

<br>

---

<br>

<br>

### Configuring Your Cell's Content - 셀의 컨텐트 설정하기

---

스토리보드안의 셀의 레이아웃과 컨텐트를 설정하자. 기본적으로 테이블은 하나의 셀 타입을 가지고 있다.

그러나 테이블의 프로토타입 셀의 속성을 바꿈으로써 추가로 더할 수 있다. 

셀의 컨텐트를 설정하는 것 이외에도, 아래의 사항을 설정할 수 있다.

- **Identifier**  - 셀을 생성하기 위해서 또는 재사용하기 위해 식별자를 사용하자
- **Style** - 사용자 정의 샐을 정의하거나 표준 타입을 선택하자
- **Class** - UITableViewCell을 서브 클래싱하여 사용자 정의 행동을 명시하자



기본 표준 타입의 경우,  UITableViewCell은 컨텐트를 보여주기 위해서 뷰들을 제공한다.

표현하기 위해서는 셀의  프로퍼티`textLabel ` , `detailTextlabel`,`imageView`  의 값을 할당해야  한다.

아래의 그림으로 셀의 컨텐트 영역안에 어떤 값이 위치되어있는 지 볼 수 있다.

만약 셀에서 이미지를 공급하지 않는 다면, 셀은 남는 공간을 다른 컨텐트 뷰들을 위해서 분배한다 .







<br>

![63b0c95b-bf2f-4798-9cca-8a5e77631679](https://user-images.githubusercontent.com/39197978/62724862-e55fff80-ba4e-11e9-8d3c-2c944b42c2a9.png)



---

<br>

<br>



## Topic

---

<br>

#### Creating a Table View Cell - 테이블 뷰 셀 생성하기

<br>

- **`init(style: UITableViewCell.CellStyle, reuseIdentifier: String?)`**

  - 스타일과 재사용 식별자를 이용하여 셀을 초기화하고 반환한다.

    <br>

- **`enum UITableViewCell.CellStyle`**

  - 셀의 다양한 스타일에 대한 열거형

- **`init?(coder: NSCoder)`**

---

<br>

<br>

#### Reusing Cells - 셀들 재사용하기

- **`var reuseIdentifier: String?`**
  - 재사용가능한 셀의 식별자로 사용되는 문자열
- **`func prepareForReuse()`**
  - 테이블 뷰의 델리게이트로 부터 재사용을 위해 재사용가능한 셀을 준비하는 메서드
  - **데이터소스의 `dequeueReuseableCell() `을 호출 시 해당 셀의 `prepareForReuse() `메소드가 호출된다.**  

<br>

---

<br>

<br>

####  Specifying Content for Standard Cell Styles - 표준 셀의 스타일의 컨텐트 명시하기

---

- **`var textLabel: UILabel?`**
  - 테이블 셀의 메인 텍스트로 사용되는 레이블
- **`var detailTextLabel: UILabel?`**
  - 만약 존재한다면, 두번째 레이블
- **`var ImageView: UIImageView?`**
  - 테이블 셀의 이미지 뷰

<br>

------

<br>

<br>

#### Accessing Views of the Cell Object - 셀 객체의 뷰들에 접근하기

---

- **`var contentView: UIView`**
  - 셀 객체의 컨텐트 뷰
- **`var backgroundViewL UIVIew?`**
  - 셀의 배경으로써 사용되는 뷰
- **`var selectedBackgroundView: UIView?`**
  - 셀이 선택되었을 때, 배경으로써 사용되는 뷰
- **`var multipleSelectionBackgroundView: UIView?`**
  - 여러개의 로우를 선택할 수 있을 때, 선택된 셀에 사용될 배경 뷰

<br>

------

<br>

<br>

#### Managing Accessory Views - 액세사리 뷰 관리하기



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Managing Cell Selection and Highlighting - 셀의 선택과 강조 관리하기

<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Editing the Cell - 셀 수정하기

---

- **`var isEditing: Bool`**
  - 셀이 수정가능한 상태인지 표시하는 Boolean 값
- **`func setEditing(Bool, animated: Bool)`**
  - 수정모드의 진입과 종료를 바꾸는 메소드
- **`var editingStyle: UITableViewCell.EditingStyle`**
  - 셀의 수정모드 스타일
- **`enum UITableViewCell.EditingStyle`**
  - 셀에서 사용되는 수정 스타일
- **`var showingDeleteConfirmation: Bool`**
  - 셀이 현재 삭제 확인 버튼이 보여지는 지 표시하는 Boolean 값
- **`var showsReorderControl: Bool`**
  - 재정렬 컨트롤을 셀이 보여줄 것인지 결정하는 Boolean 값

<br>

------

<br>

<br>

#### Dragging the Row - 로우 드래그 하기



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Adjusting to State Transitions - 상태 변경 적용하기

---

- **`func willTransition(to: UITableViewCell.StateMask)`**
  - **새로운 셀의 상태로 막 변하려고할 때** 셀에게 알리기 위해 호출되는 메서드
- **`func didTransition(to: UITableViewCell.StateMask)`**
  - **새로운 셀의 상태로 변경되어진 다음**에 셀에게 알리기 위해 호출되는 메서드

<br>

- **`struct UITableViewCell.StateMask`**
  - 셀의 새로운 상태를 결정하는 상수

<br>

------

<br>

<br>

#### Managing Content Indentation - 컨텐트의 굴곡 관리하기

- 



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Managing Focus - 포커스 관리하기



<추후 업데이트 예정>

<br>

------

<br>

<br>

#### Enumerations -열거하기



<추후 업데이트 예정>

<br>

------

