# URLSession

<br>

## Declaration

```swift
class URLSession : NSObject
```

<br>

## Overview

`URLSession` 클래스 와 관련된 클래스들은 데이터 다운로딩, 데이터 업로딩 API를 제공합니다.

게다가 앱이 동작중이 아니거나 정지된 상태일지라도, API는 앱의 백그라운드 다운로드를 가능하게 합니다. 

리다이렉션이벤트를 알리거나, 인증을 지원하는 델리게이트 메소드들의 집합입니다.

<br>



> **Important**
>
> `URLSession` API 는 많은 다른 클래스들과 함께 동작한다. 이 동작은 매우 복잡해서, 문서를 읽지 않는 다면 명확하게 이해 할수 없습니다.  API 사용전에,  [URL Loading System](https://developer.apple.com/documentation/foundation/url_loading_system)  에 대해 읽자. 이 아티클의  [First Steps](https://developer.apple.com/documentation/foundation/url_loading_system#2878017), [Uploading](https://developer.apple.com/documentation/foundation/url_loading_system#3038363),  [Downloading](https://developer.apple.com/documentation/foundation/url_loading_system#3038364) 은 `URLSession` 의 일반적인 동작들을 수행하는 예제를 제공합니다.



`URLSession`API를 사용함으로써, 앱은 하나또는 그이상의 세션을 만든다. 관련된 데이터를 전송하는 작업들의 그룹을 조정합니다.

예를 들어, 만약 우리가 웹브라우저를 생성한다면, 앱은 하나의 새션을 탭별로 생성해야할 것입니다. 또는 백그라운드 다운로드들을 위한 다른 세션과 하나의 상호작용용 세션을 만들 수도 있습니다.

각각의 세션은 작업의 연속을 더하고, 각각은 특정한 URL에 대한 요청을 표현한다.

<br><br>

### Types of URL Sessions



정해진 URL세션 안의 작업들은 일반적인 세션 설정 객체를 공유합니다.

세션설정 객체는 연결, 단일 호스트에서 최대 동시 연결 수, 셀룰러 네트워크에 대해서 연결을 허용할 것인지를 나타내는 객체입니다.

`URLSession` 은 어떠한 설정도 없이 사용하는 기본적인 요청을 위한 싱글톤 프로퍼티  `shared` 을 가지고 있습니다.

직접 생성한 객체만큼 커스터마이징을 할수는 없습니다. 그러나 만약 요구사항 많이 제한되어 있다면,  좋은 출발점이 될 수 있습니다. 우리는 이 세션에 `shared` 클래스 메소드를 호출함으로서 접근할 수 있습니다.

> `default` ,`ephemeral`,`background` 세션은 `URLSessionConfigure`를 이용해서 생성할수 있는 `URLSession`입니다.

- `default` 세션은 shared세션과 거의 동일하게 행동합니다. 그러나 이 세션은 더 많은 설정과 델리게이트를 통해 점직적으로 많은 데이터를 얻는 것을 가능하게합니다.
- `Ephemeral` 세션은 shared 세션과 거의 비슷합니다. 그러나 **캐시나 쿠키 또는 증명서같은것을 디스크에 쓰지 않습니다.** (Ephemeral : 일시적인)
- `Background`세션은 앱이 동작중이 아닌 중에도 우리에게 컨텐트의 **업로드, 다운로드를 백그라운드에서 수행**할 수 있게 해줍니다. 

See [Creating a Session Configuration Object](https://developer.apple.com/documentation/foundation/urlsessionconfiguration#1660412) in the [`URLSessionConfiguration`](https://developer.apple.com/documentation/foundation/urlsessionconfiguration) class for details on creating each type of configuration.

<br>

<br>

### Types of URL Session Tasks



세션 안에서, 우리는 데이터를 서버로 업로드하고, 그 후 서버로 부터 데이터를 디스크의 파일 또는 메모리 내의 하나 또는 여러개의 `NSData` 객체를 가져오는 가져오는 작업을 만듭니다.

 `URLSession`API는 작업의 타입:



- `Data Task` 는 `NSData` 객체를 이용해서 데이터를 보내고 받아옵니다. `Data Task` 는 서버와 **자주 짧게 대화식으로 요청하는 데에 적절합니다.**
- `Upload Task`는 `Data Task` 와 유사합니다 .그러나  **파일 형태의 데이터를 보내고, 또 앱이 동작중이 아닐 지라도  백그라운드 업로드를 지원합니다**
- `Download Task` 는 파일형태의 데이터흘 가져옵니다. 그리고 앱이 동작중이 아닐 지라도 백그라운드 업로드와 다운로드를 지원합니다. 

<br>

### Using a Session Delegate

세션안의 작업들은 또한 우리에게 발생한 **다양한 이벤트(인증의 실패, 서버로부터의 데이터 도착, 데이터가 캐시되어졌을때, 등등) 의 정보를 제공해주는 델리게이트를 공유**합니다.

만약 우리가 어떠한 기능도 필요로 하지않다면 우리는 세션의 생성자에 `nil`을 전송함으로써, 아무 기능도 사용하지 않을 수 있습니다.



>  **Important**
>
> 세션 객체는 앱이 죽거나, 또는 명시적으로 세션을 없애지 않는 한  델리게이트를 강하게 참조합니다. 
>
> 만약 세션을 무효화하지 않는다면, 앱은 메모리 누수에 빠질 것입니다.



<br>

### Asynchronicity and URL Sessions

대부분의 네트워킹 API 처럼, `URLSession` API 도 비동기적이다. 

그래서 우리가 호출한 메소드에 따라서 다른 방법으로 데이터를 반환합니다. 

- By calling a completion handler block when a transfer finishes successfully or with an error.

- 성공적으로 전송이 완료되거나, 에러가 있을 때, **`completion handler` 를 호출!**

- 전송에 성공했거나, 데이터를 받았을 때, 세션의 **델리게이트 메소드를 호출!**

  

델리게이트에 정보를 전달하는 것 이외에도, 

URLSession API는 만약 **작업의 현재 상태**를 기반으로 프로그래밍적 결정을 만들기위한 질의를 지원하는 **progress, status** 프로퍼티를 제공합니다. 

URLSession들은 취소, 재시작, 재개, 작업 중지를 지원합니다. 그리고 중지되거나 취소되거나 실패한 다운로드에 대해 재개할 수 있는 능력또한 제공합니다.

<br><br>

### Protocol Support



`URLSession` 은 기본적으로 `data`, `file`, `ftp`,  `http`, `https` URL스키마 를 지원합니다. 

`URLSession` supports the HTTP/1.1 and HTTP/2 protocols. HTTP/2 support, as described by [RFC 7540](https://tools.ietf.org/html/rfc7540), requires a server supporting Application-Layer Protocol Negotiation (ALPN).

You can also add support for your own custom networking protocols and URL schemes (for your app’s private use) by subclassing `URLProtocol`.

<br><br>

### App Transport Security (ATS)

Starting in iOS 9.0 and OS X 10.11, a new security feature called App Transport Security (ATS) is enabled by default for all HTTP connections made with `URLSession`. ATS requires that HTTP connections use HTTPS ([RFC 2818](https://tools.ietf.org/html/rfc2818)).

For more information, see [NSAppTransportSecurity](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33) in the [Information Property List Key Reference](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009247).

<br><br>

### NSCopying Behavior

Session and task objects conform to the [`NSCopying`](https://developer.apple.com/documentation/foundation/nscopying) protocol as follows:

- When your app copies a session or task object, you get the same object back.
- When your app copies a configuration object, you get a new copy that you can independently modify.

<br><br>

### Thread Safety

URLSession 의 API 는 자체적으로 충분히 thread-safe 하다. 만약 우리가 session과 작업을 자유롭게 어떤 thread 에든 생성할 수 있다. 우리의 델리게이트 메소드가 제공된 `completion handler`를 호출할 때, 이 작업은 **현재 델리게이트 큐에서 자동으로 스케줄링된다.** 

> 델리게이트 큐에서 자동으로 스케쥴링해서 작업이 안전하게 가능하다.



> **Warning**
>
> 시스템은 세션 델리게이트 메소드 [`urlSessionDidFinishEvents(forBackgroundURLSession:)`](https://developer.apple.com/documentation/foundation/urlsessiondelegate/1617185-urlsessiondidfinishevents) 을 보조스레드에서 호출할 수 있습니다.
>
> 그러나  앱 델리게이트 메소드[`application(_:handleEventsForBackgroundURLSession:completionHandler:)`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622941-application) 에서 제공된 completion handler를 호출하는 메소드를 구현해해야할 수도 있습니다.
>
> 이 경우 무조건 메인스레드에서 completion handler를 호출하는 걸 잊지말아야합니다.

