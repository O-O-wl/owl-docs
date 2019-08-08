# UITableViewDataSource

https://developer.apple.com/documentation/uikit/uitableviewdatasource

---

> 테이블 뷰의 셀들을 제공하고,  데이터를 관리하기 위해 사용하는 객체로써 채택되야하는 메소드들
>
> ---



<br>

<br>

### Declaration

```swift
protocol UITableViewDataSource
```

<br><br>

### Overview

<br>

테이블 뷰들은 단지 그들의 **데이터의 시각화만을 관리**한다. 그들은 데이터 그 자체를 관리하지는 않는다.

데이터를 관리하기 위해서, 테이블에 Data Source 객체를 제공해야한다.

이 객체는 `UITableViewDataSource` 프로토콜의 구현체이다.  **Data Source 객체는 테이블로 부터 받은 데이터와 연관된 요청에 응답한다.** 또한 테이블의 데이터를 직접 관리한다. 

또 다른 데이터 소스의 책임은 아래와 같다.

- 테이블안의 로우들과 색션의 수를 보고한다.
- 테이블의 각각의 로우에 대한 셀을 제공한다.
- 색션 헤더들과 푸터들의 타이틀을 제공한다.
- 테이블의 인덱스를 설정한다.
- 본질적인 **데이터의 변화가 요구되는 유저또는 테이블로 부터 시작된 업데이트에 대해 응답**한다.



2개의 필수 구현 메소드가 있고 아래에 예시 코드를 보면 된다.

```swift
// 함수의 인자로 들어온 색션의 로우의 수를 반환한다.
override func tableView(_ tableView: UITableView, numberOfRowsInSection section:Int) -> Int {
  return 0
}

// 각각의 로우에 해당하는 Cell 객체를 반환한다.
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  // 적절한 타입의 셀을 가져온다.
  let cell = tableView.dequeueReuseableCell(withIdentifier: "myCell", for: indexPath)
 
  // 셀의 컨텐트를 설정한다.
  cell.textLabel!.text = "나의 테이블 셀"
  
  return cell
}
```



테이블의 기능을 가능하게 해주는 프로토콜의 다른 메소드들을 사용하자 .

예를 들어 *swipe-to-delete* 기능을 추가하고 싶다면, `tableView(_:commit:forRowAt:)`  을 구현하자.

<br>

---

<br>

<br>

### Specifying the Location of Rows and Sections

<br>

테이블 뷰들은 `NSIndexPath` 객체들의 `row`,`section` 프로퍼티들을 사용하여 셀의 위치를 전달한다.

로우와 섹션 인덱스들은  0을 시작으로 한다. 

로우를 완전히 유일하게 식별하기 위해서는 `section`,`row` 를 모두 필요로 한다는 것을 의미한다.

만약 섹션을 가지지 않는 다면, row의 값만 필요로 하긴 할 것이다.

![95595fc6-198c-415e-82b7-6af8b9edc396](https://user-images.githubusercontent.com/39197978/62734556-447c3f00-ba64-11e9-9a7b-b8f350e575f6.png)



---

<br>

<br>

### Topics

---

<br>

#### Providing the Number of Rows and Sections - 로우와 섹션의 수를 제공하기

- **`@required func tableView(UITableView, numberOfSectionRowsInSection: Int) -> Int`**

  - 데이터 소스 객체에 테이블 뷰의 **주어진 섹션의 로우의 갯수**를  물어보는 메소드

- **`func numberofsections(in: UITableView) -> Int`**

  - 데이터 소스에게 테이블 뷰의 섹션의 수를 요청하는 메소드

  <br>

---

<br><br>

#### Providing Cells, Headers, and Footers - 헤더, 푸터, 셀 제공하기

- **`@required func tableView(UITableView, cellForRowAt: IndexPath) -> UITableViewCell`**
  - 테이블 뷰의 특정한 위치에 삽입할 셀을 데이터소스에게 요청하는 메소드

- **`func tableView(UITableView, titleForHeaderInSection: Int) -> String?`** 
  - 테이블 뷰의 **섹션의 헤더의 타이틀**을 데이터 소스에게 요청하는 메소드
- **`func tableView(UITableView, titleForFooterInSection: Int) -> String?`**
  - 테이블 뷰의 **섹션의 푸터의 타이틀**을 데이터소스에게 요청하는 메소드

<br>

---

<br><br>

#### Inserting or Deleting Table Rows - 테이블 로우들을 삭제하고 삽입하기

- **`func tableView(UITableView, commit: UITableViewCell.EditingStyle, forRowAt: IndexPath)`**
  - 명시된 로우의 삽입과 삭제의 커밋에 대해 데이터 소스에게 요청하는 메소드
- **`func tableView(UITableView, canEditRowAt: IndexPath) -> Bool`**
  - 주어진 로우가 수정가능한 지, 데이터소스에게 물어보는 메소드

<br>

---

<br><br>

####  Configuring an Index - 인덱스 설정하기

- **`func sectionIndexTitles(for: UITableView) -> [String]?`**
  - 데이터소스에게 테이블 뷰의 섹션의 **타이틀들**을 물어보는 메소드
- **`func tableView(UITableView, sectionForSectionIndexTitle: String, at: Int) -> Int`**
  - 주어진 타이틀과 섹션의 타이틀 인덱스를 가지고 섹션의 인덱스를 반환히기 위해 데이터 소스에게 물어보는 메서드

<br>

---

