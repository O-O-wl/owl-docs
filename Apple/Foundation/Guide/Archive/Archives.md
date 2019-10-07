# Archives

아카이브는 객체들과 값들을 아키텍처와 독립적인 바이트의 스트림으로의 전환을 의미한다.

그 전환은 객체들과 값들 사이에서의 관계와 동일성을 보존한다.

Cococa 아카이브는 Objective-C objects, scalars, arrays, structures, strings 들을 저장할 수 있다.

하지만 플랫폼에 따라 구현이 다른 `union` , ` void* `, `메소드 포인터`같은 변수들 저장할 수 없다.

아카이브는 데이터와 객체의 데이터 타입의 정보를 저장한다. 그래서 바이트들의 스트림으로 부터 디코드되어진 객체는 일반적으로 스트림으로 인코드한 객체와 동일한 클래스 타입이다.

<br>

## Coders

아카이브에 쓰이거나 읽혀지는 객체는 코더 객체와 함께 협력한다.

Coder객체는 `NSCoder` 라는 추상 클래스의 자식클래스의 인스턴스이다.

NSCoder는 객체에 저장된 정보를 가져 와서 파일에 쓰거나, 프로세스간에 또는 네트워크를 통해 전송하거나, 다른 유형의 데이터 교환을 수행하기에 적합한 다른 형식으로 넣는 광범위한 인터페이스를 선언합니다.

NSCoder는  파일을 올바른 포맷으로 쓰거나, 네트워크를 가로지르거나, 다른 프로세스와의 전환에서 다른 타입으로의 변환을 책임진다.



`NSCoder`는 바이트의 스트림안에 저장된 정보를 취해서 객체로 다시 되돌리는 인터페이스이다.

구현한 자식클래스는 특정한 아카이빙 포맷을 지원하는 인터페이스를 구현해야한다.

Coder 객체들은 인코드 되어지거나 디코드 되어진 객체에게 메시지를 보내 객체들을 읽거나 쓴다.



Coder는 아카이브를 생성할 때는   `encodeWithCoder:`  메시지를 객체에게 전달하고 , 아카이브를 읽어올 때는 `initWithCoder: ` 메시지를 전달한다.

그 메시지들은 `NSCoding` 프로토콜에 정의되어 있다.



Only objects whose class conforms to the `NSCoding` protocol can be written to an archive. 

단지 `NSCoding` 프로토콜을 준수하는 클래스의 객체들은 아카이브에 쓰여질 수 있다.

(The reference for each Cocoa class indicates whether the class adopts the `NSCoding` protocol.) 

객체가 메시지를 받을때, 객체는 메시지를 coder에게 객체 또는 값을 다시 보낸다.

객체들을 인코딩할 때, Coder는 아카이브에 객체들의 의 identity 와 계층에서의 위치를 기록한다.



중복과 제약 이라는 문제를 풀기위해서,  뿌리객체와 조건부 객체라는 개념을 이용한다.



### Root Object



객체그래프는 반드시 단순한 트리구조인 것은 아니다. 

두개의 객체가 각각 서로를 포함할수도 있다. 그래서 Coder가 모든 링크를 따라가며 인코딩한다면, 무한루프에 빠질수 있다.

Coder는 다중 그리고 순환 참조를 인식하고 다루고, 각각의 객체의 복사본을 하나보다 더 많이 인코딩해서는 안되고, 디코딩을 할 때는 모든 참조에 대해 다시 복원 되어야한다.

이러한 문제를 해결하기 위해서 `NSCoder`는 뿌리객체라는 개념을 사용한다.

뿌리 객체는 객체 그래프의 시작점이다.  객체그래프를 인코딩하기 위해서, 우리는 ` NSCoder`의 `encodeRootObject:` 메소드를 인코딩할 객체에 전달한다. 

이 호출의 컨텍스트안에 인코딩 되어지는 모든 객체는 추적된다,

If the coder is asked to encode an object more than once, the coder encodes a reference to the first encoding instead of encoding the object again.

만약 coder가 하나의 객체의 인코드를 여러번 요청 받는 다면,  객체를 다시  인코드하는 대신에 첫번째 참조에 대한 인코딩을 한다.

NSCoder는 뿌리객체를 지원하지는 않는다. 

simply encodes the object by invoking `encodeObject:`. `encodeObject:`  단순하게 메소드를 호출함으로써 객체를 인코드할 한다.

다중 클래스를 추적하는것은 `NSCoder`하위 클래스의 책임이다. 

<br>

### Conditional Objects

Another problem presented by object graphs is that it is not always appropriate to archive the entire graph. 

객체 그래프로 부터 보여지는 또 따른 문제는 전체 그래프를 아카이브하는 것이 항상 적절하지는 않다.

For example, when you encode an `NSView` object, the view can have many links to other objects: subviews, superviews, formatters, targets, windows, menus, and so on.

예를 들어, NSView를 인코딩할 때, View는 다른 객체들의 링크를 많이 가질 수있다. (Subviews. Superviews, …)등등 

만약 view 가 다른 객체들의 참조의 모든 것들을 인코드 한다면, 앱 전체를 인코드 해야할 수도 있다.

하나의 view 의 subview 들은 항상 아카이브 되어야한다. 그러나 superview는 필수는 아니다.

이 같은 경우, superview는 그래프의 외부 부분으로 여겨진다.

하나의 view 는 superview 없이 존재할 수 있다. 그러나 subview들은 그렇지 않다. 

그러나 하나의 view은 superview의 참조는 유지해야할 필요가 있다. 그러나 만약 superview가 아카이브내에 인코드 되어진다면, 하나의 view도 그 superview의 참조를 유지해야할 필요는 있다. 

