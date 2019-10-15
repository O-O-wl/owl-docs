**Speaker → Mashup → 1**

## 목차

- Observable이란?
- Event란?
- Single, Maybe, Completable
- Subscribe
- Hot Observable, Cold Observable
- Subject란?
- Marble Diagram 보는 법
- Subject 종류 소개 + Marble Diagram으로 보기
- RxOperator 핥아보기 → **코드와 함께**

## 살펴보기 앞서

RxSwift Playground 다운로드!

[RxSwiftPlayground.zip](https://www.notion.so/59081ae144bc4c33807954cb2ce36426#a86ed66b1b6747f6b6a67a51ffd36c83)

## Obsevable이란?

**한 줄 정리!**

Observable은 통로, 흐름이다. 무엇이 흐르느냐? **Event라는 것이 흐르는 통로**이다.

## Event란?

Event는 일단 Swift의 **Enum** 타입입니다.

```
public enum Event<Element> {
    /// Next element is produced.
    case next(Element)

    /// Sequence terminated with an error.
    case error(Swift.Error)

    /// Sequence completed successfully.
    case completed
}
```

어떠한 값이 올 때는 `.next` 오류가 났을 때는 `.error` 이제 더 이상 아무것도 오지 않을 때는 `.complete` 가 Observable 즉 통로로 떨어지게 됩니다.

## Single, Maybe, Completable [부제 - Observable의 다른 모습]

일단 Single, Maybe, Completable은 Observable의 형태를 **명시적으로 표현**하는 것!

### Single이란?

하나의 값이 떨어지거나, 오류가 나고 더 이상 아무것도 오지 않는 Observable을 말합니다.

- 위의 설명을 Event로 표현한다면?

`.next` → `.complete` or `.error` 로 이벤트가 흐르는 Observable

### Maybe란?

하나의 값이 떨어지거나, 오류가 나거나, 아무것도 오지 않는 Observable을 말합니다.

- 위의 설명을 Event로 표현한다면?

`.next` → `.complete` or `.error`  or `.complete`로 이벤트가 흐르는 Observable

### Completable이란?

아무것도 오지 않거나 오류가 나는 Observable을 말합니다.

- 위의 설명을 Event로 표현한다면?

`.error` or `.complete`로 이벤트가 흐르는 Observable

## Subscribe란?

```
// 보통 Observable을 사용할 때 이런 형태로 사용합니다.
let observable: Observable<String>
let single: Single<String>
let maybe: Maybe<String>
let completable: Completable

// 위의 모두는 Event라는 것을 떨어뜨립니다.
// Subscribe의 종류 1 -> 위에서 설명한 Event가 떨어집니다.
observable.subscribe({event: Event<String> -> Void})

// Subscribe의 종류 2 -> Event의 타입에 맞추어 각각의 함수를 호출해줍니다.
observable.subscribe(
	onNext: { value: String -> Void }, // Event가 .next일 때 .next안의 Value를 반환
	onError: { error: Error -> Void }, // Event가 .error일 때 .error안의 Error를 반환
  onCompleted: { _ -> Void },        // Event가 .complete일 때 함수 호출
  onDisposed: { // 이 부분은 나중에! }
)
```

고로 .subscribe를 호출하면 **Event가 Observable이라는 통로를 타고 .subscribe의 클로저(함수)안으로 들어**오게 됩니다!

- **subscribe가 가지는 Power!**

`.subscribe` 함수가 호출 되기 전까지는 Observable의 Event는 흐르지 않습니다. `.subscribe`가 호출되는 순간부터 **Observable이라는 통로에 Event가 흐르게 됩니다.**

(`.subscribe`가 호출되기 전까지는 Observable은 Event 설계도? 정도 **like 클래스와 인스턴스**)

## Hot Observable 과 Cold Observable

**한 줄 정리!**

Observable이 **생성되는 타이밍에 어떤 Event가 어느 시점에 방출**되는지 결정된다면 **Cold**, 그렇지 않다면 **Hot**

```
// Cold 예시
Observable.just(1) // .next(1) -> .complete

// Hot 예시
button.rx.tap // 사용자가 버튼을 누를 때마다 .next 방출
```

## Subject란? [부제 - 너와 나의 연결고리]

**한 줄 정리!**

Observable을 다른 곳에 전달하는 **연결고리 역할**을 수행합니다.

```
// 보통 다음과 같이 사용됩니다.
let observable: Observable<Int>
let subject = PublishSubject<Int>()

// observable의 Event를 subject가 대신 받아서
observable.subscribe(subject)

// subject의 subscribe 함수로 받도록 합니다.
subject.subscribe()
```

## Marble Diagram 보는 법!

**보기 전에**

```
// map Operator 부터 먼저 이해하고 보자
let observable = Observable.of(1, 2, 3)
// .next(1), .next(2), .next(3), .complete

let observable2 = observable.map { $0 * 10 }
// .next(10), .next(20), .next(30), .complete
```



위에 정리 된 코드를 Marble Diagram으로 표현하면 위 그림과 같습니다. `.next`를 **동그라미**로 `.complete`를 **막대기**로 표현합니다.

## Subject 종류

```
PublishSubject`, `BehaviorSubject`, `ReplaySubject
```







## RxOperator 핥아보기

**RxMarble 앱 →** RxSwift의 모든 Operator와 Operator에 대한 Marble Diagram을 설명



- 이 앱에서 관심 있는 Operator 3개 정도 골라서 같이 알아보는 형태로 진행 (Marble Diagram보는 것도)
- 