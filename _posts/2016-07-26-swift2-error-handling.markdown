---
published: true
title: Swift2 Error Handling
layout: post
tags: [swift, error, throws, swift2]
---
원문: https://www.raywenderlich.com/130197/magical-error-handling-swift
 
## 시작하기

강좌를 위해 2개의 playground 를 준비했습니다. 우선 다운로드 받으세요.

Xcode에서 우선 첫번째 Errors with nil starter playground 파일을 열어주세요.

몇개의 class, structs, enums 가 보일겁니다.

아래 코드를 주의깊게 보세요.

```swift
protocol MagicalTutorialObject {
  var avatar: String { get }
}
```

위 프로토콜은 강좌 전체에 사용되는 Class, Structure 에 적용되는데, 각 오브젝트들을 콘솔화면에 표시해 주기 위해서 사용됩니다.

```swift
enum MagicWords: String {
  case Abracadbra = "abracadabra"
  case Alakazam = "alakazam"
  case HocusPocus = "hocus pocus"
  case PrestoChango = "presto chango"
}
```

이 enumeration 은 주문 (Spell) 을 만들기 위한 마법과 가ㅗ련된 용어들을 설명하고 있습니다.


```swift
struct Spell: MagicalTutorialObject {
  var magicWords: MagicWords = .Abracadbra
  var avatar = "*"
}

```

위 코드가 기본적인 Spell 을 위한 기본 구조입니다. 기본 magic word 는 “Abracadabra" 입니다.

자 이제 여러분들은 마법세상과 관련된 기본 내용 들에 익숙해 지셨겠죠. 이제 마법을 사용할 준비가 되었습니다.

## 왜 에러처리에 신경써야 하죠?

에러처리을 사용하면 이슈파악, 원인, 관련된 중요도 등을 파악할 수 있습니다. 이는 사용자들에게는 좀더 나은 사용성을 줄 수 있고, 개발자에게 관리상의 편안함을 줄 수 있습니다. 코드 상의 에러가 자세하면 할 수록 문제점을 파악하기가 더 쉬워집니다. 에러처리는 또한 문제점이 발생했을때 좀 더 적합한 방식으로 처리하여 유저들이 화나지않도록 해주기도 하죠.

하지만 항상 에러처리가 필요한 것은 아닙니다.  에러의 가능성을 줄일 수 있는 방법이 있다면 그렇게 하는 것이 일반적인 방법입니다. 잠재적인 에러 조건을 피할 수 없는 상황이라면, 명시적인 에러처리가 최선의 방법입니다.

## nil 을 사용해 Error를 피하는 기법

스위프트에는 이미 세련된 옵셔널 기법을 사용해서 에러조건을 완전히 피할 수 있습니다.  값(value) 가 들어와야 할 곳에 아무런 값이 없을 때가 이런 상황입니다.  이 접근방법이 사용될 가장 알맞은 상황은 에러 발생시 아무런 동작도 취하지 않는 경우입니다.

이 기법을 사용한  두가지 예시는 failable 초기화와 guard statement 입니다.

##Failable initializers ( 실패가능 초기자)

해석하자면 "실패할 수 있는 초기화" 입니다. 너무 길기 때문에 원문 그대로 사용합니다. Failable initializers 를 이용하면 충분한 정보가 없는 경우 오브젝트 생성 자체를 막을 수 있습니다.
Swift2 이전에는 이러한 기능을 사용하기 위해 Factory 패턴들 사용했습니다. 이는 기존의 다른 언어들도 마찬가지입니다.

createWithMagicWords 함수에서 이러한 예를 볼 수 있습니다.

```swift
static func createWithMagicWords(words: String) -> Spell? {
  if let incantation = MagicWords(rawValue: words) {
    var spell = Spell()
    spell.magicWords = incantation
    return spell
  }
  else {
    return nil
  }
}
```



위 코드를 보면 미리 제공된 magic word 를 이용하여 초기화하려합니다만, magic word 가 없는 경우에는 nil 을 리턴합니다.

확인하려면 이 강좌 맨 아래에 있는 spell 부분을 보세요.

