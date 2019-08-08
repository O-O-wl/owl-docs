

# Table View Programming Guide for iOS



## OverView of the Table View API

---

테이블 뷰 프로그래밍 인터페이스는 몇몇의 UIKit의 클래스들을 포함한다.

<br>

<br>

### Table View

---

테이블 뷰는 `UITableView`의 인스턴스이다. **테이블 뷰의 외형을 설정하기위해서  메서드들을 사용하자.** 예를 들어, 기본 로우의 높이를 명시하거나, 테이블의 해더로써 사용할 서브뷰를 제공할수 있다.  특정한 로우와 셀뿐만아니라 현재 선택된 로우에 접근하는 메소드들도 제공한다

추가로, **테이블뷰의 선택과 스크롤링, 그리고 섹션과 로우의 삽입과 삭제를 관리**하는 UITableView 다른 메서드를 호출할 수있다.

<br>

UITableView는 **윈도우의 사이즈보다 더 긴 컨텐트를 관리하는 스크롤과 관련된 행동을 정의하는 UIScrollview클래스**를 상속한다. 

UITableView 는 스크롤에 관련된 행동을 수직적인 스크롤링만을 위한 행동으로 재정의한다.

<br>

<br>

### Table View Controller

---

​	

`UITableViewController` 클래스는 테이블뷰를 관리한다. 

그리고 몇가지 테이블과 관련된 행동을 지원한다.

- 선택 관리
- 로우의 수정
- 테이블 설정



이 다양한 지원들은 테이블에 기반을 둔 인터페이스를 초기화하고 생성하기위해서 써야할 코드의 양을 최소화 할 수있다.

직접 클래스를 사용하는 것을 삼가하자.

 대신 UITableViewController를 상속하고 추가행동을 정의하자.

<br><br>

### Data Source and Delegate

---

**Data Source - 데이터모델과 뷰 중재**

**Delegate - 뷰의 출현 과 행동(수정, 선택, 삭제) 관리**

<br>

하나의`UITableView` 객체는 하나의 델리게이트와 하나의 데이터소스를 가져아한다. 

MVC 디자인 패턴에 따르면, **데이터 소스는 앱의 데이터 모델과 테이블 뷰 사이에서 중재**한다.

반면에 델리게이트는 테이블 뷰의 등장과 행동을 관리한다.

데이터 소스와 델리게이트는 많은 경우에 같은 객체이다.

그리고 그 객체는 대개 `UITavbleViewController`의 서브 클래스이다.

**DataSource 객체**는 는 `UITableViewDataSource`  를 채택하고 필수 구현 메소드를 2개 가지고 있다. `tableView(tableView:numOfRowsInSection)` 하나의 섹션에 몇개의 테이블 로우가 표현될 지 알려주는 메소드이다. `tableView(tableView:cellForRowAtIndexPath)` 는 테이블안의 각각의 로우에 표시할 셀들을 제공하는 메소드이다.

여러개의 색션을 설정하기 위한 메소드 , 해더 또는 푸터를 제공하기 위한 필수가 아닌 메서드, 그리고 테이블 로우의 재정렬, 삭제,추가를 지원하는 메서드들이 존재한다.

<br>

**Delegate 객체**는 는 `UITableViewDelegate` 프로토콜을 채택한다.

 이 프로토콜은 필수 구현 메소드는 따로 가지지 않는다. 테이블뷰의 시각적인 요소를 수정, 셀의 선택을 관리, 액세사리뷰에 대한 지원 ,  테이블 안의 각각의 로우들의 수정 델리게이트에 할당하자. 

<br>

앱은 비필수 클래스 `UILocalizedIndexedCollation` 의 사용으로 데이터소스를 조직화하고 인덱스 되어진 리스트들과 인덱스 되어진 아이템을 탭하였을떄 적절한 섹션을 표시할 수 있습니다.

<br>

<br>

### Extension to the NSIndexPath Class

---

많은 테이블 뷰의 메소드들을  **index path**들을 인자로써 또는 반환 값으로 사용한다.

하나의 index path는 **중첩된 배열들의 내부의 트리의 노드의 경로**를 식별한다.

그리고 이 객체를 **Foundation** 의 `NSIndexPath` 객체로써 표현한다.

<br>

<br>

### Table View Cells

---

데이터 소스는 각각의 시각적인 로우를 위해서 셀 객체를 반환해야한다. 

그 셀 객체들은  **셀의 수정과 선택, 액세사리 뷰, 그리고 셀의 설정을 관리하는 메소드를 포함하는 UITableViewCell**을 상속해야한다.

기본적으로 하나 또는 두개의 텍스트를 구성하는  `UITableViewCell`  클래스의 객체를 생성 할 수 있다. 대신 표준 스타일을 사용하는 대신 커스텀 서브뷰를 더할 수 도 있다. 

