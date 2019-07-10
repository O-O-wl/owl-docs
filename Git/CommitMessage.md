## 골치아픈 이름 짓기

프로그래머들에게 가장 어려운 것 하나만 꼽아보라고 하면 절반이 ‘naming’을 선택한다고 합니니다. 프로그램을 작성하면서 파일명, 클래스명, 변수명, 함수명 등 수도 없이 많은 이름들을 지어내야 합니다. 거의 작명소 수준입니다. 그런 이름 짓기보다 더 어려운 것이 있습니다. 바로 커밋 메시지 작성입니다.

한글로 커밋 메시지를 작성하는 팀이라면 별 걱정이 없겠으나, 영문으로 작성하는 팀이라면 만만치 않은 일입니다. 하고 싶은 말은 많은데, 막상 쓰지를 못하니 이렇게 답답할 수가 없습니다. 최근 커밋 메시지 하나를 올리며 이런 저런 생각들이 들었습니다. 이게 문법에 맞는걸까, 왜 자꾸 문장이 길어지는걸까, 말인지 방구인지 알고 쓰고 있는걸까. 그래서 차라리 영어권 프로젝트에서 어떤 식으로 작성하는지 살펴보고 패턴을 분석해보기로 했습니다.

규모가 큰 프로젝트이므로 어느정도 체계가 잡혀 있을 것 같은 node.js와 React, React Native의 Change History를 살펴보았습니다. 일단 구구절절하지 않게 필요한 내용을 짧막하게 작성하는 것이 눈애 띕니다. 아무리 영어권 프로젝트라 하더라도, 커밋에 참가한 사람들 중 많은 수가 모국어로 영어를 쓰는 것은 아닐테니 간결한 커밋 메시지는 꼭 필요해 보입니다. 어떻게 메시지가 간결해질 수 있는지, 또 알아두면 좋을 특징들이 있는지 살펴봅시다.

## 커밋 메시지 규칙

이미 유정하지만, 커밋 메시지를 작성하는 좋은 규칙들을 공유합니다.

- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
- [AngularJS Git Commit Message Conventions](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit)

그 외에, 이번 분석을 통해 생각해 본 간결한 메시지를 위한 또다른 규칙들은 다음과 같습니다:

#### 동명사보다 명사를 사용합니다.

동사를 명사화시키기 보다는 그 의미를 잘 표현하는 명사를 찾아서 사용합니다. 이렇게하면 문장이 장황하지 않고 간결해집니다. 아무래도 영어에 약한 사람들에게 가장 어려운 미션 중 하나가 아닐까 생각됩니다.

#### 관사는 사용하지 않습니다.

꼭 필요한 경우가 아니면 **a**, **an**, **the**는 사용하지 않습니다.

#### 부정문 `Don't`를 사용합니다.

커밋 메시지를 명령문 형태로 써야 한다는 것은 모두들 알고 있지만, 부정 명령문을 사용하는 것은 잘 모릅니다. ‘A를 사용해’라고 명령했으니 반대를 이야기 할 때는 ‘A를 사용하지마’로 표현해야 합니다. Not use가 아니라 Don’t use 입니다.

```
Don't use spdy on node >= v10.0.0
Don't write to the persisted query cache until execution will begin.
Do not return list if there are too many crashes
```

#### 오타 수정은 Correct misspelled text가 아닙니다.

그냥 **Fix typo**라고만 하면 됩니다.

## 좋은 커밋 메시지를 위한 영어 단어 목록

node.js와 React 프로젝트의 Change History를 참고로 자주 반복되는 로그 메시지의 패턴을 정리해 봤습니다. 특별한 목적의 커밋이 아닌 이상 대부분의 메시지는 아래의 경우들에 포함 됩니다. 문장을 명료하게 만들어주는 문법들도 적어 보았습니다.

### FIX

가장 자주 사용되는 커밋 로그 중 하나로 ‘Fix’가 있습니다. 보통 올바르지 않은 동작을 고친 경우에 사용합니다.

#### Fix A

> A를 수정합니다

```
Fix stat cache
Fix changelog entry
Fix broken jsiexecutor search path.
```

#### Fix A in B

> B의 A를 수정합니다

가장 자주 사용되는 패턴입니다.