![image](https://cdn4.raywenderlich.com/wp-content/uploads/2016/04/Screen-Shot-2016-04-15-at-6.45.31-PM.png)


"abracadabra", “ascendio” 를 사용할때는 주문(spell)을 올 바르게 만들 수 있는 반면, ?????

이 디자인패턴은 old-school 스타일입니다. 스위프트에는 좀 더 나은 방법이 제공되고 있답니다. 팩토리패턴 대신 Failable initializer 를 사용하기 위해서 Spell Extension 을 수정합시다.

createWithMagicWords 함수를 삭제하고 아래처럼 수정해주세요.

```swift
init?(words: String) {
  if let incantation = MagicWords(rawValue: words) {
    self.magicWords = incantation
  }
  else {
    return nil
  }
}
```


자 이제 명시적인 생성및 spell object 를 리턴하지 않기 위해 코드가 간단해 졌습니다.

앗. first 와 second 부분에 컴파일 에러가 발생하네요.

```swift
let first = Spell.createWithMagicWords("abracadabra")
let second = Spell.createWithMagicWords("ascendio")
```
아래와 같이 생성자를 수정해 주세요.

```swift
let first = Spell(words: "abracadabra")
let second = Spell(words: "ascend")

```
모든 에러가 수정되었고 잘 실행될 겁니다. 코드가 훨씬 깔끔해졌죠~


## Guard 구문

어떤 값이 참 인지 아닌지 확실히 파악할 수 있는 빠른 방법입니다.
예를들어 값이 0보다 큰경우 또는 값이 unwrapped 가능한경우를 들수있다.  결과가 fail 일 때 특정 코드블럭을 실행합니다.

Guard 구문은 Swift 2 에서 소개되었습니다. 이른바 함수나 메소드속에서 early exit 를 가능하게 해 줍니다.  이는 나머지 코드에 대한 실행할 지 말지 결정하는데 명확하게 해 줍니다.

Guard 구문을 사용하여 아래처럼 수정해 봅시다.

```swift
init?(words: String) {
  guard let incantation = MagicWords(rawValue: words) else {
    return nil
  }
  self.magicWords = incantation
}
```

이 구문을 사용함으로써 추가적인 else 코드블럭을 없앨 수 있고,  에러처리를 초기화 구문의 앞부분으로 이동 시킴으로써 코드가 깔끔해집니다. 또한 코드상에 indent 가 줄어들어 읽기 쉬워지는 장점 또한 있습니다.  이를 Golden path라고 합니다.

정리해보자면 first, second Spell 변수가 그대로이지만 코드는 훨씬 더 단순하게 바뀌었습니다.

## Custom Handling 을 통한 에러 피해보기

여기부터 섹션2입니다. 다음 강좌를 위해 Avoiding Errors with Custom Handling – Starter.playground 를 열어주세요.

아래 코드를 봐 주세요.

```swift
struct Spell: MagicalTutorialObject {
 
  var magicWords: MagicWords = .Abracadbra
  var avatar = "*"
 
  init?(words: String) {
    guard let incantation = MagicWords(rawValue: words) else {
      return nil
    }
    self.magicWords = incantation
  }
 
  init?(magicWords: MagicWords) {
    self.magicWords = magicWords
  }
}
```

이 부분은 Spell 초기자입니다. 섹션 1 에서 배웠던 부분을 반영했습니다. MagicalTutorialObject 프로토콜과 두번째 초기자를 잘 봐주세요. 두번째 초기자는 편의상 추가되었습니다.

```swift
protocol Familiar: MagicalTutorialObject {
  var noise: String { get }
  var name: String? { get set }
  init()
  init(name: String?)
}
```

이제부터 사용할 플레이그라운드에는 여러가지 동물에게 Familiar protocol을 적용할 겁니다. ( 박쥐, 두꺼비등 )

노트: unfamiliar 와 familiar 에 대한 어원에 대해 궁금하신 분은 원문을 참조하세요~

```swift
struct Witch: MagicalBeing {
  var avatar = "*"
  var name: String?
  var familiar: Familiar?
  var spells: [Spell] = []
  var hat: Hat?
 
  init(name: String?, familiar: Familiar?) {
    self.name = name
    self.familiar = familiar
 
    if let s = Spell(magicWords: .PrestoChango) {
      self.spells = [s]
    }
  }
 
  init(name: String?, familiar: Familiar?, hat: Hat?) {
    self.init(name: name, familiar: familiar)
    self.hat = hat
  }
 
  func turnFamiliarIntoToad() -> Toad {
    if let hat = hat {
      if hat.isMagical { // When have you ever seen a Witch perform a spell without her magical hat on ? :]
        if let familiar = familiar {   // Check if witch has a familiar
          if let toad = familiar as? Toad {  // Check if familiar is already a toad - no magic required
            return toad
          } else {
            if hasSpellOfType(.PrestoChango) {
              if let name = familiar.name {
                return Toad(name: name)
              }
            }
          }
        }
      }
    }
    return Toad(name: "New Toad")  // This is an entirely new Toad.
  }
 
  func hasSpellOfType(type: MagicWords) -> Bool { // Check if witch currently has appropriate spell in their spellbook
    return spells.contains { $0.magicWords == type }
  }
}
```

* Witch는  두개의 init 에서 두가지 방법으로 초기화될 수 있습니다.
* Witch는 spells 에 저장된 몇개의 주문들을 알고 있습니다. (Spells 는 Spell 오브젝트로 이루어진 array 입니다 )
* Witch 는 familiar 를 두꺼비로 바꾸는 취향이 있습니다. turnFamiliarIntoToad 함수내에서 PrestoChango 를 사용합니다.

여기서 주의깊게 볼 것을 indentation 이 상당히 길고 깊이까지 들어가 있다는 점입니다. 계단처럼되어 있는 코드를 보세요.  또한 만약 뭔가 잘못되는 경우에는 완전히 다른 두꺼비가  return 될 수도 있습니다. 뭔가 헷갈리고 잘못된 것 처럼 보이네요. 다음 섹션에서 이 코드를 아주 완전히 바꿉시다.

## Swift Errors 를 사용하기 위한 리팩터링.

이른바 pyramid of doom 이라는 Anti-Swift 패턴입니다. 다른 언어에서도 볼 수 있는데 제어구문사용을 위해 여러 단계의 중첩된 구문이 사용됩니다. 한 눈에 알아보기 힘들죠. 위 코드 상에서는 turnFamiliarIntoToad() 에서 볼 수 있습니다. 코드 파악하기 위한 상당한 노력이 필요합니다.

이전에 보았던 Guard 구문과 multiple 옵셔널 바인딩을 사용해서 문제를 해결해보죠. 아.근데.. The use of a do-catch mechanism, however, eliminates the problem altogether by decoupling control flow from error state handling.

do-catch 매커니즘은 종종 아래 키워드 주변에서 잘 볼 수 있습니다.

* throws
* do
* catch
* try
* defer
* ErrorType

이 매커니즘을 보기 위해, 여러개의 custom error 들을 발생시켜봅시다(throw errors). 먼저 발생할수 있는 모든 조건을 나열해 enumeration 구문으로 정의해 봅시다.

Witch 정의 윗부분에 아래 code 를 추가해주세요.

```swift
enum ChangoSpellError: ErrorType {
  case HatMissingOrNotMagical
  case NoFamiliar
  case FamiliarAlreadyAToad
  case SpellFailed(reason: String)
  case SpellNotKnownToWitch
}
```

Swift 에서 에러를 사용하기 위해서는 ErrorType 을 따라야 합니다(conform)
SpellFailed 경우를 보면  custom reason 을 지정해 줄 수 있습니다.  Custom reason 은 Spell Failure 와 연관값을 사용합니다.(associated value)

자 우리 이쁜이들 이제 마법을 한번 써 볼까요 😉
메소드 시그니쳐에  throws 를 추가합시다. 메소드 호출시 실패가 일어날 수도 있다는 것을 알려주는 구문입니다.

```swift
func turnFamiliarIntoToad() throws -> Toad {
```

MagicalBeing  Protocol역시 아래처럼 수정해주세요.

```swift
protocol MagicalBeing: MagicalTutorialObject {
  var name: String? { get set }
  var spells: [Spell] { get set }
  func turnFamiliarIntoToad() throws -> Toad
}

```

## Hat Error 처리하기.

마녀가 모자를 쓰고 있다는 것을 확실하게 하기 위해서 아래 구문을 수정해주세요.

```swift
if let hat = hat {
```
를 아래처럼 수정해주세요.

```swift
guard let hat = hat else {
  throw ChangoSpellError.HatMissingOrNotMagical
}
```

```
Note: 플레이그라운드가 컴파일러 에러를 뱉을 수 있으니  메소드 만 아래쪽의  associated } 부분을 반드시 제거해주세요.
```

다음줄은 boolean 체크를 포함하고 있습니다. 이 또한 hat 과 연관되어 있죠.

```swift
if hat.isMagical {
```

잠시 하략. 코딩하면서 체크해보자.

```swift
guard let hat = hat where hat.isMagical else {
  throw ChangoSpellError.HatMissingOrNotMagical
}
```

## Familiar Error 처리하기
 
다음, 마녀가 familiar 를 가지고 있는지 체크하는 구문을 변경합시다

```swift
if let familiar = familiar {
```
…를 아래처럼 Guard 구문에서 .NoFamiliar error 를 발생시키게끔 변경:

```swift
guard let familiar = familiar else {
  throw ChangoSpellError.NoFamiliar
}
```
지금 당장 발생하는 에러는 잠시 무시해주세요. 다음 코드수정때 없어집니다.

## Toad Error 처리하기
 
다음 줄을 보죠, 마녀가 두꺼비에게 turnFamiliarIntoToad() 주문을 걸려고 시도 할때 기존의 두꺼비를 리턴하고 있습니다. 여기서는 명시적인 에러가 마녀의 실수를 좀 더 잘 나타내 줍니다. 아래와 같이 수정합니다.

```swift
if let toad = familiar as? Toad {
  return toad
}
```

…를 아래처럼 수정해주세요.

```swift
if familiar is Toad {
  throw ChangoSpellError.FamiliarAlreadyAToad
}
```

as? 에서 is 로 변경된 부분을 자세히 봅시다. 이렇게 하면 결과값을 반드시 사용하지않고도 프로토콜 준수여부를 간단히 알아 볼 수 있습니다. `is`키워드는 좀 더 일반적인 경우 타입비교에 사용됩니다. is 와 as 에 대해 좀 더 알고 싶으시면 The Swift Programming Language 교재의 타입캐스팅 섹션을 참고하시기 바랍니다.

## Spell Error 처리하기

마지막입니다, hasSpellOfType(type:) 을 호출합시다.  이 메소드를 호출하는 것은 마녀의 마법책 속에 사용하기에 적합한 스펠(주문)이 있다는 것을 보장해 줍니다.

```swift
if hasSpellOfType(.PrestoChango) {
  if let toad = f as? Toad {
    return toad
  }
}
```
를 아래와 같이 수정.

```swift
guard hasSpellOfType(.PrestoChango) else {
  throw ChangoSpellError.SpellNotKnownToWitch
}
 
guard let name = familiar.name else {
  let reason = "Familiar doesn’t have a name."
  throw ChangoSpellError.SpellFailed(reason: reason)
}
 
return Toad(name: name)
```


다음 일종의 fail-safe 였던 아래 코드를 삭제해주세요.

```swift
return Toad(name: "New Toad")
```
훨씬 깔끔해진 전체 코드는 아래에서 확인해주세요.

```swift
func turnFamiliarIntoToad() throws -> Toad {
 
  // When have you ever seen a Witch perform a spell without her magical hat on ? :]
  guard let hat = hat where hat.isMagical else {
    throw ChangoSpellError.HatMissingOrNotMagical
  }
 
  // Check if witch has a familiar
  guard let familiar = familiar else {
    throw ChangoSpellError.NoFamiliar
  }
 
  // Check if familiar is already a toad - if so, why are you casting the spell?
  if familiar is Toad {
    throw ChangoSpellError.FamiliarAlreadyAToad
  }
  guard hasSpellOfType(.PrestoChango) else {
    throw ChangoSpellError.SpellNotKnownToWitch
  }
 
  // Check if the familiar has a name
  guard let name = familiar.name else {
    let reason = "Familiar doesn’t have a name."
    throw ChangoSpellError.SpellFailed(reason: reason)
  }
 
  // It all checks out! Return a toad with the same name as the witch's familiar
  return Toad(name: name)
}
```
뭔가 잘못된 것을 알리기 위해서 간단히 turnFamiliarIntoToad() 에서 옵셔널을 반환할 수도 있습니다. 하지만 custom error 를 사용하면 좀 더 깔끔하게 표현할 수 있고 그에 따른 각각의 처리를 할 수 있습니다.

## Custom Errors 의  또 다른 좋은 점이 뭐가 있나?

자 이제 여러분들은 custom Swift errors 를 던지는 메소드를 만들었습니다. 이제 잘 사용해 봅시다.  자바등의 언어에서 사용되는 try-catch 구문과 비슷한 swift 의 표준인 do-catch 를 사용해 볼까요.

Playground 에 아래 코드를 추가해주세요.

```swift
func exampleOne() {
  print("") // Add an empty line in the debug area
 
  // 1
  let salem = Cat(name: "Salem Saberhagen")
  salem.speak()
 
  // 2
  let witchOne = Witch(name: "Sabrina", familiar: salem)
  do {
    // 3
    try witchOne.turnFamiliarIntoToad()
  }
  // 4
  catch let error as ChangoSpellError {
    handleSpellError(error)
  }
  // 5
  catch {
    print("Something went wrong, are you feeling OK?")
  }
}
```
이 함수가 하는 일은 아래와 같습니다.

1. 마녀의 familiar 를 생성합니다.  이름이 Salem 인 고양이네요.
2. 마녀를 만듭니다. 이름은 Sabrina 네요.
3. 고양이를 두꺼비로 변신시킵니다.
4. ChangoSpellError 에러를 잡고 적절히 에러처리를 합니다.
5. 마침내 모든 에러를 잡고 멋진 메시지를 남깁니다. 

위처럼 잘 따라했는데 에러가 발생하네요. 그럼 다시 수정해볼까요?

handleSpellError 함수가 아직 정의되지 않았네요, exampleOne() 함수가 정의된 곳 위쪽에 handleSpellError() 에 대한 정의를 추가해주세요. 

```swift
func handleSpellError(error: ChangoSpellError) {
  let prefix = "Spell Failed."
  switch error {
    case .HatMissingOrNotMagical:
      print("\(prefix) 모자를 까먹었나요,아니면 충전된 배터리가 필요하나요?")
 
    case .FamiliarAlreadyAToad:
      print("\(prefix) 왜 두꺼비를 또 다시 두꺼비로 바꾸려고 하십니까?")
 
    default:
      print(prefix)
  }
}
```
아래코드를 플레이그라운드에 추가하고 마지막으로 실행해봅시다.
```swift
exampleOne()
```
Xcode 좌측코너의 화살표 아이콘을 눌러서 디버그콘솔 윈도우를 열어주세요. 플레이그라운드의 결과 값은 여기서 확인 가능합니다. 

![image](https://cdn1.raywenderlich.com/wp-content/uploads/2016/04/Expand-Debug-Area-1.gif)


## 에러 잡기 (Catching Errors)

위 코드 스니펫에서 사용된 언어의 특징을 간략히 아래에 설명했습니다. 아래를 보세요.

### catch

swift 에서는 패턴매칭을 사용할 수 있습니다. 이는 특정에러를 처리하기 위해서 또는 여러에러타입을 한꺼번에 처리할때 사용할 수 있습니다.

위 코드에서는 catch 의 용도 몇 가지를 보여줍니다: 첫번째로 특정 ChangoSpell 에러를 catch 할때, 두번째는 나머지 error 케이스에 대한 처리입니다. 

### try
 
코드상에 어떤 라인이나 섹션에서 에러가 발생할 수 있는지 확실하게 지정해 주기 위해서 try구문을 do-catch와 함께 사용할 수 있습니다.

아래처럼 try구문은 여러가지 방법으로 사용할 수 있습니다. 이중에 한가지가 위에서 사용된 방법이죠.
  
* try: 명확하고 즉각적인 do-catch 구문이며 표준적인 사용법입니다. 이 옵션이 위에서 사용되었습니다. 
* try?: 기본적으로 우선 무시하는 방법으로 에러를 처리하는 방법입니다. 혹시 에러가 발생되면 구문에 대한 결과값은 nil 이 될 겁니다.
* try!: 강제언래핑할때 사용된 문법과 비슷합니다. 이론상 이 구문을 사용하면 statement 가 에러를 뱉게될거라는 예상을 할 수 있습니다만, 사실상 에러조건은 절대로 발생하지 않습니다. try! 는 특정 미디어 파일을 로딩하는 등의 동작을 위해서 사용합니다. 강제언래핑과 마찬가지로 굉장히 주의깊게 사용해야 합니다.

이제 try? 구문이 사용되는 예를 봅시다. 아래 코드를 그대로 복사해서 여러분의 플레이그라운드에 추가해주세요. 

```swift
func exampleTwo() {
  print("") // Add an empty line in the debug area
 
  let toad = Toad(name: "Mr. Toad")
  toad.speak()
 
  let hat = Hat()
  let witchTwo = Witch(name: "Elphaba", familiar: toad, hat: hat)
 
  let newToad = try? witchTwo.turnFamiliarIntoToad()
  if newToad != nil { // Same logic as: if let _ = newToad
    print("Successfully changed familiar into toad.")
  }
  else {
    print("Spell failed.")
  }
}
```
Notice the difference with exampleOne. Here you don’t care about the output of the particular error, but still capture the fact that one occurred. The Toad was not created, so the value of newToad is nil.

## 에러 표시하기

### throws

함수나 메소드가 에러를 던지는 경우 throws 키워드를 사용합니다. 던져진 에러는 콜스택으로 자동으로 전달됩니다. 단 너무 멀리까지 이런 전파가 이뤄진다면 좋은 방법은 아닙니다.  과도한 에러전달은 적절한 에러처리를 방해하는 요소로 작용할 수 있습니다. throws 는 이러한 전파가 코드상에 문서화 됨을 확인시켜주기 위한 위임작업입니다. 

### rethrows
여태까지 사용했던 모든 예제에서 throws 를 사용했는데 rethrows 는 뭘까요?  `함수의 인자가 에러를 발생시킬때`만 throw 한다는 것을 컴파일러에게 알려주기 위한 것이 rethrows 입니다.
아래에서 마법과 같은 예시를 보시죠( 플레이그라운드에 추가할 필요는 없습니다)

```swift
func doSomethingMagical(magicalOperation: () throws -> MagicalResult) rethrows -> MagicalResult {
  return try magicalOperation()
}
```

doSomethingMagical(_:) 은 에러를 throw 하는데 그 조건과 결과는 아래와 같습니다. 

케이스1: 함수의 인자인 magicalOperation이 에러를 발생시킨다. =>  doSomethingMagical(_:) 도 에러를 발생시킴.
케이스2: magicalOperation 가 성공적이라면 MagicalResult 값을 반환한다.

##  에러처리 작동을 조작하기

### defer

Although auto-propagation will serve you well in most cases, there are situations where you might want to manipulate the behavior of your application as an error travels up the call stack.
The defer statement is a mechanism that permits a ‘cleanup’ action to be performed whenever the current scope is exited, such as when a method or function returns. It’s useful for managing resources that need to be tidied up whether or not the action was successful, and so becomes especially useful in an error handling context.

비록 대부분의 경우 자동전파기능은 잘 작동합니다만, 콜스택으로...

실제 어떻게 작동하는지 , Witch 구조체에 아래 메소드를 추가해주세요.

```swift
func speak() {
  defer {
    print("*cackles*")
  }
  print("Hello my pretties.")
}
```
Add the following code to the bottom of the playground:

```swift
func exampleThree() {
  print("") // Add an empty line in the debug area
 
  let witchThree = Witch(name: "Hermione", familiar: nil, hat: nil)
  witchThree.speak()
}
 
exampleThree()
```
In the debug console, you should see the witch cackle after everything she says.
Interestingly, defer statements are executed in the opposite order in which they are written.
Add a second defer statement to speak() so that a Witch screeches, then cackles after everything she says:

디버깅 콘솔상에서, 마녀가 말할 때마다 cackles 이라고 하는 것을 볼 수 있습니다. 흥미롭게도 defer 는 구현된 것과 반대로 동작합니다. 
두번째 defer ....

```swift
func speak() {
  defer {
    print("*cackles*")
  }
 
  defer {
    print("*screeches*")
  }
 
  print("Hello my pretties.")
}
```
Did the statements print in the order you expected? Ah, the magic of defer!

## 더 재미있는 에러처리를 위해

위와 같은 문법을 도입함으로써 스위프트는 다른 인기많은 언어들과 어깨를 나란히 하게  되었습니다. 또한  기존의 오브젝티브C 베이스의 NSError 접근방식과 분리시켰습니다.  

타 언어에서의 do-catch 구문은 언어자체의 큰 오버헤드를 주고 있는 반면, 스위프트에서는 이러한 문법들이 기존 문법처럼 쓰이고 있습니다. 이런 부분이 스위프트를 좀 더 효율적이고 효과적인 언어로 만들고 있습니다.

다만 에러처리와 throw 구문들을 사용할 수 있다고해서 이러한 특징들을 반드시 이용해야 만 하는 것은 아닙니다. 진행중인 프로젝트에서 throw 와 catch 를 사용할 때 반드시 가이드라인을 따라 개발해야 합니다. 특별히 아래와 같은 몇가지 방법을 추천합니다.

* 명확하고 깔끔한 에러타입을 사용해야 한다
* 하나의 에러스테이트를 가지는 경우에는 옵셔널을 사용한다
* 하나 이상의 에러가 존재하는 경우에 custom error 를 사용한다
* 소스로 부터 너무 멀리까지 에러를 전파해서는 안 된다

## Swift 에서 에러처리의 미래는?

여러 Swift 포럼사이에 고급 에러처리에 대한 여러가지 아이디어들이 나오고 있습니다. 그중 가장 활발히 논의되고 있는 것 중 하나는 untyped propagation 이라는 개념입니다.

Swift 3 에 소개된 주요 에러처리 방법 또는 기존의 방식중 어떤 것을 선호하던지간에 깔끔한 에러처리에 대한논의가 활발할 때 Swift 는 더욱 발전할 것 입니다.
  


## 자.이제 다음엔 무엇을 해야 하나?
이 강좌에서 완성된 플레이그라운드 코드는 [여기서](https://cdn2.raywenderlich.com/wp-content/uploads/2016/04/Magical-Error-Handling-in-Swift.zip) 받으세요.

더 깊이 알고 싶으신 분들은 아래 추천하는 포스팅들을 참고하시기 바랍니다. 일부의 내용들은 이 강좌에 언급된 내용들도 있습니다:

[Swift Apprentice, Chapter 21 – Error Handling](https://www.raywenderlich.com/store/swift-apprentice)

[Failable Initializers](http://bit.ly/1RMpOY3)

[Factory Method Pattern](https://en.wikipedia.org/wiki/Factory_method_pattern)

[Pyramid of Doom](https://en.wikipedia.org/wiki/Pyramid_of_doom_(programming))

 
## 역자의 한마디

Ray Weinerlich 사이트가 커지면서 오히려 Localization 에 신경 쓸 여력이 없다고 합니다.

제가 그동안 작업했던 공식홈페이지에 한글 포스팅 번역물들이 모두 사라져서 이곳에 포스팅합니다.

- Eddie Kwon -
- ever8greener@gmail.com -


## 추가링크들

[스위프트2.1에러처리] (https://blog.asamaru.net/2015/10/05/swift-2-dot-1-try-catch/)