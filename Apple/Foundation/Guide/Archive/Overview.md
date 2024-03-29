## Archives

아카이브는 복잡한 객체의 그래프를 저장할 수 있다.

아카이브는 그래프내의 모든 객체들의 identity와 관계들을 를 보관할 수 있다.

언 아카이브될 때, 객체 그래프의 재생성은 기존 객체그래프의 완벽한 복사본이어야 한다.

우리의 데이터 모델의 저장소로서 아카이브를 사용할 수 있다.

특정한 파일 포맷에 대한 데이터를 설계하는 것 대신에,  아카이빙 인프라를 사용하고 아카이브로 객체를 직접 저장할 수 있다.

아카이빙을 지원하기 위해서는 , 객체들은 `NSCoding` 프로토콜을 따라야한다. 

객체의 인스턴스 프로퍼티를 아카이브에 인코딩하는 메소드 와 아카이브로 부터 인스턴스 프로퍼티를 복원하는 디코드 메소드를 필요로 한다.

(`NSString`, `NSArray`, `NSNumber`,...) 와 같은 Foundation 값타입 객체들과 Appkit, UIKit 은 `NSCoding` 프로토콜을 따르고 있고, 이것들은 아카이브에 저장할 수 있다.



## Serializations



직렬화는  (dictionaries, arrays, strings, and binary data와 같은 ) 값 객체들의 단순한 계층구조를 저장한다. 

직렬화는 단지 객체들의 값과 계층에서 그 값의 위치만을 저장한다.

Multiple references to the same value object might result in multiple objects when deserialized. The mutability of the objects is not maintained.

같은 값 객체에 대한 다중 참조는  역직렬화 의 결과는 같은 값을 가진 여러개의 객체가 된다.

프로퍼티 리스트는 직렬화의 예이다. 앱 요소(`Info.plist`)와 사용자 환경설정은 프로퍼티 리스트로 저장된다.