```
Fix calculation in process.uptime()
Fix build warning in node_report.cc
Fix error condition in Verify::VerifyFinal
Fix typo in callback.cc
Fix duplicate symbols linker error in xcodeproj
```

#### Fix A which B, Fix A that B

> B절인 A를 수정합니다

‘Fix A’로 끝낼 수 있지만, 보다 많은 정보를 주기 위해 which나 that 관계 대명사로 A를 설명합니다. 무엇을 수정한 것인지 보다 상세하게 설명할줄 때 주로 사용됩니다.

```
Fix incorrect type which makes animated gifs not loop forever on device
Fix crash that happens when a component throws an exception that contains a null message
```

#### Fix A to B, Fix A to be B

> B를 위해 A를 수정합니다

왜 수정하는지를 추가로 설명합니다.

```
Fix inability to remove 'Disabled' state from AccessibilityStates
Fix HTTP connection timeout callback to be appropriately called
```

#### Fix A so that B

> A를 수정해서 B가 되었습니다

‘Fix A to B’와 의미는 비슷하나, 어감이 살짝 다릅니다. 고쳐진 B의 상태가 보다 강조됩니다.

```
Fix react-native init --help so that it doesn't return undefined
Fix Android 28's inverted ScrollView so that momentum is in the proper direction
```

#### Fix A where B

> B처럼 발생하는 A를 수정했습니다

여기서 A는 보통 ‘issue’, ‘error’, ‘crash’등이 들어갑니다. B는 문제가 발생한 모습을 적어주면 됩니다.

```
Fix case where content of inline views didn't get relaid out
Fix case where inline view is visible even though it should have been truncated
Fix issue where Image.resizeMode isn't respected while source is loading, resulting in unexpected padding
```

#### Fix A when B

> B일 때 발생하는 A를 수정했습니다

여기서 A는 보통 ‘issue’, ‘error’, ‘crash’등이 들어갑니다. B는 문제가 발생하는 상황을 적어주면 됩니다.

```
Fix accidental showing of Modal when visible prop is undefined or null
Fix crash when removing root nodes
```

### ADD

코드나 테스트, 예제, 문서 등의 추가가 있을 때 사용합니다

#### Add A

> A를 추가합니다

추가하는 행위는 대부분 목표나 목적이 명시되기 때문에 이 패턴은 자주 사용되지 않습니다.

```
Add ERR_INSPECTOR_COMMAND error
```

#### Add A for B

> B를 위해 A를 추가했습니다

```
Add documentation for the defaultPort option
Add example for setting Vary: Accept-Encoding header in zlib.md
Add missing includes for vtune build
Add test for dynamically enabling node.async_hooks tracing
Add test for InterpolatorType
Add devDependencies support for templates
```

#### Add A to B

> B에 A를 추가했습니다

```
Add error description to Image onError callback
Add displayName to ActivityIndicator
Add deprecation notice to SwipeableListView
```

### REMOVE

코드의 삭제가 있을 때 사용합니다. ‘Clean’이나 ‘Eliminate’를 사용하기도 합니다. 보통 A 앞에 ‘unnecessary’, ‘useless’, ‘unneeded’, ‘unused’, ‘duplicated’가 붙는 경우가 많습니다.

#### Remove A

> A를 삭제합니다

```
Remove fallback cache
Remove unnecessary italics from child_process.md
Remove useless additionnal blur call
Remove unneeded .gitignore entries
Remove unused variable
Remove duplicated buffer negative allocation test
```

#### Remove A from B

> B에서 A를 삭제합니다

```
Remove absolute path parameter from transformers
Remove trailing slash from origin header if no port is specified
```

### USE

특별히 무언가를 사용해 구현을 하는 경우입니다.

#### Use A

> A를 사용합니다

‘사용하였음’을 이야기 할 때는 대체적으로 목적이 필요하기 때문에 이 용법은 자주 사용되지 않습니다.

```
Use more stable cast where possible
```

#### Use A for B

> B에 A를 사용합니다

```
Use fake MessageEvent for port.onmessage
Use object writer for thrown errors
Use ru_stime for system CPU calculation
Use relative path for SCRIPTDIR
```

#### Use A to B

