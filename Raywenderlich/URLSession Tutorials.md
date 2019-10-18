# URLSession Tutorial: Getting Started

https://www.raywenderlich.com/3244963-urlsession-tutorial-getting-started



서버로 부터 데이터를 가져오거나 우리의 소셜 미디어 상태를 업데이트하거나, 디스크로 리모트파일을 다운로드하는 작업 모두,  네트워크요청은 마법을 만들어낸다. 

네트워크 리퀘스트를 위한 많은 요구사항을 돕기위해서, 애플은 컨텐트를 업로드하고 다운로드하기위한 네트워킹 API를 가진`URLSession` 을 제공한다. 



이 튜토리얼에서, 우리는 `Half Tunes` 을 만드는 법을 배울 것이다. 이 앱은 **iTunes Search API**를 이용해서 30초 미리듣기를 다운로드 한다.  그리고 이 앱이 백그라운드 작업을 지원하고, 유저의 일시정지, 취소 , 재개를 제공한다.



## Getting Started



스타터 프로젝트는 음악 검색과 검색결과를 보여주는 UI와 ,네트워킹 클래스,  트랙을 플레이하고 저장하기위한 도우미 메소드들을 포함하고 있다.

프로젝트를 빌드하고 동작시키자. 우리는 상단에 서치바를 가진 뷰 , 빈 테이블 뷰를 볼 것이다.

