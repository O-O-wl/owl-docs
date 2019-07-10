# 주석

---

> 주석은 기껏해야 **필요악**이다. - 로버트.C.마틴



책을 읽기전까진 , 주석은 읽기 편하게 설명을 해주고 있다고 느꼈고, 꼭 필요하다고 생각했었다.

코드를 읽는 것보다 주석을 읽으면 빠르게 이해되고, 이해가 안되는 코드도 주석을 읽고 보면 술술 읽혀 나갔던 적이 많았다.

하지만 이 책에서는 **주석은 코드의 실패**라고 한다.

내가 주석이 유용하다고 느낀 건 주석이 유용해서가 아니라  코드가 의도를 표현하지 못하고 복잡하고 명료하게 작성되서라는 것을 알게 되었다.

### "주석이 필요한 코드는 잘못 작성된 코드이다" 

라고 생각된다.

```swift
// 이름을 가져오는 메서드  
func get() -> String {
    return self.name
 }
```

위와 같이 주석이 메소드의 동작을 설명해주는 주석을  보는 것보다는

```swift
func getName() -> String {
  return self.name
}
```

위의 코드로 함수의 이름으로 충분히 동작을 표현해서 의도를 전달하는 것이 더 적절할 것같다.

대부분의 경우에는 주석이 없어도 코드로 의도를 전달할 수 있다.

이 밖에도 주석이 나쁘게 작용하는 경우는 엄청 많다.

###" 주석은 거짓말을 자주한다 "

```swift
// 사람의 이름을 소개하는 메소드입니다.
func describe(person: People) {
  print("이름은 \(person.name)입니다.")
  print("나이는 \(person.age)살 입니다.")
  print("취미는 \(person.habby)입니다.")
  print("혈액형은 \(person.bloodType)형입니다")
}
```

위의 코드의 문제가 있나?

###없다. 

하지만 주석에는 문제가 있다.

주석은 이름을 소개하는 함수라고 정보를 제공하고 있다.

아마도 이전에 구현이 변경되기 전에는 이름만을 표현하는 함수였을 것이다.

하지만 지금은 `People` 객체의 여러 속성을 묘사하는 메소드로 구현이 추가되어 변경된 것같다.

하지만  그 과정에서 주석은 최신화가 되지 않은 것이다.

### 주석을 달거면 코드의 수정을 반영해야한다.

안그러면 이렇게 주석은 거짓말을 하게된다.





몇몇 경우에는 주석이 도움을 주는 경우도 있긴하다. 



### 좋은주석

- **법적인 주석**

  

  ```swift
  //
  //  main.swift
  //  Project
  //
  //  Created by O-O-wl on 07/03/2019.
  //  Copyright © 2019 부엉이. All rights reserved.
  //
  ```

- **정보를 제공하는 주석**

- ```swift
  func check(id: String) -> Bool {
     let idFormat = "[a-zA-Z0-9]{4,}"
     let regex = try! NSRegularExpression(pattern:idFormat, options:[])
  	 //...
  }
  ```

- ```swift
  func check(id: String) -> Bool {
     let idFormat = "[a-zA-Z0-9]{4,}" // 4글자이상 영어대,소문자,숫자로 구성된 패턴
     let regex = try! NSRegularExpression(pattern:idFormat, options:[])
  	 //...
  }
  ```

  위의 정규표현식을 아직 모르는 이들을 위해 주석으로 처리했다. 

  저런 자세한 사항을 변수명으로 표현하는 것보다 주석으로 표현함으로써 , 선언부에서만 자세한 사항을 표현하게끔 했다.

  만약 변수명으로 사용했다면 , 변수명을 사용할때마다 쓸데 없는 자세한사항을 매번 표현했을 수도 있다.

  

- **결과를 경고하는 주석**

- ```swift
  // Null 이 반환될 경우 오류가 발생할 수 있습니다.
  func search(keyWord: String) -> Object {
    return self.dictionary[keyWord]!
  }
  /// 위의 예시는 반환형을 Object? 옵셔널로 표현함으로써 해결될 수 있으나, 예시로 사용하기 위해 사용하였다.
  ```

- **TODO 주석**

  ```swift
  /// -Todo: 옵셔널로 수정할 예정, 더 의도가 분명한 이름으로 수정
  // Null 이 반환될 경우 오류가 발생할 수 있습니다.
  func search(keyWord: String) -> Object {
    return self.dictionary[keyWord]!
  }
  ```

  앞으로의 해야할 일이나 , 다른 협업하는 누군가에게 부탁, 요청을 적어두는 것이다.

  `Todo` 중에 해결된 코드에 대해서는 지워야한다. 주기적은 점검을 통해서 지워나가자.

- **중요성 강조 주석**

- ```swift
  func divide(operand1:Int,operand2:Int) -> Int {
    // 분모가 0인지 체크하므로 수정되어서는 안된다.
    guard check(operand2) else { 
      return  0
    }
    return operand1/operand2
  }
  ```

  구현이 바뀌어서는 안되는 부분에 대한 부분을 협업하는 **다른 협업하는 개발자**에게 중요성을 명시적으로 알리기위해서 주석을 사용할 수 도 있다.



## 위와 같은 경우를 제외하고는 주석사용하기 이전에 코드로 명확한 표현을 하기 위해서 노력해야 할 것이다