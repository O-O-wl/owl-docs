

# Preserving the Results of a Throwing Expression

---

> 결과를 직렬화하거나, 메모이즈 할 때, throwing expression을 감싸서 호출하자
>
> ---

<br>

<br>

### Overview

---

때때로 전체 함수 호출의 결과 또는 값을 던지거나 반환할수 있는 표현을 보존해야한다.

예를 들어, 결과를 직렬화 하거나, 결과 데이터를 핸들링하는 앱의 다른 일부의 값으로 전달할 수도 있다.

`Result` 타입은 **잠재적으로 실패하는 연산의 결과를 획득하는 상황에서 사용**하자.

<br>

---

<br>

<br>

### Identifuy a Throwing Expression to Preserve

---

일반적으로 우리는 `do-try` 표현으로  throwing expressions 을 이용해 발생하는 즉시 핸들링 했다.

그러나 때때로 는 우리는 전체의 결과의 연산을 저장할 필요가 있다.  아래의 예제는` Random numbers generate APi` 이다. 이중의 반은 실패를 할 것이다.



```swift
enum EntropyError: Error  {
  case entropyDeleted
}

struct UnreliableRandomGenerator {
  func random() throws -> Int {
    if Bool.random() {
      return Int.random(in: 1...100)
    } else {
      throw EntropyError.entropyDeleted
    }
  }
}
```

<br>

<br>

### Convert the Throwing Expression to a Result

---

`Result` 의 이니셜라이저를 사용하는 throwing expression 으로 부터 던져진 에러 또는 반된 값을 보존할 수 있다. 이니셜라이저로 보내는 클로저 안에서 throwing expression 을 호출해라

```swift
let singleSample = Result { try UnreliableRandomGenerator().random() }
```



많은 상황에서, 우리는 코드의 보존된 결과를 사용합니다.

에를 들어, 우리는 랜덤한 수의 연속을 실행하고, 랜덤 숫자 생성기로 부터 반환된 수의 범위 뿐만 아니라 , API의 실패율에 대해 모두 통계적인 평균을 계산한다.

우리는 성공한 값 뿐만 아니라 모든 결과를 저장해야할 필요가 있는 경우가 있습니다.

<br>

아래의 예제는 init(catching:) 이니셜라이저를 사용하는 예시이다.

```swift
struct RandomessMonitor {
  // 랜덤 숫자 생성기
  let randomnessSource: UnreliableRandomGenerator
  // 결과 값 저장 배열
  var results: [Result<Int, Error>] = []
  
  init(generator: UnreliableRandomGenerator) {
    randomnessSource = generator
  }
  
  mutating func sample() {
    let sample = Result{ try randomnessSource.random() }
    results.append(sample)
  }
  //								  수의 합, 시도한 수
  func summary() -> (Double, Double) {
    let totals = results.reduce((sum: 0, count: 0)) { total, sample in 
          switch sample  {
            case .success(let number): 
            	return (total.sum + number, total.count)
            case .failure:
            return (total.sum, total.count + 1)
          }
       }
    return (
 						average: Double(totals.sum)/ Double(results.count - totals.count),
    				failureRate: Double(total.count)/ Double(results.count)
    			)
  		}
  
}
```