![Half Tune App's Starter Screen](https://koenig-media.raywenderlich.com/uploads/2019/05/01-Initial-App-281x500.png)

서치바 안에 쿼리를 타이핑하고 키보드의  **search** 버튼을 탭하자.

뷰는 여전히 비어있다. 우리는 `URLSession`를 이용해서 완성할 것이다.

<br>



## URLSession Overview



시작하기 전에, `URLSession` 과 구성하는 클래스 에 대해서 이해하는 것이 중요하다. 

그러니 퀵 오버뷰를 보자. 



`URLSession` is both a class and a suite of classes for handling HTTP- and HTTPS-based requests:

`URLSession`은 HTTP/ HTTPS 기반의 요청들을 처리하기 위한 클래스 및 클래스의 집합입니다.

![URLSession Diagram](https://koenig-media.raywenderlich.com/uploads/2019/05/02-URLSession-Diagram-650x432.png)

`URLSession` is the key object responsible for sending and receiving requests. 

`URLSession`  은 리퀘스트를 보내고 받는 것을 책임지는 중요한 객체입니다.

우리는 `URLSessionConfiguration`을 이용해서 3가지의 세션을 생성할 수 있다.

- *default*: **디스크에 영속된 전역 캐시를 이용**하고, 인증서와 쿠키 저장소 객체를 사용하는 세션 객체

- *ephemeral*: 세션과 관련된 정보를 **메모리안에 저장**하는 것을 제외하고는, default와 비슷하다.  비공개 세션 정도로 생각하면 됩니다.

- *background*: 세션이 백그라운드에서 다운로드나 업로드를 수행하게 해줍니다. 앱이 중지되거나 시스템으로 부터 종료되어도 전송은 계속됩니다.

  

`URLSessionConfigureation` 은 또 우리에게 세션의 **timeout 값, 캐시정책, htttp 헤더 와 같은 속성들을 설정**하게 해줍니다.

`URLSessionTask` 는 작업을 나타내는 추상 클래스이다. 세션은 파일을 업로드, 다운로드 하거나 데이터를 가져오는 실제 작업을 하는 하나 또는 여러개의 task를 생성한다.

<br>

### Understanding Session Task Types

3가지 타입의 SessionTask 의 구체타입이 존재한다.

- *URLSessionDataTask*:  서버로부터 데이터를 가져와서 메모리에 저장하는 GET 요청에 대한 작업에 사용한다. 
- *URLSessionUploadTask*: POST 또는 PUT 메소드를 통해 디스크로부터 웹 서비스로 파일을 업로드하는 작업에서 사용한다.
- *URLSessionDownloadTask*:  원격 서비스로부터 파일을 임시 파일 위치로 다운로드하는 작업에 사용한다.

![URLSession Task Types](https://koenig-media.raywenderlich.com/uploads/2019/05/03-Session-Tasks.png)

<br>우리는 또 작업을 멈추거나 취소하거나 다시 재개할 수 있다. `URLSessionDownloadTask`는 미래의 재개를 위한 중지 기능도 가지고 있다.

Generally, `URLSession` returns data in two ways: 

일반적으로 `URLSession` 은 두가지 방법으로 데이터를 반환한다.



- 작업이 성공적으로 끝나거나 에러를 가졌을 때, Completion handler를 통해서
- 세션이 만들어 졌을 때 설정한 델리게이트에 메소드의 호출을 통해



이제는 `URLSession` 이론을 실제로 적용해보자. 

![Putting Theory Into Practice](https://koenig-media.raywenderlich.com/uploads/2019/05/swift-gears-320x320.png)

## DataTask and DownloadTask

우리는 사용자의 검색 조건을 위해   iTunes Search API 에 질의를 하는 data task를 만듬으로써 시작할 것이다.

*SearchViewController.swift*  파일 안에 `searchBarSearchButtonClicked` 는 스테이터스바에 네트워크 인디케이터를 사용해서, 네트워크 작업중을 보여줄 것이다. 또 우리는 네트워크 리퀘스트를 만들자 마자  *QueryService.swift* 안에서 처리하는 `getSearchResults(searchTerm: completion:)` 을 이후에 호출한다.`

In *QueryService.swift*, replace `// TODO 1` with the following:

```swift
let defaultSession = URLSession(configuration: .default)
```

And `// TODO 2` with:

```swift
var dataTask: URLSessionDataTask?
```

Here’s what you’ve done:

우리가 아래의 사항을 한 것이다: 

1. default session 을 생성한다.

2. API로 GET요청을 만들기 위해서 우리가 사용할 URLSessionDataTask 를 정의했다. 이 data task 는 새로운 검색 문자열이 들어올 때마다 다시 초기화될 것이다. 

   <br>

이후,  `getSearchResults(searchTerm:completion:)` 메소드 내부의 컨텐트를 아래에 맡게 변경하자: 

```swift
// 1
dataTask?.cancel()
    
// 2
if var urlComponents = URLComponents(string: "https://itunes.apple.com/search") {
  urlComponents.query = "media=music&entity=song&term=\(searchTerm)"      
  // 3
  guard let url = urlComponents.url else {
    return
  }
  // 4
  dataTask = 
    defaultSession.dataTask(with: url) { [weak self] data, response, error in 
    defer {
      self?.dataTask = nil
    }
    // 5
    if let error = error {
      self?.errorMessage += "DataTask error: " + 
                              error.localizedDescription + "\n"
    } else if 
      let data = data,
      let response = response as? HTTPURLResponse,
      response.statusCode == 200 {       
      self?.updateSearchResults(data)
      // 6
      DispatchQueue.man.async {
        completion(self?.tracks, self?.errorMessage ?? "")
      }
    }
  }
  // 7
  dataTask?.resume()
}
```

<br>

각각의 번호가 붙여진 코멘트는 각각의 턴에 취한 행동이다:

1. 새로운 유저의 쿼리를 위해서, 우리는 이미 진행되던 dataTask를 취소합니다. 왜냐하면 우리는 dataTask객체를 새로운 쿼리를 위해 재사용하기를 원하기 때문입니다.
2. query URL안에 유저의 검색 문자열을 포함하기 위해서, 우리는 API 베이스 URL로 `URLComponents`를 만듭니다. 그 후 , 쿼리 스트링을 설정합니다.
3.  `urlComponents`  의 `url`  프로퍼티는 옵셔널이므로 , 안전하게 언래핑합니다.
4. 우리가 생성한 세션으로 부터 `URLSessionDataTask` 를 생성합니다 .  그리고 작업이 끝났을 때 호출할 completion handler를 함께 전달합니다.
5. 만약 레퀘스트가 성공적이라면, 우리는 `data` 를 응답 `tracks` 배열로 파싱하는 도우미 메소드 `updateSearchResults` 를 호출합니다. 
6. main queue로 전환합니다. completion handler에게 `tracks` 를 전달하기 위해서
7. 모든 `Task`는 기본적으로 중지 상태로 시작합니다. `resume()` 을 호출해서 `data Task`를 시작합니다.

<br>

`SearchViewController` 내에, completion closure를 보자.  네트워크 인디케이터를 숨긴 다음, `searchResults` 안의에`results` 를  저장한다음  테이블 뷰를 업데이트합니다.

*Note*:  기본 리퀘스트 메소드는 GET 이다. 만약 POST, PUT , DELETE를 원한다면 `URLRequest.HTTPMethod`프로퍼티를 설정한다음 `dataTask`를 생성합니다. 



<br>

앱을 빌드후 동작해보자. 어떤 곡에대해 검색하자 그러면 테이블 뷰에 관련된 트랙 결과들이 드러날 것입니다.

![Half Tunes Screen With Relevant Track Results](https://koenig-media.raywenderlich.com/uploads/2019/05/04-Search-281x500.png)

이제 Half Tunes는 약간 동작합니다!

곡들의 검색결과를 아주 나이스하게 보여줍니다. 그러나 우리가 만약 download하기 위해서 탭해도 아무일 이 일어나지 않는다.

이것은 우리의 다음 작업이다. 우리는 음악의 스니펫을 로컬 파일로 저장하기 쉽게  ` download task` 를 사용할 것입니다. 

<br>

### Downloading Classes



우리가 여러개의 다운로드를 다루기 위해 필요한 1번째는 다운로드 활성상태 유지하기 위한 사용자 정의 객체입니다.

새로운 Download.swift 파일을 Model 폴더 내에 생성하자.

파일을 열고, 아래의 구현을 추가하자.

```swift
class Download {
  var isDownloading = false
  var progress: Float = 0
  var resumeData: Data?
  var task: URLSessionDownloadTask?
  var track: Track
  
  init(track: Track) {
    self.track = track
  }
}
```

Here’s a rundown of the properties of `Download`:

- *isDownloading*: 다운로드가 정지중인지 진행중인지 표현
- *progress*: 0.0 ~ 1.0 사이의 다운로드 진행상태를 보여주는 변수
- *resumeData*:  사용자가 download task 를 중지했을 때,  이태껏 생성된 `Data` 를 저장한다. 만약 호스트 서버가 지원한다면 중지된 다운로드를 제개할수 있습니다.
- *task*:  트랙을 다운로드하는  `URLSessionDownloadTask` 
- *track*: 다운로드 할 대상 트랙, 이 트랙의 URL은 Download 객체의 식별자이다. 

<br>

Next, in *DownloadService.swift*, replace `// TODO 4` with the following property:

```swift
var activeDownloads: [URL: Download] = [:]
```

This dictionary will maintain a mapping between a URL and its active `Download`, if any.

이 딕셔너리는 `URL`과 진행중인 `Download`를 매핑한다.

<br>

## URLSession Delegates

우리는 download task 를 completion handler 와 함께 생성할 수 있다.  튜토리얼의 뒷부분에 우리는 다운로드 진행상태를 업데이트하고 검사할 것이다.  그러므로 우리는 사용자 정의 델리게이트가 필요합니다.



몇몇 session delegate에 관련된 프로토콜이 있습니다. 

`URLSessionDownloadDeleagte` 는 download task 에 대한 이벤트를 처리합니다.

우리는 `SearchViewController` 를 session delegate 로 설정할 필요가 있습니다. 그래서 지금 우리는 session delegate 프로토콜을 확장할 것입니다.



*SearchViewController.swift* 를 열고 아래와 같이 확장하자.

```swift
extension SearchViewController: URLSessionDownloadDelegate {
  func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask,
                  didFinishDownloadingTo location: URL) {
    print("Finished downloading to \(location).")
  } 
}
```



`URLSessionDownloadDelegate` 메소드는 다운로드가 끝났을 때 알리는  `urlSession(_:downloadTask:didFinishDownloadingTo:)`  메소드 단 하나만이 required 구현 메소드이다.

<br><br>

### Downloading a Track



모든 준비 작업이 끝났으므로 이제 파일 다운로드를 시작할 수 있습니다. 

우리의 첫번째 단계는 download task 를 다루는 session 을 만드는 것입니다.

In *SearchViewController.swift*, replace `// TODO 6` with the following code:

```swift
lazy var downloadsSession: URLSession = {
  let configuration = URLSessionConfiguration.default
  
  return URLSession(configuration: configuration, 
                    delegate: self, 
                    delegateQueue: nil)
}()
```



여기, 우리는 기본 설정을 가진 session을 초기화하고 이 세션의 이벤트를 받게하는 delegate 를 지정했다. 

task의 진행상항을 모니터하기에 매우 유용하다.



큐는 nil로 설정하면 은 serial operation queue를 만들어서 completion handler와 delegate 메소드를 모두 수행한다.



Now replace `// TODO 7` at the end of `viewDidLoad()` with the following line:

```swift
downloadService.downloadsSession = downloadsSession
```

`DownloadService` 의 `downloadsSession` 프로퍼티를 우리가 정의한 세션으로 설정하자.

설정된 delegate와 session으로, 사용자가 트랙의 다운로드를 원할 때 우리는 download task 를 만들 준비가 마침내 끝났다. 

<br>

In *DownloadService.swift*, replace the content of `startDownload(_:)` with the following implementation:

```swift
// 1
let download = Download(track: track)
// 2
download.task = downloadsSession.downloadTask(with: track.previewURL)
// 3
download.task?.resume()
// 4
download.isDownloading = true
// 5
activeDownloads[download.track.previewURL] = download
```

사용자가 테이블 뷰 셀의 다운로드 버튼을 탭하면, `TrackCellDeleagate` 가 cell로 `Track` 을 식별해내고, 그 `Track` 에 대한 `startDownload(_ :)` 를 실행한다.

<br>

 `startDownload(_:)` 안을 한번 들여다보자

1.  `Track` 으로 `Download` 객체를 생성한다.
2. 설정된 세션 객체를 사용해서, `URLSessionDownloadTask` 를 생성한다. 그 후 `Download.task` 프로퍼티에  설정한다.
3. `resume` 을 통해 task 를 실행한다.
4. 다운로드가 진행중이라는 것을 표시한다.
5. 마지막으로 `activeDownloads` 안에 `URL` 과 `Download` 객체를 .



<br>

앱을 빌드후 실행하고, 셀의 다운로드 버튼을 누르면 아래와 같은 출력문을 볼 수 있다.

```shell
Finished downloading to file:///Users/mymac/Library/Developer/CoreSimulator/Devices/74A1CE9B-7C49-46CA-9390-3B8198594088/data/Containers/Data/Application/FF0D263D-4F1D-4305-B98B-85B6F0ECFE16/tmp/CFNetworkDownload_BsbzIk.tmp.
```

The Download button is still showing, but you’ll fix that soon. First, you want to play some tunes!

### Saving and Playing the Track

When a download task completes, `urlSession(_:downloadTask:didFinishDownloadingTo:)`provides a URL to the temporary file location, as you saw in the print message. 

download task 가 끝났을 때, `urlSession(_:downloadTask:didFinishDownloadingTo:)` 메소드는 URL을 임시파일 영역에 저장한다. 

우리의 작업은 이것을 앱 샌드박스 컨테이너 디렉터리 영구적인 영역에 옮기는 것이다. 이 메소드가 리턴되기 전에.

<br>

In *SearchViewController.swift*, replace the print statement in `urlSession(_:downloadTask:didFinishDownloadingTo:)` with the following code:

```swift
// 1
guard let sourceURL = downloadTask.originalRequest?.url else {
  return
}

let download = downloadService.activeDownloads[sourceURL]
downloadService.activeDownloads[sourceURL] = nil
// 2
let destinationURL = localFilePath(for: sourceURL)
print(destinationURL)
// 3
let fileManager = FileManager.default
try? fileManager.removeItem(at: destinationURL)

do {
  try fileManager.copyItem(at: location, to: destinationURL)
  download?.track.downloaded = true
} catch let error {
  print("Could not copy file to disk: \(error.localizedDescription)")
}
// 4
if let index = download?.track.index {
  DispatchQueue.main.async { [weak self] in
    self?.tableView.reloadRows(at: [IndexPath(row: index, section: 0)], 
                               with: .none)
  }
}
```

Here’s what you’re doing at each step:

1. `Task` 로 부터 원본 요청 URL을 추출해내고, `activeDownloads`에서 일치하는 `Download`를 찾고, 그것을 딕셔너리로 부터 지운다.
2. 그 후 URL을 앱의 Document 디렉터리의 경로에 `lastPathComponent`를 더함으로써 영속적으로 저장할 파일의 경로를 생성하는` localFilePath(for:) `메소드의 파라미터로 전달한다. 
3.  `fileManager` 를 이용해서, 우리는 다운로드된 파일을 임시 파일 위치로 부터 적절한 파일 위치로 이동 시킨다. 일단 작업을 복사하기 전에 해당 위치의 아이템을 지워준다. 그리고 우리는 `track`의 `downloaded`를 `true`로 설정해준다.  
4. 마지막으로, 우리는 download track의 `index` 속성과 일치하는 cell 을 reload 한다.

Build and run your project, run a query, then pick any track and download it. When the download has finished, you’ll see the file path location printed to your console:

프로젝트를 빌드하고 동작시켜보자. 트랙을 다운로드하면 그때 다운로드가 끝났을 때, 우리는 파일의 위치를 볼 수 있다.

```shell
file:///Users/mymac/Library/Developer/CoreSimulator/Devices/74A1CE9B-7C49-46CA-9390-3B8198594088/data/Containers/Data/Application/087C38CC-0CEB-4895-ADB6-F44D13C2CA5A/Documents/mzaf_2494277700123015788.plus.aac.p.m4a
```

다운로드 파일또한 사라진다. 왜냐하면 delegate 메소드 가 track의  `downloaded`  가 `true` 로 설정하기때문이다.

트랙을 탭하면 우리는 AVPlayerViewController를 통해 음악을 둘을 수 있다.


![Half Tunes App With Music Player Running](https://koenig-media.raywenderlich.com/uploads/2019/05/06-Playing-Download-281x500.png)

### Pausing, Resuming, and Canceling Downloads

사용자가 다운로드를 중지하거나 취소하는 것을 만약 원한다면? 

이 섹션에서는 다운로드 진행중에 우리는 중지, 재개, 그리고 취소 기능을 사용자가 이용할 수 있게 구현할 것이다.

일단 진행중인 다운로드를 추소하는 것부터 시작할 것이다.

<br><br>

### Canceling Downloads

In *DownloadService.swift*, add the following code inside `cancelDownload(_:)`:

```swift
guard let download = activeDownloads[track.previewURL] else {
  return
}

download.task?.cancel()
activeDownloads[track.previewURL] = nil
```

To cancel a download, you’ll retrieve the download task from the corresponding `Download` in the dictionary of active downloads and call `cancel()` on it to cancel the task. You’ll then remove the download object from the dictionary of active downloads.

다운로드를 취소하기 위해0서, 우리는 `activeDownloads` 딕셔너리에서 일치하는`Download`를 가져온 뒤,` cancel()` 을 호출함으로써, task를 취소했다.

<br><br>

### Pausing Downloads

우리의 다음 작업은 우리의 사용자가 다운로드를 일시정지하고 이후에 다시 마저 하는 것을 구현하는 것이다.

다운로드를 일시정지하는 것은 취소하는 것과 유사하다. 일시정지는 일단 download task를 취소한다.

그러나 호스트 서버가 제공한다면, 이후에 다운로드를 재개할 수 있는 충분한 정보들을 포함하는 resume data 를 제공한다. 

> *Note*: 우리는 단지 다운로드를 특정 상황에서만 재개할 수 있다. 예를 들어, 리소스가 첫 요청 이래로 변경되지 않았어야한다.



Replace the contents of `pauseDownload(_:)` with the following code:

```swift
guard
  let download = activeDownloads[track.previewURL],
  download.isDownloading 
  else {
    return
}

download.task?.cancel(byProducingResumeData: { data in
  download.resumeData = data
})

download.isDownloading = false
```

The key difference here is that you call `cancel(byProducingResumeData:)` instead of `cancel()`. You provide a closure parameter to this method, which lets you save the resume data to the appropriate `Download` for future resumption.

이곳에서의 중요한 차이점은 우리가 `cancel(byProductionResumeData:)` 를 `cancel()` 대신에 호출했다는 점이다. 

그리고 파라미터로  우리는 `resume data` 를 저장하게했다.

우리는 또한 isDownloading 을 `false` 로 설정해서, 우리의 작업이 중지 되었음을 표현했다.

일시 정지 기능이 완료 되었다. 이후 진행할 기능은 중지된 다운로드를 재개하는 것이다.

<br>

### Resuming Downloads

Replace the content of `resumeDownload(_:)` with the following code:

```swift
guard let download = activeDownloads[track.previewURL] else {
  return
}

if let resumeData = download.resumeData {
  download.task = downloadsSession.downloadTask(withResumeData: resumeData)
} else {
  download.task = downloadsSession
    .downloadTask(with: download.track.previewURL)
}

download.task?.resume()
download.isDownloading = true
```

사용자가 다운로드를 재개했을 때, 우리는 Download의 resumeData의 존재 에 대해 검사한다. 

만약 찾았다면, 우리는 새로운 download task 를 생성하고, `downloadTask(withResumeData:)`를 호출한다. 

만약 resume data 가 부재라면, 새로운 download task 를 url로 생성한다.

 `resume()`을 다시 호출하고`Download.isDownloading ` 를 다시  `true` 로 설정한다.

<br><br>



### Showing and Hiding the Pause/Resume and Cancel Buttons



우리는 일시정지/재개 그리고 취소 버츤을 적절하게 보여줄 필요가 있다.

TrackCell 의 configure 는 활성 다운로드를 가진다면 알려줄 필요가 있다. 



In *TrackCell.swift*, change `configure(track:downloaded:)` to `configure(track:downloaded:download:)`:

```swift
func configure(track: Track, downloaded: Bool, download: Download?) {
```

In *SearchViewController.swift*, fix the call in `tableView(_:cellForRowAt:)`:

```swift
cell.configure(track: track,
               downloaded: track.downloaded,
               download: downloadService.activeDownloads[track.previewURL])
```



Back in *TrackCell.swift*, locate `// TODO 14` in `configure(track:downloaded:download:)`and add the following property:

```swift
var showDownloadControls = false
```

Then replace `// TODO 15` with the following:

```swift
if let download = download {
  showDownloadControls = true
  let title = download.isDownloading ? "Pause" : "Resume"
  pauseButton.setTitle(title, for: .normal)
}
```

download 객체의 존재는 아예 새로운 다운이 아님을 의미합니다.

그래서 셀은 Pause/Resume 그리고 Cancel 을 보여줘야합니다. pause 와 resume 은 같은 버튼을 공유하므로, 우리는 이 두개의 상태를 적절하게 토글 할 것 입니다.



Below this if-closure, add the following code:

If-closure 아래에 해당 코드를 적자.

```swift
pauseButton.isHidden = !showDownloadControls
cancelButton.isHidden = !showDownloadControls
```

Here, you show the buttons for a cell only if a download is active.

우리는 셀에 다운로드가 활성화되면 해당 버튼을 보여줄 것이다.

마지막으로 아래의 라인으로 변경하자.

Finally, replace the last line of this method:

```swift
downloadButton.isHidden = downloaded
```

with the following code:

```swift
downloadButton.isHidden = downloaded || showDownloadControls
```

우리는 셀에게 만약 다운로드 진행중이라면, 다운로드 버튼을 보이지 않게끔 하게 만들었다.

프로젝트를 빌드하고 동작시켜보자. 그리고 몇개의 트랙을 동시에 다운 받으며, 우리는 정지 재개를 해보자.



![Half Tunes App Screen With Pause, Resume, and Cancel Options](https://koenig-media.raywenderlich.com/uploads/2019/05/09-Pausing-And-Resuming-281x500.png)

<br>

## Showing Download Progress

아직 다운로드의 진행상황를 보여주지 않는다. 사용자 경험을 좀 더 개선하기 위해서, 우리는 우리의 앱이 다운로드 진행상황 이벤트를 들을 수 있게 하고, 그 진행상황을 셀에 보여주게 수정할 것이다.

session delegate 메소드는 이 작업에 완전 걸맞다.



First, in *TrackCell.swift*, replace `// TODO 16` with the following helper method:

```swift
func updateDisplay(progress: Float, totalSize : String) {
  progressView.progress = progress
  progressLabel.text = String(format: "%.1f%% of %@", progress * 100, totalSize)
}
```

The track cell has `progressView` and `progressLabel` outlets. The delegate method will call this helper method to set their values.

트랙 셀은 `progressView` 와 `progressLabel ` 아울렛을 소유하고 있다. 

delegate 메소드는 아울렛들의 값을 설정하는 도우미 메소드를 호출할것이다.



Next, in *SearchViewController.swift*, add the following delegate method to the `URLSessionDownloadDelegate` extension:

```swift
func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask,
                  didWriteData bytesWritten: Int64, totalBytesWritten: Int64,
                  totalBytesExpectedToWrite: Int64) {
  // 1
  guard
    let url = downloadTask.originalRequest?.url,
    let download = downloadService.activeDownloads[url]  
    else {
      return
  }
  // 2
  download.progress = 
    Float(totalBytesWritten) / Float(totalBytesExpectedToWrite)
  // 3
  let totalSize = 
    ByteCountFormatter.string(fromByteCount: totalBytesExpectedToWrite, 
                              countStyle: .file) 
  // 4
  DispatchQueue.main.async {
    if let trackCell = 
      self.tableView.cellForRow(at: IndexPath(row: download.track.index,
                                              section: 0)) as? TrackCell {
      trackCell.updateDisplay(progress: download.progress, 
                              totalSize: totalSize)
    }
  }
}
```

Looking through this delegate method, step-by-step:

1.  제공된 `downloadTask` 의 URL을 추출하고, 그것을 일치하는 Download를 찾기 위해서 사용한다.
2. 이 메소드는 또한 우리가 썼거나, 쓰기위해서 추출한 전체 바이트들을 제공한다. 우리는 Download의 결과를 그 두개의 배율로써 진행 상황을 계산한다. 그리고 TrackCell은 그 값을 사용해서 progressView를 업데이트 할 것이다.
3. `ByteCountFormatter` 는 하나의 바이트 값을 취하고, 사람이 파일의 전체 사이지를 보여주는 읽을 수 있는 형태의 문자열을 생성한다. 우리는 이 문자열을 완료 퍼센테이지 옆에 보여줄 것이다.
4. 마지막으로, 우리는 Track을 디스플레이하는 책임을 지는 셀을 찾고, 이전 단계로 부터 더 나아간 값을 가진 progress view, progress label 을 업데이트 하는 도우미 메소드를 호출한다.  이건 UI와 관련된 로직이라서, main queue에서 일어나야한다.

<br>

### Displaying the Download’s Progress

Now, update the cell’s configuration to display the progress view and status when a download is in progress.

이제, 다운로드 중일 때, progress view 와 statis 를 보여주는 셀의 설정을 업데이트하자.

Open *TrackCell.swift*. In `configure(track:downloaded:download:)`, add the following line inside the if-closure, after the pause button title is set: 

```swift
progressLabel.text = download.isDownloading ? "Downloading..." : "Paused"

```

다운로드가 일시정지 중이거나, delegate 메소드로 부터 첫 업데이트 이전에는 cell 에게 보여줄 것을 주자.

Now, add the following code *below* the if-closure, below the `isHidden` lines for the two buttons:

```swift
progressView.isHidden = !showDownloadControls
progressLabel.isHidden = !showDownloadControls
```

progress view 와 progress label 은 오직 다운로드가 진행중일 때만 보여준다.

<br>

프로젝트를 빌드하고 동작시켜보자. 그리고 트랙을 다운로드하고 progress bar 상태 업데이트를 구경하자.

![Half Tunes App with Download Progress Features](https://koenig-media.raywenderlich.com/uploads/2019/05/10-Download-Progress-281x500.png)



## Enabling Background Transfers

이제 우리의 앱은 꽤나 유용하다, 그러나 백그라운드 전송은 큰 강화요소가 될 것이다.

앱이 백그라운드 상태이거나 또는 만약 어떠한 이유로 크러시가 나도 다운로드를 계속한다.

이렇게 작은 노래 스니펫에 대해서는 필수가 아닐 수도 있다. 그러나 앱이 큰 파일들을 전송한다면 적절한 기능이 될 것이다.

그러나 만약 우리의 앱이 동작하지 않는 다면, 어떻게 작업을 할 수 있을까?



The OS runs a separate daemon outside the app to manage background transfer tasks, and it sends the appropriate delegate messages to the app as the download tasks run.

운영체제가 백그라운드 전송 작업을 관리하기 위해서 앱의 외부에서 데몬을 동작시킨다. 그리고 적절한 delegate 메시지를 다운로드 작업이 동작하게 앱에 계속해서 보낸다.



전송이 활성화인 동안 앱이 종료되는 이벤트에도 영향을 받지 않고 이 작업은 계속 동작할 것이다.

작업이 완료되었을 때,  데몬은 다시 앱을 백그라운드로 런칭할 것이다.  

다시 런칭된 앱은 관련된 completion delegate 메소드를 수행하기 위해서 백그라운드 세션을 다시 생성한다.

디스크에 다운로드된 파일을 저장하는 등의 관련작업을 수행한다.



>  *Note*: 만약 사용자가 앱을 강제로 종료한다면, 시스템은 세션의 백그라운드 전송과 앱의 재시동을 취소시켜버릴 것이다.



In *SearchViewController.swift*, in the initialization of `downloadsSession`, find the following line of code:

```swift
let configuration = URLSessionConfiguration.default
```

…and replace it with the following line:

```swift
let configuration = 
  URLSessionConfiguration.background(withIdentifier:
                                       "com.raywenderlich.HalfTunes.bgSession")
```



`default session` 을 사용하는 대신 , `background session` 을 사용할 것이다. 만약 필요하다면, 앱이 새로운 백그라운드 세션을 생성하게 해주는 session에 유일한 아이디를 설정해주는 것은 중요하다.

> *Note*:  우리는 백그라운드 session 설정으로  1개 이상의 session을 만들어서는 안된다.
>
> 왜냐하면, 시스템은 그 세션과 관련된 작업의 아이디를 사용하기때문이다.

<br><br>

### Relaunching Your App

If a background task completes when the app isn’t running, the app will relaunch in the background. You’ll need to handle this event from your app delegate.

만약 백그라운드 작업이 앱이 동작중이지 않을 때 완료된다면, 앱은 백그라운드에서 재시작될 것리다. 우리는 app delegate로 부터 이벤트를 핸들링할 필요가 있다.



Switch to *AppDelegate.swift*, replace `// TODO 17` with the following code:

```swift
var backgroundSessionCompletionHandler: (() -> Void)?
```

Next, replace `// TODO 18` with the following method:

```swift
func application(
  _ application: UIApplication,
  handleEventsForBackgroundURLSession 
    handleEventsForBackgroundURLSessionidentifier: String,
  completionHandler: @escaping () -> Void) {
    backgroundSessionCompletionHandler = completionHandler
}
```

우리는 제공된 completionHandler를 앱에서 나중에 사용하기 위해 저장한다.



`application(_:handleEventsForBackgroundURLSession:)`  완료된 백그라운드 작업을 다루기위해서 앱을 깨운다.

우리는 아래 2개의 메소드를 핸들링 할 필요가 있다.

- 첫째, 앱은 적절한 세션 델리게이트 메소드로 부터 제공된 식별자를 이용해서 백그라운드 설정과 세션을 재생성해야한다. 그러나  `SearchViewController` 가 초기화될 때, 백그라운드 세션이 생성되기 때문에, 우리는 이미 재 연결되어있다.
- Second, you’ll need to capture the completion handler provided by this delegate method. Invoking the completion handler tells the OS that your app’s done working with all background activities for the current session. It also causes the OS to snapshot your updated UI for display in the app switcher.
- 둘째, 우리는 delegate 메소드로 부터 제공받은 completion handler를 붙잡아둘 필요가 있다. completion handler를 호출해서 OS에게 우리의 앱의 작업의 완료를 알립니다. 이것은 OS가 앱 스위처에 보여줄 우리의 업데이트된 UI를 스냅샷하게 해줍니다.

<br>

제공된 completion hander를 호출하는 위치는  `urlSessionDidFinishEvents(forBackgroundURLSession:)` 입니다. 

URLSessionDelegate 메소드는 백그라운드 세션의 모든 작업이 완료되었을떄 발생합니다.



In *SearchViewController.swift* replace `// TODO 19` with the following extension:

```swift
extension SearchViewController: URLSessionDelegate {
  func urlSessionDidFinishEvents(forBackgroundURLSession session: URLSession) {
    DispatchQueue.main.async {
      if let appDelegate = UIApplication.shared.delegate as? AppDelegate,
        let completionHandler = appDelegate.backgroundSessionCompletionHandler {
        appDelegate.backgroundSessionCompletionHandler = nil
        
        completionHandler()
      }
    }
  } 
}
```



위의 코드는 저장해둔 completion handler 를  앱델리게이트로 부터 가져와서 메인스레드에서 호출합니다.

<br>

### Testing Your App’s Functionality

앱을 빌드하고 동작시키자. 몇몇 다운로드를 시작하고, 앱을 백그라운드로 보내기위해 홈버튼을 탭하자. 다운로드가 끝났다고 생각될 때까지 기다린다. 그후 더블탭으로 앱스위처를 보자.

앱의 스냅샷을 볼 수 있을 것이다.

![Completed Half Tunes App With All Functions Enabled](https://koenig-media.raywenderlich.com/uploads/2019/05/11-Background-Download-281x500.png)

You now have a functional music-streaming app! Your move now, Apple Music! :]

## Where to Go From Here?

Congratulations! You’re now well-equipped to handle most common networking requirements in your app. 

If you want to explore the subject further, there are more `URLSession` topics than would fit in this tutorial. For example, you can also try out uploading tasks and session configuration settings such as timeout values and caching policies.

To learn more about these features (and others!), check out the following resources:

- Apple’s [URLSession Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/URLLoadingSystem/Articles/UsingNSURLSession.html#//apple_ref/doc/uid/TP40013509-SW1) contains comprehensive information on everything you’d want to do.
- Our own [Networking with URLSession](https://videos.raywenderlich.com/courses/67-networking-with-urlsession/lessons/1) video course starts with HTTP basics, then goes on to cover tasks, background sessions, authentication, App Transport Security, architecture and unit testing.
- [AlamoFire](https://github.com/Alamofire/Alamofire) is a popular third-party iOS networking library; we cover the basics of it in our [Beginning Alamofire](http://www.raywenderlich.com/85080/beginning-alamofire-tutorial) tutorial.

I hope you enjoyed reading this tutorial. If you have any questions or comments, please join the discussion below!