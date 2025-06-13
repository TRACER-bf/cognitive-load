# 인지 부하가 진짜 중요하다

[가독성 좋은 버전](https://minds.md/zakirullin/cognitive) | [중국어 번역](https://github.com/zakirullin/cognitive-load/blob/main/README.zh-cn.md)

*이 문서는 살아있는 문서이며, 마지막 업데이트: **2025년 5월**. 여러분의 기여를 환영합니다!*

## 소개
수많은 유행어와 베스트 프랙티스가 있지만, 대부분은 실패했습니다. 우리는 더 근본적인 무언가가 필요합니다. 결코 틀릴 수 없는 무언가가요.

가끔 코드를 읽으면서 혼란을 느낍니다. 혼란은 시간과 돈을 낭비하게 만듭니다. 이 혼란의 원인은 바로 높은 *인지 부하*입니다. 이건 멋진 추상 개념이 아니라 **인간의 근본적인 한계**입니다. 상상 속의 것이 아니라 실제로 존재하며, 우리는 그것을 느낄 수 있습니다.

우리는 코드를 작성하는 것보다 읽고 이해하는 데 훨씬 더 많은 시간을 보냅니다. 따라서 우리는 항상 우리 코드에 과도한 인지 부하를 심고 있지는 않은지 스스로에게 물어봐야 합니다.

## 인지 부하란?
> 인지 부하는 개발자가 작업을 완료하기 위해 얼마나 많이 생각해야 하는지를 의미합니다.

코드를 읽을 때, 변수의 값, 제어 흐름, 호출 순서 등 여러 가지를 머릿속에 담아둡니다. 평균적으로 사람은 작업 기억(working memory)에 [네 개 정도의 덩어리(chunk)](https://github.com/zakirullin/cognitive-load/issues/16)만을 유지할 수 있습니다. 인지 부하가 이 임계값에 도달하면, 이해하는 것이 훨씬 더 어려워집니다.

*완전히 낯선 프로젝트에서 몇 가지 수정을 하라는 요청을 받았다고 가정해봅시다. 정말 똑똑한 개발자가 이 프로젝트에 기여했다고 들었습니다. 멋진 아키텍처, 최신 라이브러리, 트렌디한 기술들이 사용되었습니다. 즉, **작성자는 우리에게 높은 인지 부하를 남긴 셈입니다.***

<div align="center">
  <img src="/img/cognitiveloadv6.png" alt="Cognitive load" width="750">
</div>

우리는 프로젝트의 인지 부하를 가능한 한 줄여야 합니다.

<details>
  <summary><b>인지 부하와 방해(인터럽션)</b></summary>
  <img src="img/interruption.jpeg"><br>
</details>

## 인지 부하의 종류
**내재적(intrinsic)** - 작업 자체의 본질적인 난이도에서 비롯됩니다. 줄일 수 없으며, 소프트웨어 개발의 핵심에 있습니다.

**외재적(extraneous)** - 정보가 제시되는 방식에서 비롯됩니다. 작업과 직접적으로 관련 없는 요소(예: 똑똑한 작성자의 특이한 습관)로 인해 발생합니다. 크게 줄일 수 있습니다. 우리는 이 외재적 인지 부하에 집중할 것입니다.

<div align="center">
  <img src="/img/smartauthorv14thanksmari.png" alt="Intrinsic vs Extraneous" width="600">
</div>

이제 외재적 인지 부하의 구체적이고 실용적인 예시로 바로 넘어가겠습니다.

---

인지 부하의 수준을 다음과 같이 표현하겠습니다:  
`🧠`: 작업 기억이 신선한 상태, 인지 부하 없음  
`🧠++`: 작업 기억에 두 가지 사실이 들어옴, 인지 부하 증가  
`🤯`: 인지 과부하, 네 가지 이상을 기억해야 함  

> 우리의 뇌는 훨씬 더 복잡하고 미지의 영역이 많지만, 이 단순한 모델로 설명하겠습니다.

## 복잡한 조건문
```go
if val > someConstant // 🧠+
    && (condition2 || condition3) // 🧠+++, 이전 조건이 true여야 하고, condition2나 condition3 중 하나가 true여야 함
    && (condition4 && !condition5) { // 🤯, 이쯤 되면 머리가 복잡해짐
    ...
}
```

의미 있는 이름의 중간 변수를 도입하세요:
```go
isValid = val > someConstant
isAllowed = condition2 || condition3
isSecure = condition4 && !condition5 
// 🧠, 조건을 기억할 필요 없이, 설명적인 변수만 기억하면 됨
if isValid && isAllowed && isSecure {
    ...
}
```

## 중첩된 if문
```go
if isValid { // 🧠+, 이제부터 중첩 코드는 유효한 입력에만 적용됨
    if isSecure { // 🧠++, 유효하고 안전한 입력에만 적용됨
        stuff // 🧠+++
    }
} 
```

조기 반환(early return)과 비교해보세요:
```go
if !isValid
    return
 
if !isSecure
    return

// 🧠, 이전 반환 조건은 신경 쓸 필요 없음, 여기까지 왔으면 모두 통과한 것

stuff // 🧠+
```

행복 경로(happy path)에만 집중할 수 있으므로, 모든 전제 조건에서 작업 기억을 해방시킬 수 있습니다.

## 상속의 악몽
관리자(admin) 사용자를 위해 몇 가지를 변경하라는 요청을 받았습니다: `🧠`

`AdminController extends UserController extends GuestController extends BaseController`

오, 기능 일부가 `BaseController`에 있네요. 한번 살펴봅시다: `🧠+`  
기본 역할 메커니즘은 `GuestController`에서 도입됨: `🧠++`  
일부 기능은 `UserController`에서 부분적으로 변경됨: `🧠+++`  
드디어 `AdminController`에 도달, 이제 코딩! `🧠++++`  

잠깐, `SuperuserController`가 `AdminController`를 상속하네요. `AdminController`를 수정하면 상속받은 클래스에 영향을 줄 수 있으니, 먼저 `SuperuserController`도 살펴봐야겠죠: `🤯`

상속보다는 합성(composition)을 선호하세요. 자세한 내용은 [관련 자료](https://www.youtube.com/watch?v=hxGOiiR9ZKg)를 참고하세요.

## 너무 많은 작은 메서드, 클래스, 모듈
> 여기서 메서드, 클래스, 모듈은 서로 바꿔 쓸 수 있습니다

"메서드는 15줄 이하로 짧아야 한다"거나 "클래스는 작아야 한다"는 식의 만트라는 다소 잘못된 것으로 드러났습니다.

**깊은(deep) 모듈** - 단순한 인터페이스, 복잡한 기능  
**얕은(shallow) 모듈** - 제공하는 기능에 비해 인터페이스가 상대적으로 복잡함

<div align="center">
  <img src="/img/deepmodulev8.png" alt="Deep module" width="700">
</div>

너무 많은 얕은 모듈이 있으면 프로젝트를 이해하기 어려워집니다. **각 모듈의 책임뿐만 아니라, 이들 간의 모든 상호작용까지 기억해야 하기 때문입니다.** 얕은 모듈의 목적을 이해하려면, 관련된 모든 모듈의 기능을 먼저 살펴봐야 합니다. `🤯`

> 정보 은닉이 가장 중요하지만, 얕은 모듈에서는 복잡성을 충분히 숨기지 못합니다.

저는 두 개의 개인 프로젝트가 있는데, 둘 다 약 5,000줄 정도의 코드입니다. 첫 번째 프로젝트에는 80개의 얕은 클래스가 있고, 두 번째에는 7개의 깊은 클래스만 있습니다. 1년 반 동안 이 프로젝트들을 관리하지 않았습니다.

다시 돌아와 보니, 첫 번째 프로젝트의 80개 클래스 간의 상호작용을 풀어내는 것이 매우 어려웠습니다. 코딩을 시작하기 전에 엄청난 양의 인지 부하를 다시 쌓아야 했죠. 반면, 두 번째 프로젝트는 단순한 인터페이스를 가진 몇 개의 깊은 클래스만 있어서 금방 파악할 수 있었습니다.

> 최고의 컴포넌트는 강력한 기능을 제공하면서도 단순한 인터페이스를 가진 것들입니다.  
> **John K. Ousterhout**

UNIX I/O의 인터페이스는 매우 단순합니다. 기본적으로 다섯 가지 호출만 있습니다:
```python
open(path, flags, permissions)
read(fd, buffer, count)
write(fd, buffer, count)
lseek(fd, offset, referencePosition)
close(fd)
```

이 인터페이스의 현대적 구현은 **수십만 줄의 코드**로 이루어져 있습니다. 엄청난 복잡성이 내부에 숨겨져 있지만, 단순한 인터페이스 덕분에 사용하기 쉽습니다.

> 이 깊은 모듈의 예시는 John K. Ousterhout의 저서 [A Philosophy of Software Design](https://web.stanford.edu/~ouster/cgi-bin/book.php)에서 가져왔습니다. 이 책은 소프트웨어 개발에서 복잡성의 본질을 다루며, Parnas의 영향력 있는 논문 [On the Criteria To Be Used in Decomposing Systems into Modules](https://www.win.tue.nl/~wstomv/edu/2ip30/references/criteria_for_modularization.pdf)에 대한 최고의 해석을 담고 있습니다. 둘 다 필독서입니다. 기타 관련 자료: [A Philosophy of Software Design vs Clean Code](https://github.com/johnousterhout/aposd-vs-clean-code), [It's probably time to stop recommending Clean Code](https://qntm.org/clean), [Small Functions considered Harmful](https://copyconstruct.medium.com/small-functions-considered-harmful-91035d316c29).

P.S. 너무 많은 책임을 가진 비대한 God 객체를 옹호하는 것이 아닙니다.

## 한 가지 책임 원칙
우리는 종종 "모듈은 한 가지, 오직 한 가지 일만 책임져야 한다"는 모호한 원칙을 따라 얕은 모듈을 많이 만들게 됩니다. 그런데 이 '한 가지'가 무엇일까요? 객체를 인스턴스화하는 것도 한 가지 일이죠? 그래서 [MetricsProviderFactoryFactory](https://minds.md/benji/frameworks) 같은 클래스도 괜찮아 보입니다. 이런 클래스들은 이름과 인터페이스가 구현 전체보다 더 정신적으로 부담스럽습니다. 뭔가 잘못된 거죠.

> 이런 얕은 컴포넌트 사이를 오가는 것은 정신적으로 매우 피곤합니다. [선형적 사고](https://blog.separateconcerns.com/2023-09-11-linear-code.html)가 인간에게 더 자연스럽습니다.

우리는 시스템을 변경하여 사용자와 이해관계자를 만족시키기 위해 노력합니다. 우리는 그들에게 책임이 있습니다.

> 모듈은 한 명의 사용자 또는 이해관계자에게만 책임져야 합니다.

이것이 바로 단일 책임 원칙(Single Responsibility Principle)의 본질입니다. 즉, 한 곳에 버그가 생겼을 때, 두 명의 서로 다른 비즈니스 담당자가 와서 불평한다면, 우리는 이 원칙을 위반한 것입니다. 모듈에서 처리하는 일의 개수와는 상관이 없습니다.

하지만 이 규칙조차도 해석하는 사람마다 다르게 받아들여져 오히려 해가 될 수 있습니다. 더 나은 접근법은 모든 것이 얼마나 많은 인지 부하를 유발하는지 살펴보는 것입니다. 한 곳의 변경이 여러 비즈니스 흐름에 연쇄적으로 영향을 미친다는 사실을 기억하는 것은 정신적으로 매우 부담스럽습니다. 결국, 복잡한 용어를 외울 필요 없이, 인지 부하만 신경 쓰면 됩니다.

## 너무 많은 얕은 마이크로서비스
이 얕은-깊은 모듈 원칙은 규모에 상관없이 적용할 수 있으며, 마이크로서비스 아키텍처에도 마찬가지입니다. 너무 많은 얕은 마이크로서비스는 아무런 도움이 되지 않습니다. 업계는 점점 "매크로서비스"(=깊은 서비스) 쪽으로 나아가고 있습니다. 가장 심각하고 고치기 힘든 현상 중 하나는 이 과도하게 세분화된 얕은 분리로 인해 발생하는 분산 모놀리식(distributed monolith)입니다.

제가 한 스타트업을 컨설팅한 적이 있는데, 다섯 명의 개발자가 17개의(!) 마이크로서비스를 도입했습니다. 이들은 일정에 10개월이나 뒤처져 있었고, 출시도 요원해 보였습니다. 새로운 요구사항이 생길 때마다 4개 이상의 마이크로서비스를 수정해야 했습니다. 통합 영역에서의 진단 난이도는 치솟았고, 출시까지 걸리는 시간과 인지 부하는 감당할 수 없을 정도였습니다. `🤯`

새로운 시스템의 불확실성에 이렇게 접근하는 것이 맞을까요? 논리적 경계를 처음부터 제대로 나누는 것은 매우 어렵습니다. 핵심은, 책임감 있게 최대한 늦게 결정을 내리는 것입니다. 그때가 가장 많은 정보를 바탕으로 결정할 수 있기 때문입니다. 네트워크 계층을 미리 도입하면, 설계 결정을 되돌리기 어렵게 만듭니다. 이 팀의 유일한 변명은 "FAANG 기업들이 마이크로서비스 아키텍처가 효과적임을 증명했다"였습니다. *이제 그만 꿈에서 깨어나세요.*

[Tanenbaum-Torvalds 논쟁](https://en.wikipedia.org/wiki/Tanenbaum%E2%80%93Torvalds_debate)에서는 리눅스의 모놀리식 설계가 잘못됐고, 마이크로커널 아키텍처를 써야 한다고 주장했습니다. 이론적으로는 마이크로커널이 더 우월해 보였지만, 실제로는 30년이 지난 지금도 마이크로커널 기반 GNU Hurd는 개발 중이고, 모놀리식 리눅스는 어디에나 있습니다. 이 페이지도 리눅스로 구동되고, 여러분의 스마트 주전자도 리눅스로 구동됩니다. 바로 모놀리식 리눅스로요.

진정으로 분리된 모듈을 가진 잘 만들어진 모놀리식이 여러 마이크로서비스보다 훨씬 더 유연한 경우가 많습니다. 유지보수에 필요한 인지 노력도 훨씬 적습니다. 별도의 배포가 정말로 필요해질 때(예: 개발팀 확장)만 네트워크 계층을 도입해도 늦지 않습니다.

## 기능이 풍부한 언어
우리는 좋아하는 언어에 새로운 기능이 추가되면 신이 납니다. 그 기능을 배우고, 코드를 작성합니다.

기능이 많으면, 몇 줄의 코드를 작성하는 데도 30분씩 고민하게 됩니다. 이건 시간 낭비일 뿐만 아니라, **나중에 다시 돌아왔을 때 그 사고 과정을 다시 재현해야 한다는 점이 더 문제입니다!**

**복잡한 프로그램뿐만 아니라, 왜 개발자가 그 기능을 선택했는지까지 이해해야 합니다.** `🤯`

이 말은 바로 Rob Pike가 한 말입니다.

> 선택지를 줄여서 인지 부하를 낮추세요.

언어 기능은 서로 독립적(orthogonal)일 때 괜찮습니다.

<details>
  <summary><b>20년 경력의 C++ 엔지니어의 생각 ⭐️</b></summary>
  <br>
  최근 RSS 리더기를 보니 "C++" 태그로 300개 정도의 읽지 않은 글이 쌓여 있었습니다. 작년 여름 이후로 언어 관련 글을 한 편도 읽지 않았는데, 기분이 정말 좋더군요.<br><br>
  20년 동안 C++을 써왔고, 인생의 2/3를 이 언어와 함께 했습니다. 대부분은 언어의 어두운 구석(각종 undefined behavior 등)을 다루는 경험이었죠. 이 경험은 재사용할 수 없고, 이제 와서 다 버리기도 아깝습니다.<br><br>
  예를 들어, <code>||</code> 토큰은 <code>requires ((!P&lt;T&gt; || !Q&lt;T&gt;))</code>와 <code>requires (!(P&lt;T&gt; || Q&lt;T&gt;))</code>에서 의미가 다릅니다. 첫 번째는 제약 조건의 논리합, 두 번째는 기존의 논리 OR 연산자이며, 동작도 다릅니다.<br><br>
  단순 타입에 공간을 할당하고 <code>memcpy</code>로 바이트를 복사하는 것만으로는 객체의 생명주기가 시작되지 않습니다. C++20 이전에는 그랬고, C++20에서야 고쳐졌지만, 언어의 인지 부하는 오히려 늘었습니다.<br><br>
  인지 부하는 계속 늘어납니다. 고쳐진 부분이 있어도, 언제 고쳐졌는지, 이전에는 어땠는지까지 알아야 하니까요. 저는 전문가니까요. 물론 C++은 레거시 지원이 뛰어나지만, 그만큼 레거시를 반드시 마주하게 됩니다. 예를 들어, 지난달 동료가 C++03에서의 동작을 물어보더군요. <code>🤯</code><br><br>
  초기화 방법이 20가지나 있었습니다. 통일된 초기화 문법이 추가됐지만, 이제 21가지가 됐죠. 혹시 초기화 리스트에서 생성자 선택 규칙을 기억하는 분 있나요? 암시적 변환에서 정보 손실이 가장 적은 쪽을 선택한다던가, <i>하지만</i> 값이 정적으로 알려져 있으면... <code>🤯</code><br><br>
  <b>이렇게 늘어난 인지 부하는 비즈니스 과제에서 비롯된 본질적 복잡성이 아닙니다. 단지 역사적 이유(=외재적 인지 부하)로 존재할 뿐입니다.</b><br><br>
  저는 몇 가지 규칙을 만들었습니다. 표준을 기억해야 할 정도로 명확하지 않은 코드는 아예 쓰지 않는다는 식이죠. 참고로 표준은 1,500페이지나 됩니다.<br><br>
  <b>C++을 탓하려는 게 아닙니다.</b> 저도 이 언어를 좋아합니다. 다만, 이제 좀 지쳤을 뿐입니다.<br><br>
  <p>글쓴이: <a href="https://0xd34df00d.me" target="_blank">0xd34df00d</a></p>
</details>

## Business logic and HTTP status codes
On the backend we return:  
`401` for expired jwt token  
`403` for not enough access  
`418` for banned users  

The engineers on the frontend use backend API to implement login functionality. They would have to temporarily create the following cognitive load in their brains:  
`401` is for expired jwt token // `🧠+`, ok just temporary remember it  
`403` is for not enough access // `🧠++`  
`418` is for banned users // `🧠+++`  

Frontend developers would (hopefully) introduce some kind `numeric status -> meaning` dictionary on their side, so that subsequent generations of contributors wouldn't have to recreate this mapping in their brains.

Then QA engineers come into play:
"Hey, I got `403` status, is that expired token or not enough access?"
**QA engineers can't jump straight to testing, because first they have to recreate the cognitive load that the engineers on the backend once created.**

Why hold this custom mapping in our working memory? It's better to abstract away your business details from the HTTP transfer protocol, and return self-descriptive codes directly in the response body:
```json
{
    "code": "jwt_has_expired"
}
```

Cognitive load on the frontend side: `🧠` (fresh, no facts are held in mind)  
Cognitive load on the QA side: `🧠`

The same rule applies to all sorts of numeric statuses (in the database or wherever) - **prefer self-describing strings**. We are not in the era of 640K computers to optimise for memory.  

> People spend time arguing between `401` and `403`, making decisions based on their own mental models. New developers are coming in, and they need to recreate that thought process. You may have documented the "whys" (ADRs) for your code, helping newcomers to understand the decisions made. But in the end it just doesn't make any sense. We can separate errors into either user-related or server-related, but apart from that, things are kind of blurry. 

P.S. It's often mentally taxing to distinguish between "authentication" and "authorization". We can use simpler terms like ["login" and "permissions"](https://ntietz.com/blog/lets-say-instead-of-auth/) to reduce the cognitive load.

## Abusing DRY principle

Do not repeat yourself - that is one of the first principles you are taught as a software engineer. It is so deeply embedded in ourselves that we can not stand the fact of a few extra lines of code. Although in general a good and fundamental rule, when overused it leads to the cognitive load we can not handle.

Nowadays, everyone builds software based on logically separated components. Often those are distributed among multiple codebases representing separate services. When you strive to eliminate any repetition, you might end up creating tight coupling between unrelated components. As a result changes in one part may have unintended consequences in other seemingly unrelated areas. It can also hinder the ability to replace or modify individual components without impacting the entire system. `🤯`  

In fact, the same problem arises even within a single module. You might extract common functionality too early, based on perceived similarities that might not actually exist in the long run. This can result in unnecessary abstractions that are difficult to modify or extend.  

Rob Pike once said:

> A little copying is better than a little dependency.  

We are tempted to not reinvent the wheel so strong that we are ready to import large, heavy libraries to use a small function that we could easily write by ourselves.  

**All your dependencies are your code.** Going through 10+ levels of stack trace of some imported library and figuring out what went wrong (*because things go wrong*) is painful.  

## Tight coupling with a framework
There's a lot of "magic" in frameworks. By relying too heavily on a framework, **we force all upcoming developers to learn that "magic" first**. It can take months. Even though frameworks enable us to launch MVPs in a matter of days, in the long run they tend to add unnecessary complexity and cognitive load.

Worse yet, at some point frameworks can become a significant constraint when faced with a new requirement that just doesn't fit the architecture. From here onwards people end up forking a framework and maintaining their own custom version. Imagine the amount of cognitive load a newcomer would have to build (i.e. learn this custom framework) in order to deliver any value. `🤯`

**By no means do we advocate to invent everything from scratch!**

We can write code in a somewhat framework-agnostic way. The business logic should not reside within a framework; rather, it should use the framework's components. Put a framework outside of your core logic. Use the framework in a library-like fashion. This would allow new contributors to add value from day one, without the need of going through debris of framework-related complexity first.  

> [Why I Hate Frameworks](https://minds.md/benji/frameworks)

## Layered architecture
There is a certain engineering excitement about all this stuff.

I myself was a passionate advocate of Hexagonal/Onion Architecture for years. I used it here and there and encouraged other teams to do so. The complexity of our projects went up, the sheer number of files alone had doubled. It felt like we were writing a lot of glue code. On ever changing requirements we had to make changes across multiple layers of abstractions, it all became tedious. `🤯`

Abstraction is supposed to hide complexity, here it just adds [indirection](https://fhur.me/posts/2024/thats-not-an-abstraction). Jumping from call to call to read along and figure out what goes wrong and what is missing is a vital requirement to quickly solve a problem. With this architecture's layer uncoupling it requires an exponential factor of extra, often disjointed, traces to get to the point where the failure occurs. Every such trace takes space in our limited working memory. `🤯`  

This architecture was something that made intuitive sense at first, but every time we tried applying it to projects it made a lot more harm than good. In the end, we gave it all up in favour of the good old dependency inversion principle. **No port/adapter terms to learn, no unnecessary layers of horizontal abstractions, no extraneous cognitive load.**

<details>
  <summary><b>Coding principles and experience</b></summary>
  <img src="img/complexity.png"><br>
  <a href="https://twitter.com/flaviocopes">@flaviocopes</a>
</details>

If you think that such layering will allow you to quickly replace a database or other dependencies, you're mistaken. Changing the storage causes lots of problems, and believe us, having some abstractions for the data access layer is the least of your worries. At best, abstractions can save somewhat 10% of your migration time (if any), the real pain is in data model incompatibilities, communication protocols, distributed systems challenges, and [implicit interfaces](https://www.hyrumslaw.com).  

> With a sufficient number of users of an API,  
> it does not matter what you promise in the contract:  
> all observable behaviors of your system  
> will be depended on by somebody.

We did a storage migration, and that took us about 10 months. The old system was single-threaded, so the exposed events were sequential. All our systems depended on that observed behaviour. This behavior was not part of the API contract, it was not reflected in the code. A new distributed storage didn't have that guarantee - the events came out-of-order. We spent only a few hours coding a new storage adapter, thanks to an abstraction. **We spent the next 10 months on dealing with out-of-order events and other challenges.** It's now funny to say that abstractions helps us replace components quickly.  

**So, why pay the price of high cognitive load for such a layered architecture, if it doesn't pay off in the future?** Plus, in most cases, that future of replacing some core component never happens.  

These architectures are not fundamental, they are just subjective, biased consequences of more fundamental principles. Why rely on those subjective interpretations? Follow the fundamental rules instead: dependency inversion principle, single source of truth, cognitive load and information hiding. Your business logic should not depend on low-level modules like database, UI or framework. We should be able to write tests for our core logic without worrying about the infrastructure, and that's it. [Discuss](https://github.com/zakirullin/cognitive-load/discussions/24).

Do not add layers of abstractions for the sake of an architecture. Add them whenever you need an extension point that is justified for practical reasons.

**[Layers of abstraction aren't free of charge](https://blog.jooq.org/why-you-should-not-implement-layered-architecture), they are to be held in our limited working memory**.

<div align="center">
  <img src="/img/layers.png" alt="Layers" width="400">
</div>

## Domain-driven design
Domain-driven design has some great points, although it is often misinterpreted. People say "We write code in DDD", which is a bit strange, because DDD is about problem space, not about solution space.

Ubiquitous language, domain, bounded context, aggregate, event storming are all about problem space. They are meant to help us learn the insights about the domain and extract the boundaries. DDD enables developers, domain experts and business people to communicate effectively using a single, unified language. Rather than focusing on these problem space aspects of DDD, we tend to emphasise particular folder structures, services, repositories, and other solution space techniques. 

Chances are that the way we interpret DDD is likely to be unique and subjective. And if we build code upon this understanding, i.e., if we create a lot of extraneous cognitive load - future developers are doomed. `🤯`  

Team Topologies provides a much better, easier to understand framework that helps us split the cognitive load across teams. Engineers tend to develop somewhat similar mental models after learning about Team Topologies. DDD, on the other hand, seems to be creating 10 different mental models for 10 different readers. Instead of being common ground, it becomes a battleground for unnecessary debates.  

## Examples
- Our architecture is a standard CRUD app architecture, [a Python monolith on top of Postgres](https://danluu.com/simple-architectures/)
- How Instagram scaled to 14 million users with [only 3 engineers](https://read.engineerscodex.com/p/how-instagram-scaled-to-14-million)
- The companies where we were like "woah, these folks are [smart as hell](https://kenkantzer.com/learnings-from-5-years-of-tech-startup-code-audits/)") for the most part failed
- One function that wires up the entire system. If you want to know how the system works - [go read it](https://www.infoq.com/presentations/8-lines-code-refactoring)

These architectures are quite boring and easy to understand. Anyone can grasp them without much mental effort.

Involve junior developers in architecture reviews. They will help you to identify the mentally demanding areas.

## Cognitive load in familiar projects

> The problem is that **familiarity is not the same as simplicity**. They *feel* the same — that same ease of moving through a space without much mental effort — but for very different reasons. Every "clever" (read: "self-indulgent") and non-idiomatic trick you use incurs a learning penalty for everyone else. Once they have done that learning, then they will find working with the code less difficult. So it is hard to recognise how to simplify code that you are already familiar with. This is why I try to get "the new kid" to critique the code before they get too institutionalised!  
>
> It is likely that the previous author(s) created this huge mess one tiny increment at a time, not all at once. So you are the first person who has ever had to try to make sense of it all at once.  
>
> In my class I describe a sprawling SQL stored procedure we were looking at one day, with hundreds of lines of conditionals in a huge WHERE clause. Someone asked how anyone could have let it get this bad. I told them: "When there are only 2 or 3 conditionals, adding another one doesn't make any difference. By the time there are 20 or 30 conditionals, adding another one doesn't make any difference!"  
>
> There is no "simplifying force" acting on the code base other than deliberate choices that you make. Simplifying takes effort, and people are too often in a hurry.  
>
> *Thanks to [Dan North](https://dannorth.net) for his comment*.  

If you've internalized the mental models of the project into your long-term memory, you won't experience a high cognitive load.  

<div align="center">
  <img src="/img/mentalmodelsv15.png" alt="Mental models" width="700">
</div>

The more mental models there are to learn, the longer it takes for a new developer to deliver value.  

Once you onboard new people on your project, try to measure the amount of confusion they have (pair programming may help). If they're confused for more than ~40 minutes in a row - you've got things to improve in your code.  

If you keep the cognitive load low, people can contribute to your codebase within the first few hours of joining your company.

## Conclusion
Imagine for a moment that what we inferred in the second chapter isn't actually true. If that's the case, then the conclusion we just negated, along with the conclusions in the previous chapter that we had accepted as valid, might not be correct either. `🤯`

Do you feel it? Not only do you have to jump all over the article to get the meaning (shallow modules!), but the paragraph in general is difficult to understand. We have just created an unnecessary cognitive load in your head. **Do not do this to your colleagues.**

<div align="center">
  <img src="/img/smartauthorv14thanksmari.png" alt="Smart author" width="600">
</div>

We should reduce any cognitive load above and beyond what is intrinsic to the work we do. 

---
[LinkedIn](https://www.linkedin.com/in/zakirullin/), [X](https://twitter.com/zakirullin), [GitHub](https://github.com/zakirullin)

[Readable version](https://minds.md/zakirullin/cognitive)

<details>
    <summary><b>Comments</b></summary>
    <br>
    <p><strong>Rob Pike</strong><br>Nice article.</p>
    <p><strong><a href="https://x.com/karpathy/status/1872038630405054853" target="_blank">Andrej Karpathy</a></strong> <i>(ChatGPT, Tesla)</i><br>Nice post on software engineering. Probably the most true, least practiced viewpoint.</p>
    <p><strong><a href="https://x.com/elonmusk/status/1872346903792566655" target="_blank">Elon Musk</a></strong><br>True.</p>
    <p><strong><a href="https://www.linkedin.com/feed/update/urn:li:activity:7277757844970520576/" target="_blank">Addy Osmani</a></strong> <i>(Chrome, the most complex software system in the world)</i><br>I've seen countless projects where smart developers created impressive architectures using the latest design patterns and microservices. But when new team members tried to make changes, they spent weeks just trying to understand how everything fits together. The cognitive load was so high that productivity plummeted and bugs multiplied.</p>
    <p>The irony? Many of these complexity-inducing patterns were implemented in the name of "clean code."</p>
    <p>What really matters is reducing unnecessary cognitive burden. Sometimes this means fewer, deeper modules instead of many shallow ones. Sometimes it means keeping related logic together instead of splitting it into tiny functions.</p>
    <p>And sometimes it means choosing boring, straightforward solutions over clever ones. The best code isn't the most elegant or sophisticated - it's the code that future developers (including yourself) can understand quickly.</p>
    <p>Your article really resonates with the challenges we face in browser development. You're absolutely right about modern browsers being among the most complex software systems. Managing that complexity in Chromium is a constant challenge that aligns perfectly with many of the points you made about cognitive load.</p>
    <p>One way we try to handle this in Chromium is through careful component isolation and well-defined interfaces between subsystems (like rendering, networking, JavaScript execution, etc.). Similar to your deep modules example with Unix I/O - we aim for powerful functionality behind relatively simple interfaces. For instance, our rendering pipeline handles incredible complexity (layout, compositing, GPU acceleration) but developers can interact with it through clear abstraction layers.</p>
    <p>Your points about avoiding unnecessary abstractions really hit home too. In browser development, we constantly balance between making the codebase approachable for new contributors while handling the inherent complexity of web standards and compatibility. </p>
    <p>Sometimes the simplest solution is the best one, even in a complex system.</p>
    <p><strong><a href="https://x.com/antirez" target="_blank">antirez</a></strong> <i>(Redis)</i><br>Totally agree about it :) Also, what I believe is missing from mentioned "A Philosophy of Software Design" is the concept of "design sacrifice". That is, sometimes you sacrifice something and get back simplicity, or performances, or both. I apply this idea continuously, but often is not understood.</p>
    <p>A good example is the fact that I always refused to have hash items expires. This is a design sacrifice because if you have certain attributes only in the top-level items (the keys themselves), the design is simpler, values will just be objects. When Redis got hash expires, it was a nice feature but required (indeed) many changes to many parts, raising the complexity.</p>
    <p>Another example is what I'm doing right now, Vector Sets, the new Redis data type. I decided that Redis would not be the source of truth about vectors, but that it can just take an approximate version of them, so I was able to do on-insert normalization, quantization without trying to retain the large floats vector on disk, and so forth. May vector DBs don't sacrifice the fact of remembering what the user put inside (the full precision vector).</p>
    <p>These are just two random examples, but I apply this idea everywhere. Now the thing is: of course one must sacrifice the right things. Often, there are 5% features that account for a very large amount of complexity: that is a good thing to kill :D</p>
</details>