> B가 되도록 A를 사용합니다

```
use common operations to define browser globals
use triggerReport() to handle signals
use PauseOnNextJavascriptStatement to implement --inspect-brk-node
```

#### Use A in B

> B에서 A를 사용합니다

```
Use smart pointer in UDPWrap::OnSend
Use same parameter name in node_report.cc
Use TextLegend example in Android as well
Use main.jsbundle in iOS template for production build
Use new Metro configuration in react-native cli
```

#### Use A instead of B

> B 대신 A를 사용합니다

```
Use babel runtime instead of relying on global babelHelpers and regenerator
```

### REFACTOR

전면 수정이 있을 때 사용합니다.

#### Refactor A

```
Refactor tick objects prune function
Refactor thread life cycle management
Refactor QueryWrap lifetime management 
Refactor argument validation
Refactor thread life cycle management
Refactor MockNativeMethods in Jest
```

### SIMPLIFY

복잡한 코드를 단순화 할 때 사용합니다. Refactor의 성격이 강하나 이보다는 약한 수정의 경우 이용하면 좋습니다.

#### Simplify A

> A를 단순화합니다

```
Simplify code and remove obsolete checks
Simplify the setup of async hooks trace events
Simplify heap space iteration
Simplify TriggerNodeReport()
Simplify AliasedBuffer lifetime management 
Simplify loop arithmetic in GetCPUInfo
```

### UPDATE

개정이나 버전 업데이트가 있을 때 사용합니다. Fix와는 달리 Update는 잘못된 것을 바로잡는 것이 아니라는 점에 주의해야 합니다. 원래도 정상적으로 동작하고 있었지만, 수정, 추가, 보완을 한다는 개념입니다. 코드보다는 주로 문서나 리소스, 라이브러리등에 사용합니다.

#### Update A to B

> A를 B로 업데이트 합니다.

```
Update acorn to 6.1.0
```

> A를 B하기 위해 업데이트 합니다

```
Update react-devtools-core and plist to include security fixes reported by npm audit
Update RCTLinkingManager.h to explicitly state the 'nullability' of parameters
Update repo docs to use HTTPS
Update app icons to match recent Android releases
Update babelHelpers with Babel 7 support
```

### IMPROVE

향상이 있을 때 사용합니다. 호환성, 테스트 커버리지, 성능, 검증 기능, 접근성 등 다양한 것들이 목적이 될 수 있습니다.

#### Improve A

> A를 향상시킵니다

```
Improve compatibility with http/1
Improve Unicode handling
Improve test coverage in perf_hooks
Improve validation of report output
Improve performance of test-crypto-timing-safe-equal-benchmarks
Improve color detection
Improve Android Network Security config
Improve Accessibility
Improve iOS's accessibilityLabel performance by up to 20%
```

### MAKE

주로 기존 동작의 변경을 명시합니다.

#### Make A B

> A를 B하게 만듭니다

- config object를 read-only로 만듭니다.
- floating patch 메시지에 더 많은 정보가 담기도록 만듭니다.
- ViewPropTypes의 값들을 옵셔널하게 만듭니다.
- read()를 유저가 원한다면 무기한으로 호출되도록 만듭니다.
- IsolateData가 ArrayBuffer를 저장하도록 만듭니다.

모두 기존의 동작을 바꾼 것들입니다. 새롭게 뭔가를 만들었을 때는 Make 대신, Add를 사용해야 합니다.

```
Make config object read-only
make 'floating patch' message informational
Make values optional in ViewPropTypes
make read() be called indefinitely if the user wants so
make IsolateData store ArrayBufferAllocator
```

### IMPLEMENT

코드가 추가된 정도보다 더 주목할 만한 구현체를 완성시켰을 때 사용합니다.

#### Implement A

> A를 구현합니다

‘Add’에 비해 더 큰 단위의 코드 추가에 사용되며, 특히 모듈이나 클래스 등의 단위에 사용되기 때문에 특별히 목적을 부여 해주지 않아도 되는 경우가 많습니다. 따라서 ‘Add’에 비해 to나 for가 함께 사용되는 경우가 적습니다.

```
Implement date object
Implement Image.defaultSource
Implement bundle sync status
```

#### Implement A to B

