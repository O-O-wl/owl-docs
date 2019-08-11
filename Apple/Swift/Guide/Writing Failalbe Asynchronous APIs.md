# Writing Failable Asynchronous APIs

---

우리는 에러를 동기적으로 리턴할 수 없을 때, API의 일부의 객체를 밴딩할 수 있다.

<br>

<br>

### Overview

----

실패가능한 함수, 메소드 또는 다른 API를 작성할 때, throws keyword 를 정의부에 사용한다.

그러나, 우리는 throws 키워드를 비동기하게 리턴하는 API model에 대해 사용할 수 없다.

 비동기로 성공 인지 실패인지 에 대한 정보를 붙잡는 객체 `Result` 열거형을 사용하자.

그리고 `Result.success(:) Result.failure(:)` 케이스의 연관 값을 사용하자

<br>

---

<br><br>

### Return Result Instances Asynchronously

---

아래 따르는 예는 랜덤한 수들의 비동기한 소스를 모델링 합니다.

`fetchRemoteRandomNumver(compltetion: ) `메소드는 `Void`를 동기적으로 리턴한다.

그리고 비동기적으로 `Result<Int, EntropyError>` 를 사용해서(인자로 해서?)  `completion handler` 를 호출한다.

```swift
let queue = DispatchQueue(label: "com.example.queue")

enum EntropyError: Error {
  case entropyDeleted
}

struct AsyncRandomGenerator {
  static let entropyLimit = 5
  var count = 0
}

mutating func fetchRemoteRandomNumber(completion: @escaping (Result<Int, EntropyError>) -> Void) {
  let result: Result<Int, EntropyError>
  if count < AsyncRandomGenerator.entropyLimit {
    result = .success(Int.random(in:1...100))
  } else {
    result = .failure(.entropyDeleted)
  }
  count += 1
  
  queue.asyncAfter(deadline: .now() + 2) {
    	compltion(result)
  	}
  }
}
```

<br>

랜덤 숫자 생성기의 사용자들은 성공과 실패 둘다 핸들링 할 수잇다.

```swift
var generator = AsyncRandomGenerator()

// Request one more number than the limit to trigger a failure.
(0..<AsyncRandomGenerator.entropyLimit + 1).forEach { _ in
    generator.fetchRemoteRandomNumber { result in
        switch result {
        case .success(let number):
            print(number)
        case .failure(let error):
            print("Source of randomness failed: \(error)")
        }
    }
}

print("Waiting on some numbers.")

dispatchMain()
```