이 같은 딜레마를 풀기위해서 , NSCoder는 조건부 객체라는 컨셉을 소개한다.

조건부 객체는 객체 그래프 다른 곳에서 무조건 적으로 인코드되야한다면 그떄는 인코드를 해야하는 객체이다.

조건부 객체는 `enocdeConditionalOnbject:forKey:` 를 호출함으로써 인코드되어진다. 

 A conditional object is encoded by invoking  `encodeConditionalObject:forKey:`. 

만약 객체를 인코드하는 모든 요청이 조건부적인 메소드로 만들어진다면, 그 객체는 인코드 되지 않고 디코드시 nil이 된다.

만약 객체가 그래프 다른 곳에서 인코드 되어진다면, 모든 조건부 참조는 디코드 하나의 인코드 된 객체를 디코드한다.

일반적으로, 조건부 객체는 객체에 대한 weak 참조를 인코드할 때 사용된다.

`NSCoder` does not implement support for conditional objects;

`NSCoder`는 조건부 객체를 지원하는 것이 주 관심사는 아니다. `



NSCoder의 `encodeConditionalObject:forKey:` 의 구현은 단순히  `encodeObject:forKey` 를 호출함으로써 객체를 인코딩하는 것이다.  

구체 `NSCoder`의 서브 클래스는 조건부 객체의 추적을 유지하고, 아직 필요로 하지않는 객체들은 굳이 인코드하지는 않는다.



## Keyed Archives

Keyed archives are created by `NSKeyedArchiver` objects and decoded by `NSKeyedUnarchiver` objects. Keyed archives differ from sequential archives in that every value encoded in a keyed archive is given a name, or key. When decoding the archive, the values can be requested by name, allowing the values to be requested in any order or not at all. This freedom enables greater flexibility for making your classes forward and backward compatible.

The following sections describe how to use keyed archives.



### Naming Values

Values that an object encodes to a keyed archive can be individually named with an arbitrary string. Archives are hierarchical with each object defining a separate name space for its encoded values, similar to the object’s instance variables. Therefore, keys must be unique only within the scope of the current object being encoded. The keys used by object A to encode its instance variables do not conflict with the keys used by object B, even if A and B are instances of the same class. Within a single object, however, the keys used by a subclass can conflict with keys used in its superclasses.

Public classes, such as those in a framework, which can be subclassed, should add a prefix to the name string to avoid collisions with keys that may be used now or in the future by the subclasses of the class. A reasonable prefix is the full name of the class. Cocoa classes use the prefix “NS” in their keys, the same as the API prefix, and carefully makes sure that there are no collisions in the class hierarchy. Another possibility is to use the same string as the bundle identifier for the framework. 

You should avoid using “$” as a prefix for your keys. The keyed archiver and unarchiver use keys prefixed with “$” for internal values. Although they test for and mangle user-defined keys that have a “$” prefix, this overhead slows down archiving performance.

Subclasses also need to be somewhat aware of the prefix used by superclasses to avoid accidental collisions on key names. Subclasses of Cocoa classes should avoid unintentionally starting their key names with “NS”. For example, don’t name a key “NSString search options”.



### Return Values for Missing Keys

While decoding, if you request a keyed value that does not exist, the unarchiver returns a default value based on the return type of the decode method you invoked. The default values are the equivalent of zero for each data type: `nil`for objects, `NO` for booleans, `0.0` for reals, `NSZeroSize` for sizes, and so on. If you need to detect the absence of a keyed value, use the `NSKeyedUnarchiver` instance method `containsValueForKey:`, which returns `NO` if the supplied key is not present. For performance reasons, you should avoid explicitly testing for keys when the default values are sufficient.



### Type Coercions

`NSKeyedUnarchiver` supports limited type coercion. A value encoded as any type of integer, be it a standard `int` or an explicit 32-bit or 64-bit integer, can be decoded using any of the integer decode methods. Likewise, a value encoded as a `float` or `double` can be decoded as either a `float` or a `double`value. When decoding a `double` value as a `float`, though, the decoded value loses precision. If an encoded value is too large to fit within the coerced decoded type, the decoding method throws an `NSRangeException`. Further, when trying to coerce a value to an incompatible type, such as decoding an `int`as a `float`, the decoding method throws an `NSInvalidUnarchiveOperationException`.



### Class Versioning

Versioning of encoded data is not handled through class versioning with keyed coding as it is in sequential archives. In fact, no automatic versioning is done for a class; this allows a class to at least get a look at the encoded values without the unarchiver deciding on its own that the versions fatally mismatch. A class is free to decide to encode some type of version information with its other values if it wishes, and this information can be of any type or quantity.



### Root Objects

The `encodeObject:` and `encodeObject:forKey:` methods are able to track multiple references to objects in multiple object graphs. As many object graphs or values as desired may be encoded at the top level of a keyed archive.

`NSKeyedArchiver` implements `archiveRootObject:toFile:` and `archivedDataWithRootObject:` to produce archives with a single object graph. These archives, however, have to be unarchived using the `NSKeyedUnarchiver` methods `unarchiveObjectWithFile:` and `unarchiveObjectWithData:`.



### Delegates

`NSKeyedArchiver` and `NSKeyedUnarchiver` objects can have delegate objects. The delegates are notified as each object is encoded or decoded. You can use the delegates to perform substitutions, replacing one object for another, if desired.



### Non-Keyed Coding Methods

Keyed archivers do not have to provide names for every value encoded in the archive. The `NSKeyedArchiver` and `NSKeyedUnarchiver` classes implement the non-keyed encoding and decoding methods that they inherit from `NSCoder`. Use of the non-keyed methods within keyed coding is discouraged.