> B를 위해 A를 구현합니다

구현 목적을 설명할 필요가 있을 때에는 ‘to’를 사용합니다.

```
Implement requiresMainQueueSetup in RCTTVNavigationEventEmitter to satisfy Xcode warning
Implement an in-memory cache store to save parsed and validated documents and provide performance benefits for repeat executions of the same document
```

### REVISE

Update와 비슷하나 문서의 개정이 있을 때 주로 사용합니다.

#### Revise A

> A 문서를 개정합니다

```
Revise deprecation semverness info in Collaborator Guide
```

### CORRECT

주로 문법의 오류나 타입의 변경, 이름 변경 등에 사용합니다.

#### Correct A

> A를 고칩니다

```
Correct grammatical error in BUILDING.md
Correct parameters, return types in crypto.md
Correct styling of _GitHub_ in onboarding doc
Correct buffer changelog ordering
Correct async_hooks resource names
```

### ENSURE

무엇이 확실하게 보장받는다는 것을 명시합니다. if 구문처럼 조건을 확실하게 주었을 때에도 사용 될 수 있습니다. ‘Make sure’도 같은 용도로 사용될 수 있습니다.

#### Ensure A

> A가 확실히 보장 되도록 수정했습니다

```
Ensure quiet always takes precedence
Ensure cookies with illegal characters are not sent to okhttp
Ensure require.main for CJS top-level loads
Ensure Stream.pipeline re-throws errors without callback
Ensure options.flag defaults to 'r' in readFile
```

### PREVENT

특정한 처리를 못하게 막습니다

#### Prevent A

> A하지 못하게 막습니다

```
Prevent multiple connection errors
Prevent constructing console methods
Prevent event loop blocking
Prevent a potential error in event handling if Object.prototype is extended.
Prevent an infinite loop when attempting to render portals with SSR.
```

#### Prevent A from B

> A를 B하지 못하게 막습니다

```
Prevent event handlers from receiving extra argument in development.
```

### AVOID

‘Prevent’는 못하게 막지만, ‘Avoid’는 회피합니다. if 구문으로 특정한 동작을 제외시키는 경우에도 사용 할 수 있습니다.

#### Avoid A

> A를 회피합니다

```
Avoid flusing uninitialized traces
Avoid overrun on UCS-2 string write
Avoid race condition in OnHeaderCallback
Avoid memory leak on gc observer
Avoid materializing ArrayBuffer for creation
```

#### Avoid A if B, Avoid A when B

> B인 상황에서 A를 회피합니다

```
Avoid importing entire crypto dependency tree if not in Node.js.
Avoid "Member not found" exception in IE10 when calling preventDefault() in Synthetic Events.
Avoid input validation warning from browsers when changing type.  
Avoid double reload event when reloading JS
```

### MOVE

코드의 이동이 있을 때 사용합니다.

#### Move A to B, Move A into B

> A를 B로 옮깁니다

```
Move test-process-uptime to parallel
Move function from header to source file
Move async hooks trace events setup to pre_execution.js
move initialization of node-report into pre_execution.js
```

### RENAME

이름 변경이 있을 때 사용합니다.

#### Rename A to B

> A를 B로 이름 변경합니다

```
Rename node-report to report
Rename location to trigger
Rename node-report suite to report
```

### ALLOW

Make와 비슷하지만, 허용을 표현할 때 사용합니다.

#### Allow A to B

> A가 B를 할 수 있도록 허용합니다

```
Allow the output filename to be a {Function}
Allow Node.js-like runtimes to identify as Node.js as well.
Allow passing parseOptions to ApolloServerBase constructor.
Allow an optional function to resolve the rootValue, passing the DocumentNode AST to determine the value.
```

### VERIFY

검증 코드를 넣을 때 주로 사용합니다.

#### Verify A

> A를 검증합니다

```
Verify heap buffer allocations occur
```

### SET

변수 값을 변경하는 등의 작은 수정에 주로 사용합니다.

#### Set A to B

> A를 B로 설정합니다

```
set tls.DEFAULT_ECDH_CURVE to 'auto'
```

### PASS

파라메터를 넘기는 처리에 주로 사용합니다.

#### Pass A to B

