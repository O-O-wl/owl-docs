# Object serialization in iOS





In this post I’ll try to systemise the knowledge about major formats and types of data serialization in iOS development. I will also compare ObjC approach to the problem (NSCoding) with the one we got in Swift 4 (Codable) and will take a look at some specific use cases like interop between them and the ability to work with complex object graphs.

## Contents:

- [Serialization as a separate process](https://www.dmtopolog.com/object-serialization-in-ios/#serialization-as-a-separate-process)
- [NSCoding](https://www.dmtopolog.com/object-serialization-in-ios/#nscoding)
- [NSCoder and NSKeyedArchiver](https://www.dmtopolog.com/object-serialization-in-ios/#nscoder-and-nskeyedarchiver)
- [JSON](https://www.dmtopolog.com/object-serialization-in-ios/#json)
- [Plist](https://www.dmtopolog.com/object-serialization-in-ios/#plist)
- [Other formats](https://www.dmtopolog.com/object-serialization-in-ios/#other-formats)
- [Codable](https://www.dmtopolog.com/object-serialization-in-ios/#codable)·
- 
- [Some benchmarks and comparison](https://www.dmtopolog.com/object-serialization-in-ios/#some-benchmarks-and-comparison)
- [NSKeyedArchiver encoding Codable](https://www.dmtopolog.com/object-serialization-in-ios/#nskeyedarchiver-encoding-codable)
- [Encoding complex object graph](https://www.dmtopolog.com/object-serialization-in-ios/#encoding-complex-object-graph)
- [Codable as a replacement for NSCoding](https://www.dmtopolog.com/object-serialization-in-ios/#codable-as-a-replacement-for-nscoding)

## Serialization as a separate process



직렬화는 데이터의 구조 또는 객체의 상태를 어떤 저장되거나 전송되거나 재구성 될 수 있게 변환시키는 작업입니다.

역직렬화는 직렬화된 데이터로 부터 우리의 모델을 재생하는 작업입니다. 



Quite often serialization is only considered as a part of archiving data on disc. For a long time we would naturally use this code to store NSArray or NSDictionary to file:

직렬화는  꽤 대부분은 단지 디스크에 데이터를 아카이빙하는 부분으로써 고려되어 집니다.

우리는 NSArray 또는 NSDictionary를 파일에 저장하는 코드를 자연스럽게 사용해왔습니다.



```objective-c
NSURL *localURL = …
NSDictionary *someDict = …
[someDict writeToURL:localURL atomically:YES];
```



You can use this ObjC API in Swift as well:

```swift
let hosts: [String] = ["host1",	"host2"]
try? (hosts as NSArray).write(to: fileUrl)
```

여기 한 줄 뿐인 코드는 객체를 직렬화하고 그것을 파일에 저장하는 코드입니다.

간단하고 유용합니다.

그러나 이러한 접근은 몇몇 한계가 있습니다. 우리는 커스텀 클래스에 대해서는 사용할 수 없고, 또 Swift안에서 이러한 타입캐스팅은 선호되는 방식은 아닙니다.

그러나 더 중요한 것은, 직렬화는 영속할 데이터와 강하게 결합됩니다.

이 API를 사용하면 우리가 프로세스를 제어하지 않으며 우리가 조정할 수 없습니다.

우리가 너가 디스크에 쓴 파일을 직접 보기 전 까지, XML로 객체가 직렬화 되었는 지 알 수 없습니다.

대부분의 직렬화를 사용하는 앱은  인터넷으 가쳐 데이터를 보내거나 로컬에 저장합니다.

그러나 몇몇 다른 케이스가 있습니다, 예를 들어 데이터를 서드파이 라이브러리에 전송하거나 또는 블루투스를 통해 다른 기기에 전송하기도 한다.

이 말은 두개의 분리되었지만 캡슐화된 시스템이 있다는 것입니다.



하나의 시스템은 전송자이고, 다른 하나는 리시버 이다. 리시버쪽에서 데이터를 포맷팅할 수 있게 해주는 프로토콜이 있다.

직랼화는 객체들을 첫번째 시스템에서 사용하는 객체들을 협의된 포맷으로 전환하는 방법입니다.

데이터가 어떻게 어디에 저장거나 전송되어질 지는 사용되어질 지는 다른 관심사입니다.  전송과 저장은 별도의 다른 작업입니다.



iOS 개발에서 직렬화를 다루는 다른 방법들과 인기있는 형식을 한번 구경해봅시다.



## NSCoding

`NSCoding` 은  objc의 : `-initWithCoder:` and `encodeWithCoder:`  두개의 메소드를 가진 경량 프로토콜입니다. 

우리의 커스텀 클래스들은 `NSCoding`을 직렬화와 역직렬화를 위해 구현합니다.

어떻게 우리 객체의 다른 프로퍼티들을 키-값 형태의 컨테이너로 인코딩할지 그 방법을  구현합니다.

우리는 어떻게 우리의 객체를 키-값 쌍으로 변경되어질지를 묘사합니다.

몇몇 coder는 컨테이너를 바이너리 데이터로 직렬화 할 것입니다.



꽤 많이 사용되는 인코딩 구현: 

```swift
    func encodeWithCoder(coder: NSCoder) {
        coder.encodeObject(self.name, forKey: "name")
        coder.encodeInt(Int32(self.age), forKey: "age")
        // and so on
    }
```



때떄로 우리는 몇몇 프로퍼티를 인코딩는 정교한 방법을 필요로 합니다.

또 쉽게 우리의 커스텀 클래스의 다른 버전(몇몇 프로퍼티가 더해지거나 제거된) 을 쉽게 핸들링할수 있는 방법이 있습니다

그러나 우리가 모든 데이터 모델을 위해 작성해야하는  보일러플레이트 코드가 많이 있습니다.

모델이 커지면 커질 수록 우리가 작성해야하는 코드는 더 많아 질 것입니다.

Swift 안의 `NSCoding`을 따르는 클래스여야합니다. `enum` `struct`는 불가능합니다.

우리는 기본적으로 coder에게 값을 넘길 때, 키 - 값 형태를 사용합니다.

그래서 모든 타입을 모든 키에 전송이 가능합니다?

>  🦉 아마 키랑 타입은 아무 연관이 없기때문에, 키로 찾아온 값은 타입을 추론할 수가 없다는 말인듯???

결국 우리는 그 값들을 다시 디코드할 필요가 있을 때, 해당 값의 타입을 확실히 알 수 는 없습니다. 이는 Swift에서 타입을 확인하기위한 비용을 지불해야함을 의미합니다. 

>  🦉   반환 타입이 Any와 같은 타입이고 이걸 우리가 사용할 타입으로 확인하고 바꾸는 작업비용이 든다.





## NSCoder and NSKeyedArchiver

 

우리는 이렇게 많은 방식의 디코딩과 인코딩 방식이 있다는 것에 놀라지 않을수 없습니다.

```swift
	func encode(Int, forKey: String)
	func encode(CMTimeRange, forKey: String)

	func decodeCGPoint(forKey: String) -> CGPoint
	func decodeUIOffset(forKey: String) -> UIOffset
```

오늘날 Foundation 은 `NSCoder`의 구체 서브클래스하나와 `NSKeyedArchiver` 가 있습니다.

(NSArchiver 와 NSPortCoder 두개가 존재한다. 하지만 IOS개발에서 현재 deprecated 되어 있다)

`NSKeyedArchiver`는` NSCoding` 프로토콜을 준수하는 객체들과 함께 작동하는 아카이버 클래스입니다.

우리가 호출한 객체를 바이너리데이터로 변경하고 싶다면 호출하면 됩니다.

```swift
    let data = try? NSKeyedArchiver.archivedData(
      withRootObject: yourObject,
      requiringSecureCoding: false
    )
```



`NSCoding`의 구현은 객체데이터를 키-값 컨테이너로 패킹하는 방법을 coder에게 알려주고,` NSKeyedArchiver` 그 컨테이너를 바이너리 데이터로 바꾸는 작업을 합니다.

>**NSCoding** : 코더가 객체를 [key: value]로 바꾸는 방법을 알려준다.
>
>**NSKeyedArchiver**:  바뀐 컨테이너를 이진데이터로 바꾸는 작업을 한다.

NSKeyedArchiver는 직렬화를 하고  파일에게 출력데이터를 작성하는데 사용될 수 있습니다.  

```swift
	NSKeyedArchiver.archiveRootObject(yourObject, toFile: fileUrl.path)
// 아카이빙과 파일작성
```



NSKeyedArchiver는 데이터를 바이너리와 xml 두가지 형태로 인코딩할 수 있습니다.



전자는 바이너리는 더 가볍고 후자는 사람이 읽을 수 있고 수정가능하다. 그러나 더 많은 디스크 영역을 차지합니다.

Xml 형태로 바꾸려면 아래와 같은 코드로 가능합니다 

```swift
	let archiver = NSKeyedArchiver(requiringSecureCoding: false)
	archiver.outputFormat = .xml
	archiver.encodeRootObject(yourObject)
	let data = archiver.encodedData
```



---

### 현재 번역 진행중



## JSON

The most popular format nowadays is JSON. Up to iOS 5/macOS 10.7 Foundation didn’t have a built-in functionality to parse JSON, so there were several different third party libs for that (check out the [list here](http://awesomeios.com/#json)). Finally `NSJSONSerialization` class appeared and took it over.

In iOS serialization chain for encoding your custom objects to JSON data looks like this:

![img](https://www.dmtopolog.com/images-posts/2019-01-14-object-serialization-in-ios/ObjectToJSON.png)

NSJSONSerialization offers only the second part - serialization from NSDictionaty/NSArray to JSON, so you still need to do the mapping from your custom object to NSDictionary. The class is straightforward and supposed to be used like this:

```objective-c
    NSDictionary *objectDict = @{
                             @"name" : @"John Smith",
                             @"age" : @(34),
                             @"gender" : @"male"
                             };
    NSError *error;
    NSData *jsonData = [NSJSONSerialization dataWithJSONObject:objectDict
                                                       options:kNilOptions
                                                         error:&error];
```

As I mentioned the root object of the tree we aim to encode can be NSDictionaty or NSArray only. But there are some other limitation for using NSJSONSerialization:

- all objects are instances of NSString, NSNumber, NSArray, NSDictionary, or NSNull.
- all dictionary keys are instances of NSString.
- Numbers are not NaN or infinity.

There are some options you can pass to NSJSONSerialization for encoding and decoding data:

**1. Encoding (NSJSONWritingOptions):**

- PrettyPrinted - using white space and indentation to make the output more readable (adding some extra bytes to resulting data)
- SortedKeys - sorting keys in lexicographic order

**2. Decoding (NSJSONReadingOptions):**

- MutableContainers - arrays and dictionaries are created as mutable objects
- MutableLeaves - strings are created as instances of NSMutableString.
- AllowFragments - tells the parser to allow top-level objects that are not an instance of NSArray or NSDictionary.

If you don’t want to specify any options you can pass `kNilOptions`-constant in ObjC code or an empty array in Swift.

Back to our serialization chain: with NSJSONSerialization we still need to map our custom objects into arrays and dictionaries. For that purpose you would sometimes use manual key-value mapping inside your model object:

```objective-c
	- (NSDictionary *)jsonDict {
	    return @{
	             @"name" : self.name,
	             @"age" : @(self.age),
	             @"gender" : self.gender
	             };
	}
```

But it’s also possible to automate it using class inspection in ObjC runtime.

```objective-c
	#import &lt;objc/runtime.h>
	...
	unsigned int outCount, i;
	objc_property_t *properties = class_copyPropertyList([self class], &outCount);
	for(i = 0; i &lt; outCount; i++) {
	    objc_property_t property = properties[i];
	    fprintf(stdout, "%s %s\n", property_getName(property), property_getAttributes(property));
	}
	free(properties);
```

Here we get a list of all the properties of a given class with some attributes including the property type. So we can generalise the algorithm to serialise every object into JSON data. Optionally we can use some mapping tables and transformations if exact keys or values of the object properties are not what we want to see in JSON. There are a number of libraries out there successfully using this approach (I had a pleasant experience using [JSONModel](https://github.com/jsonmodel/jsonmodel)).

## Plist

Plist (from “property list”;) is a structured way to represent and persist a single object or an object tree. The format has appeared in NeXTSTEP operation system and made its way through decades to contemporary macOS/iOS ecosystem.

Generally plist serialization in Foundation world looks like JSON serialization. We have NSPropertyListSerialization (PropertyListSerialization when calling from Swift) which is being around since macOS 10.2/iOS 2. The class - an older brother of NSJSONSerialization - makes conversion between plist (which is represented in memory by instance of NSDictionary or NSArray) and binary data (represented by NSData).

```objective-c
    NSError *error;
    NSPropertyListFormat format;
    id plist = [NSPropertyListSerialization propertyListWithData:data
                                                         options:NSPropertyListImmutable
                                                          format:&format
                                                           error:&error];
```

NSPropertyListSerialization works only with limited amount of Foundation classes: NSData, NSString, NSArray, NSDictionary, NSDate, and NSNumber. If an instance of some other class is inside the dictionary or array you want to encode serialization will fail.

Property list can be encoded/decoded in two formats: xml (`NSPropertyListXMLFormat_v1_0`) or binary data (`NSPropertyListBinaryFormat_v1_0`) with their pros and cons (pretty much the same as for NSKeyedArchiever). There is one more format - OpenStep (`NSPropertyListOpenStepFormat`) which is deprecated now and available only for reading.

([*Here*](https://medium.com/@karaiskc/understanding-apples-binary-property-list-format-281e6da00dbd) *you can find some details about the structure of plist binary format)*

There is an `options` parameter in both serialising and deserialising calls. It has to do with mutability. When you deserialise an object from plist data you can tell serialiser if you wan to have mutable containers - NSPropertyListMutableContainers value. If you pass NSPropertyListMutableContainersAndLeaves serialiser will try to make all the values mutable if the value class has its mutable counterpart.

When serialising objects to binary data you cannot save mutability info of your objects, so `options` parameter makes no sense and you would always pass an empty value.

## Codable

All these APIs for serialization were good enough for ObjC but had some significant limitations for Swift. I’ve already mentioned that NSCoding cannot work with swift native structs and enums (although developers came up with [some workarounds](http://swiftandpainless.com/nscoding-and-swift-structs/)), and if you want to use it in swift classes you have to make them visible in ObjC. Also all the serialization APIs are needed to be adjusted for Swift’s strong type safety. So creating some tools for serialisation in a standard library was the matter of time.

Codable (which unites Encodable and Decodable protocols) was implemented in Swift 4 and made developers’ lives much easier.

As till Swift 4 there were no built-in native solution for data serialization, plenty of third party libraries appeared to fill this gap in. In [swift-evolution proposal for Codable](https://github.com/apple/swift-evolution/blob/master/proposals/0166-swift-archival-serialization.md) the authors reason that out of these solutions (for JSON parsing specifically) there is none which is both easy to use/implement and type safe enough.

It’s not explicitly said in the proposal but obviously the ability to generate Codable implementation by compiler, moving this workload from a developer, is a significant benefit of the approach we eventually have seen implemented.

Each protocol (Encodable/Decodable) consist of just one required method. The great news is that if all the object’s properties conform to Codable themselves the compiler will be able to generate the implementation for the object. If the condition isn’t met or you need some additional customisation you can write the methods manually. For Encodable it will look like this:

```swift
	func encode(to encoder: Encoder) throws {
    	var container = encoder.container(keyedBy: CodingKeys.self)
    	try container.encode(someObjectProperty, forKey: CodingKeys.someKey)
    	// handling all the object's properties
	}
```

Basically that’s the same code as the one generate by the compiler for you.

By making your object conform to this protocols you get an ability to decode them to both JSON and plist format out of the box - just pick a corresponding encoder.

Encoder is an object which does the actual serialization for the model object. There are two built-in encoders: JSONEncoder and PropertyListEncoder for corresponding formats. All the system is protocol based and there are specific Encoder/Decoder protocols so it’s possible to implement your own coders for another formats which can work with all Codable models ([an example from Mike Ash](https://www.mikeash.com/pyblog/friday-qa-2017-07-28-a-binary-coder-for-swift.html)).

The usage of built-in encoders (as well as decoders) is trivial (as far as your yourCustomObject conforms to Codable):

```swift
let jsonData = try JSONEncoder().encode(yourCustomObject)
```

This way we have ent-to-end serialization from your custom object right into the binary data.

If you take a look inside Swift source code you will see that JSONEncoder uses the same NSJSONSerialization from Foundation (which is called JSONSerialization when being used from swift) that I’ve mentioned before. JSONEncoder uses Encodable implementation of a model object as an instruction how to wrap all its properties into NSObjects. Then it puts them to a container (which is actually an array of NSObjects) and passes the container to JSONSerialization for the conversion into binary data. So basically JSONEncoder utilise Encodable implementation to map your custom objects into a data structure which can be used by good old NSJSONSerialization. So Swift safely and elegantly does the same that we developers had been doing before: performs some kind of mapping for custom model’s properties into a Foundation-based container and then uses NSJSONSerialization.

The same is fair for PropertyListEncoder as well.

*(If you want to read in details about complex use cases for Codable/Encodable/Decodable and all the capabilities of JSONEncoder I refer you to* [*this*](https://benscheirman.com/2017/06/swift-json/) *or* [*this*](https://www.hackingwithswift.com/articles/119/codable-cheat-sheet) *posts)*

## Some benchmarks and comparison

It’s hard to fairly compare all the different approaches regarding their speed or resulting data size because originally they serve completely different needs.

NSKeyedArchiver (as well as NSArchiever before) suppose to deal with complex object graphs. It takes into consideration such things like multiple references to the same object, object replacement, calling each object’s `encodeWithCoder()` method, object graphs, and so on. If you use binary output it also takes some extra effort to make the output smaller than it would otherwise be. So no surprise it takes a lot of time for NSKeyedArchiver to serialise a custom object or object graph.

JSON/Plist serialisers originally would work only with dictionary or array and some small set of predefined Foundation types. It makes the life of an encoder much easier so it can spend less time and be more efficient in disc space.

Talking about the size of output data, JSON is the most compact format out of these three and plist is a bit more verbose one (2-4 times difference). The output of NSKeyedArchiever with all the metadata it provides can take up to 10 times more disc space than JSON. But usual eventual use case for NSKeyedArchiever’s serialization is data persistence, so here the proper data structure is more important than the occupied space.

When talking about ObjC the comparison may only be done for NSDictionary or NSArray (JSON/Plist serialisers don’t work with custom types) and the results are more or less predictable: JSON is the most compact format, JSON and plist are both quite speed efficient and NSKeyedArchiver sucks (you can find [one of the benchmarks here](https://holtwick.de/blog/serialization))

But in Swift we are able to use all these 3 encoders for our custom classes conforming Codable so the conditions are equal for all of them.

I did [a trivial benchmark](https://gist.github.com/DmIvanov/ead75141ebb6dc24b5820f7c5e4a3749) myself to check how the things really are. After I took a look at the results for Swift I decided to include Obj encoding as well for custom objects. All the tests are done for small objects.

Here are the results:

![img](https://www.dmtopolog.com/images-posts/2019-01-14-object-serialization-in-ios/serialization_benchmark_chart.png)

**Serialization in Swift.**

- NSKeyedArchiver is the slowest one - let’s remember that the ability to handle Codable was added just for compatibility to this ObjC API. JSONEncoder/PropertyListEncoder which are native for Codable do the job faster.
- Plist serialization is slower than JSON serialization. But for binary plist format the difference is almost negligible. Binary plist is considered as “not human readable”;, but basically it’s just the matter of application you use to open the file. JSON is also not readable until you open it in a text editor 😉
- Serialization into binary format takes more time than XML for both plist serialiser and keyed archiver. Don’t forget that the archiver puts more metadata to the output XML, so its output file is much bigger.

**Serialization in ObjC:**

- Here **we don’t compare NSKeyedArchiver with JSON/Plist serialisers** for custom objects. For serialisers I used manual mapping (object property -> dictionary key) which is way faster than general archiver’s approach with key-value container. So for JSON/Plist we still serialise dictionary instead of the object.
- Comparing JSON with plist serialization we see that the former is twice faster than the latter.
- We can see that there is no significant difference between binary and XML formats for both plist serialiser and keyed archiver.

**Comparing Swift and ObjC:**

- I put JSON/Plist serialization for ObjC and Swift next to each other to show how big is encoding work of Codable implementation (the difference between the ObjC and Swift bars for each type) and how fast is actual work of ObjC serialisers (which is common for both languages)
- Check out NSKeyedArchiver: It’s not surprising that Swift is slower here as well, keyed archiver is completely an ObjC API.

## NSKeyedArchiver working with Codable

After Codable was introduces the necessity to use NSKeyedArchiver from Swift almost disappeared. Now to store some custom object on disk with an equal effort you can use JSONEncoder or PropertyListEncoder for serialization. NSKeyedArchiver is not the main coder for serialising custom objects anymore. But if you want to use it with models conforming to Codable you can do it (without implementing NSCoding protocol methods). Swift team specifically introduced two new methods for NSKeyedArchiver to make it work with Codable:

```swift
	// These are provided in the Swift overlay, and included in swift-corelibs-foundation.
	extension NSKeyedArchiver {
	    public func encodeCodable(_ codable: Encodable?, forKey key: String) { ... }
	}

	extension NSKeyedUnarchiver {
	    public func decodeCodable&lt;T : Decodable>(_ type: T.Type, forKey key: String) -> T? { ... }
	}
```

For encoding the root object you would use an encoding method like this:

```swift
	do {
	    let archiver = NSKeyedArchiver(requiringSecureCoding: false)
	    archiver.outputFormat = .binary
	    try archiver.encodeEncodable(yourObject, forKey: NSKeyedArchiveRootObjectKey)
	    archiver.finishEncoding()

	    // then you can use encoded data
	    try archiver.encodedData.write(to: fileUrl)
	} catch {
	    print("Couldn't write file: \(error)")
	}
```

Another case is when you want to encode the root object with let’s say JSONEncoder. This object almost conforms to Codable, but it has just one property of a legacy ObjC class which cannot adopt Codable. So your option is to manually implement Codable’s encode() method where you use NSKeyedArchiver for converting this legacy property to binary data:

```swift
  struct Person: Codable {

	    let name: String
	    let workHistory: WorkHistory // cannot be extended to adopt Codable, but conforms to NSCoding

	    enum CodingKeys: String, CodingKey {
	        case name
	        case workHistory
	    }

	    func encode(to encoder: Encoder) throws {
	        var container = encoder.container(keyedBy: CodingKeys.self)
	        try container.encode(name, forKey: .name)
	        let workHistoryData = try NSKeyedArchiver.archivedData(withRootObject: workHistory, requiringSecureCoding: false)
	        try container.encode(workHistoryData, forKey: .workHistory)
	    }

	    init(from decoder: Decoder) throws {
	        let container = try decoder.container(keyedBy: CodingKeys.self)
	        name = try container.decode(String.self, forKey: .name)
	        let workHistoryData = try container.decode(Data.self, forKey: .workHistory)
	        workHistory = try NSKeyedUnarchiver.unarchiveTopLevelObjectWithData(workHistoryData) as! WorkHistory
	    }
	}
```

## Other formats

Of course JSON and Plist are not the only formats available out there. So there are some other serialization approach specified for different needs

For platform-agnostic data exchange over the network there are some alternatives like [Protobuf](https://developers.google.com/protocol-buffers/) or [MessagePack](https://msgpack.org/) which claimed to be better in various different metrics (protobuf even has [an official Swift library](https://github.com/apple/swift-protobuf)).

For local data persistence there is [FastCoding](https://github.com/nicklockwood/FastCoding).

There also are some other popular protocols which are not ported to Cocoa platforms yet like [Apache Thrift](https://thrift.apache.org/) from Facebook or [FlatBuffers](https://google.github.io/flatbuffers/) from Google.

Personally I didn’t have a chance to use any of them in production so I just mention them as possible alternatives. You can find a lot of information about the formats, libraries to handle them and use cases where it makes sense to look into these alternatives.

## Encoding complex object graph

Quite frequent use case for serialization is persistence of complex object graphs on disk. (For example [inthisNSHipster’s post](https://nshipster.com/nscoding/) Mattt considers NSCoding/NSKeyedArchiver as a valid alternative to Core Data.)

All the mentioned encoders easily handle not only simple objects, but also quite a complex object trees with lots of nested items. But if you want to encode a complex graph it’s another task with several additional complications.

Talking about “complex graphs”; I mean a structure which has at least one of these conditions:

- Several references to one object![img](https://www.dmtopolog.com/images-posts/2019-01-14-object-serialization-in-ios/Graph_severalReferences.png)
- Cyclic references between objects![img](https://www.dmtopolog.com/images-posts/2019-01-14-object-serialization-in-ios/Graph_cyclicReferences.png)

If the graph has several references to the same object inside itself the problem is to consider uniqueness of the object. The encoder has to have some table of occurred object for not to encode the same object several times. If an object is passed to the encoder more than once it should encode only a reference. Eventually when the archive is decoded back and the graph is rebuild there should be references to the same instance, but not several different objects.

If the graph has objects referencing each other, it’s tricky for the encoder. If it doesn’t consider this possible cycles and blindly follows every link of every object it would go to an infinite loop.

Another complication for complex structures is that it might be not always appropriate to archive the entire graph. A good example is a view hierarchy. A view has many links to other objects: models, subviews, superviews, formatters, targets, gesture recognisers, and so on. If the view encoded all of its references to these objects, the entire application would get pulled in. Some objects are more important than others, though. A view’s subviews always should be archived, but not necessarily its superview. In this case, the superview is considered an extraneous part of the graph; a view can exist without its superview, but not its subviews. A view, however, needs to keep a reference to its superview, if the superview is also being encoded in the archive.

All this problems are considered in NSCoder’s API and its concrete implementation - NSKeyedArchiver. Uniqueness of the objects is controlled within the context of one graph encoding which is opened by calling `encodeRootObject:`. Multiple and circular references are also being properly managed: no infinite loops and only one instance of each object.

NSKeyedArchiver has a set of other useful things like default values for missing keys, type coercions or an ability to substitute objects. You can read more of that here (https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Archiving/Articles/archives.html)

Unfortunately most of that NSKeyedArchiver’s benefits work only for objects adopting NSCoding protocol. Neither reference control nor cyclic dependencies resolving doesn’t work for Codable.

Moreover Codable models behave the same with JSONEncoder and PropertyListEncoder, having the same issues if talking about complex graphs. So currently for Codable there is no built-in solution for this problem.

Swift team assured that this functionality was in the plan for Codable from the beginning, but they didn’t have time to accomplish it in time for Swift 4 release. The main difficulty has to do with Swift initialisation process which is much stricter that ObjC’s one. It’s still an ongoing discussion and work in progress and hopefully we will see some solution in the standard library soon.

Until then you have several options for encoding/decoding complex graph structures in Swift:

1. Use NSKeyedArchiver + NSCoding. Good old ObjC APIs work smoothly from Swift.
2. Implement an additional compatibility layer between the calling code and the actual encoders. Create a `reference table` for checking an identity of the encoding objects.
3. Implement your own encoder/decoder pair. It’s the most time consuming option, but this way eventually you can use synthesised implementation for actual encoding/decoding in your models.
4. You can use some third party solutions. The problem is common enough so there are already some attempts to implement a solution. (<https://github.com/BigZaphod/Archivable>, <https://github.com/cherrywoods/swift-meta-serialization>)

There are couple of very informative discussion on the matter in swift forum [here](https://forums.swift.org/t/codable-with-references/13885) and [here](https://forums.swift.org/t/codable-archivable/11414).

## Codable as a replacement for NSCoding

*(as a conclusion)*

Obviously Codable is the future for serialization in iOS/macOS platforms. So can we consider NSCoding as a legacy API and forget about it when considering Swift?

Codable (with Coder protocol) came from NSCoding (with NSCoder abstract class) and inherited its general mechanics. We pass an encoder into encode() method of the model and the model translates itself into a number of key-value pairs which it passes to the encoder. But Codable is different in amount of use cases it covers.

In the beginning we pointed out that even before Codable we should have distinguished serialization from archiving, but the only way we utilise NSCoding is data persistence. NSKeyedArchiever which is used together with NSCoding serialises data in a format which makes it almost impossible to deserialise the data in another ecosystem (without NSKeyedUnarchiver and the same data model as was used for encoding). NSCoding has nothing to do with JSON or plist encoding/decoding. So the only practical approach is to persist data between app launches.

Codable introduces a completely different approach. Codable implementation may be used for serialising objects not only to JSON or plist, but to some custom formats as well. It decouples serialization from archiving and makes it more independent. In this regard multipurpose Codable has more value than NSCoding.

NSCoding has its own benefits. lets mention them one more time.

You can use NSCoding in both languages. In mixed ObjC/Swift projects when legacy models are written in ObjC it doesn’t matter if you write all the new code in Swift - you still cannot build general encoding mechanism (working for both languages) based on Codable.

The second point is wide adoption of NSCoding in Apple frameworks. All of the Foundation value objects and most of the Application Kit and UIKit user interface objects adopt NSCoding. The [list of classes adopting Codable](https://github.com/apple/swift-evolution/blob/master/proposals/0167-swift-encoders.md#foundation-types-adopting-codable) now is not so big. So it might cost you quite an effort to write extensions for classes you want to serialise/archive (for instance if you want to persist your app’s view hierarchy).

For serialising complex object graphs you cannot use pure Swift and Codable without an additional harness. But you can easily do it with NSCoding.

Saying that I don’t think that Codable can completely replace NSCoding in our code, at least now. You can leverage its benefits for some use cases like JSON encoding or serialization of simple structures to store them on disk. But there are still set of use cases when NSCoding is a better approach. Our job is to know the peculiarities and restrictions of both this approaches and use them in appropriate situations.

*(One more interesting case on the topic can be found in this post: Serialization of enum with associated type)*