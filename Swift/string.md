\# String 타입



\- String은 일반적으로 문자(Character)가 1개 이상 모여서 이루어져있고, 사람이 인식하기 위한 글자를 표현하는 모든 타입을 의미한다.



\## Substring



\- prefix(), split() 등 과 같은 메서드를 사용해서 문자열에서 가져온 부분문자열 타입



\- Substring은 String에서 사용하는 대부분의 메서드를 가지고 있으며 String과 같은 방법으로 사용할 수 있다.



\- SubString은 원래 문자열이나 다른 하위 문자열을 저장하는데 사용되는 메모리의 일부를 다시 사용한다. (성능 최적화에 용이)



\- 위에서 언급한것 처럼 Substring은 자신의 원본 문자열에 대한 참조이다. 즉 부분 문자열을 다루는데 있어 원본 문자열에 대한 참조를 유지하고 있어야 한다. 그러므로 언제든지 부분 문자열을 다룰때는 이를 문자열로 변환해야 한다. 



\- 변수를 선언해서 문자열을 복사하면 이전의 문자열의 메모리는 회수될 수 있다.



   ![스크린샷 2019-04-25 오후 8.24.19](/Users/janggukjin/Desktop/스크린샷 2019-04-25 오후 8.24.19.png)



\## String.Sequence



\- Apple Developer 문서에 공식적인 정의는 아래와 같다.



\```swift

typealias SubSequence = Substring

// typealias : 기존에 선언되어있는 유형에 새로운 유형의 별칭을 사용함으로써 코드를 더 읽기 쉽도록, 이해하기 쉽도록 명확하게 만드는 문법



// typealias 예시

typealias Name = String

let name: Name = "홍길동"

// String 유형을 name이라는 별칭으로 사용한다고 선언함

\```



\- 이를 통해 알 수 있는 점은 String.Subsequence 는 Substring타입이며 더욱 이해하기 쉽도록하게 하는데 의의가 있다.