> A를 B로 넘깁니다

```
Pass the response toolkit to the context function.
```

## 마치며

실제로 프로젝트에서 사용되는 커밋 메시지는 간단하기 그지 없습니다. 문법도 중학 영어 수준이고, 어려운 단어 역시 사용되지 않습니다. 결국 커밋 로그 메시지의 작성은 작문이 아니라 패턴으로 접근해야 합니다. 자신의 커밋이 가진 특징을 패턴에 대입시켜 단어들을 뽑아내는 것이죠. 작문의 결과로 보면 너무 단순해서 부족해 보일지 몰라도, 여러 사람들에게 쉽게 읽히고 쉽게 이해되도록 하기에는 패턴화된 단순한 문장이 훨씬 낫습니다.

그럼에도 불구하고 커밋 메시지를 쉽게 쓰는 것은 어렵습니다. 첫째, 한 문장으로 설명이 가능하도록 작업을 쪼개어 커밋하는 것 자체가 쉽지 않습니다. 둘째, 작성한 당사자가 아닌 처음 보는 제3자가 이해할 수 있는 문장을 만들어야 합니다. `commit -m`을 입력한 후, 메시지 입력창이 나타나면 눈을 감고 크게 한숨을 내쉬면서 작업한 내용을 돌아보세요. 그리고 최선의 한 단어를 선택해보세요.

**Tags:** [git](https://blog.ull.im/tag/git), [commit](https://blog.ull.im/tag/commit), [convention](https://blog.ull.im/tag/convention)

**Subscribe via RSS**

Share: [ ](https://twitter.com/share?text=좋은+git+commit+메시지를+위한+영어+사전&url=https%3A%2F%2Fblog.ull.im%2Fengineering%2F2019%2F03%2F10%2Flogs-on-git.html&via=Twitter)[ ](https://www.facebook.com/sharer.php?t=좋은+git+commit+메시지를+위한+영어+사전&u=https%3A%2F%2Fblog.ull.im%2Fengineering%2F2019%2F03%2F10%2Flogs-on-git.html)

[ 3년간의 리모트 개발에 대한 소회](https://blog.ull.im/engineering/2019/03/10/remote.html)[[번역\] Mongoose 모델과 단위 테스트: 최종 가이드 ](https://blog.ull.im/engineering/2019/03/13/mongoose-models-and-unit-tests.html)

<iframe id="dsq-app4749" name="dsq-app4749" allowtransparency="true" frameborder="0" scrolling="no" tabindex="0" title="Disqus" width="100%" src="https://disqus.com/embed/comments/?base=default&amp;f=ullim&amp;t_u=https%3A%2F%2Fblog.ull.im%2Fengineering%2F2019%2F03%2F10%2Flogs-on-git.html&amp;t_d=%EC%A2%8B%EC%9D%80%20git%20commit%20%EB%A9%94%EC%8B%9C%EC%A7%80%EB%A5%BC%20%EC%9C%84%ED%95%9C%20%EC%98%81%EC%96%B4%20%EC%82%AC%EC%A0%84&amp;t_t=%EC%A2%8B%EC%9D%80%20git%20commit%20%EB%A9%94%EC%8B%9C%EC%A7%80%EB%A5%BC%20%EC%9C%84%ED%95%9C%20%EC%98%81%EC%96%B4%20%EC%82%AC%EC%A0%84&amp;s_o=default#version=3e8af7959bccec171b9bea4c5ef5f78f" horizontalscrolling="no" verticalscrolling="no" style="box-sizing: inherit; width: 1px !important; min-width: 100%; border: none !important; overflow: hidden !important; height: 390px !important;"></iframe>

**Site Map**

- [About](https://blog.ull.im/about/)
- [Posts](https://blog.ull.im/posts/)

**Contact**

- [ einsub@gmail.com](mailto:einsub@gmail.com)
- [ Twitter](https://twitter.com/einsub)
- [ Facebook](https://www.facebook.com/einsub)
- [ GitHub](https://github.com/einsub)
- [ LinkedIn](https://www.linkedin.com/in/einsub/)
- [ Instagram](https://www.instagram.com/resoneit/)

**Subscribe via RSS**

울려 퍼지다.
반향하다.
공명하다.