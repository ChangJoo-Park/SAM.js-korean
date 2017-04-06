# SAM

## SAM 패턴

SAM(State-Action-Model)은 비지니스 로직을 뷰에서 엄격하게 분리하여 프론트엔드 아키텍처를 단순화하는 새로운 반응형/함수형 패턴입니다. SAM은 다음 두가지 기본 원칙을 따릅니다.

1. 뷰와 모델 사이의 관계는 반드시 함수형이어야한다
2. 상태 변이는 반드시 프로그래밍 모델의 일급 클래스이어야 한다


마우스 클릭과 같은 특정 사용자 이벤트를 계산하는 간단한 애플리케이션과 애플리케이션에 포함된 모델을 살펴봅니다.

```javascript
var model = { counter: 0 }
```

대부분의 소프트웨어 엔지니어는 애플리케이션 상태 변이를 값을 할당하는 방식으로 구현합니다.

```javascript
model.counter = model.counter + 1;
```

그리고 최신 프레임워크(Angular2, Reactjs, MobX, Cycle.js...)는 이러한 모델의 변경에 "반응"하기 위해 설계되었습니다. 종종 애플리케이션 상태 변이의 기본 단위의 작업을 고려하지 않습니다.

SAM은 이러한 모델에 도전하고 Paxos 프로토콜 및 TLA+ 기반 애플리케이션의 상태 변이를 명확하게 격리할 것을 제안합니다.


```javascript
// 액션은 모델이 변경해야하는 값을 계산하고 제안합니다.
let proposed_value = model.counter + 1 ;

// 모델은 이러한 값을 허용하거나 거부합니다.
model.accept({counter: proposed_value})
  // 반응 루프는 뷰를 모델의 단순한 함수를 이용해 렌더링합니다
  .then(
    (model) => {
      let stateRepresentation = ```Counter: ${model.counter}``` ;
      view.display(stateRepresentation) ;
    }) ;
```

SAM은 간결함으로 인하여 "unapologetically" driven 이고 Google의 Angular 또는 Facebook의 React+JSX+Flux/Redux+Saga+Thunk+GraphQL+Relay와 같은 프레임워크의 복잡성에 도전합니다. SAM의 주요 목표 중 하나는 모든 사람들이 아름답고 매력적인 HTML5/CSS3/JavaScript 웹 응용 프로그램을 만들면서 [React.js를 매우 독창적으로 만드는 것](https://medium.com/@dan_abramov/youre-missing-the-point-of-react-a20e34a51e1a#.aoccyh29h)을 유지하는 것입니다.

- 구성
- 단방향 데이터흐름
- DSL로부터의 자유
- 명시적인 변이
- 정적 mental 모델

SAM은 [BFF](http://samnewman.io/patterns/architectural/bff/) 또는 플랫폼, 앱, 버전별로 뷰 전용 API를 생성하는 애플리케이션과 같은 수직형 슬라이스 패턴과 같은 아키텍처 패턴에도 도전하고 있습니다.

10분짜리 안내 동영상 입니다.

<div style="position:relative;height:0;padding-bottom:75.0%"><iframe src="https://www.youtube.com/embed/lMgSS_b98S8?ecver=2" width="480" height="360" frameborder="0" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>

[낚시 게임 코드](https://hyperdev.com/#!/project/plain-frog)를 살펴보거나 [실행](https://plain-frog.hyperdev.space/) 해보세요

이 새로운 패턴은 [Jean-Jacques Dubray](http://www.ebpml.org/about)가 쓴[InfoQ article](http://www.infoq.com/articles/no-more-mvc-frameworks)에서 처음 발표되었습니다. [중국어](http://www.infoq.com/cn/articles/no-more-mvc-frameworks), [일본어](http://www.infoq.com/jp/articles/no-more-mvc-frameworks), [프랑스어](http://www.infoq.com/fr/articles/no-more-mvc-frameworks), [러시아어](http://www.infoq.com/ru/articles/no-more-mvc-frameworks)로도 번역되어 있습니다.

[Join the disscussion Gitter](https://gitter.im/jdubray/sam)

[Tweets](https://twitter.com/intent/tweet?original_referer=http%3A%2F%2Fsam.js.org%2Findex.html&ref_src=twsrc%5Etfw&text=SAM%20-%20State%20%7C%20Action%20%7C%20Model&tw_p=tweetbutton&url=http%3A%2F%2Fsam.js.org%2Findex.html)

### SAM은 어떻게 동작하나요?

SAM은 매우 간단하지만 컴퓨터 과학의 기초 중 하나(TLA+)를 기반으로 합니다.
SAM은 세가지 요소(액션, 모델 그리고 스테이트)에 따라 GUI의 기본 비지니스 로직을 고려할 것을 권장합니다.

- 액션은 이벤트에 의해 트리거되며, 모델에 제안된 값으로 이벤트를 변환하는 역할을 합니다.
- 모델은 이러한 값을 적용하는데 전적으로 책임을 갖습니다.
- 일단 적용되면 스테이트 함수는 모델 속성 값으로부터 스테이트 표현식을 계산하고 "스테이트 표현"을 표시할 보기와 같이 새 애플리케이션 상태에 대해 "배워야(learn)"할 모든 것들에 알립니다.
- 그 후, 스테이트 함수는 애플리케이션 스테이트가 주어지면 자동 실행하는 액션을 호출할 next-action-predicate를 계산합니다.


모든 사용자 또는 서버 이벤트는 제안(propose)/승인(accept)/학습(learn) "단계"로 처리됩니다.
이 것이 SAM의 전부입니다.
패턴은 단일 스테이트 트리, 단 방향 데이터 흐름 및 모델의 순수 함수를 이용한 뷰로 구현됩니다
패턴은 반응 루프를 만들고, 이벤트는 모델에 데이터를 표시하는 동작을 트리거하여 스테이트를 렌더링합니다. 논리적으로 매 단계마다 새로운 "스테이트 표현"이 만들어집니다. (이는 모델의 순수 함수를 이용합니다)

SAM 패턴은 뷰를 비즈니스 로직에서 완전히 분리할 수 있습니다. 아래의 많은 샘플에서 볼 수 있듯이, 뷰는 모델 또는 동작에 대한 지식이 없는 일종의 스테이트리스 컴포넌트로 구현됩니다.

SAM을 배울 때 프로그래밍 모델(State, Action, Model), 배선(wiring) (패턴 엘리먼트들이 통신하는 방법) 및 아키텍처 (패턴 엘리먼트들이 실제로 실행되는 곳)을 명확히 구분할 것을 권장합니다. SAM은 다양한 배선 방식을 지원하며 클라이언트에서 서버로 또는 반대([동형 자바스크립트](http://sam.js.org/index.html#iso))로 쉽게 마이그레이션할 수 있는 코드를 작성할 수 있습니다.

![The State-Action-Model (SAM) Pattern](http://sam.js.org/assets/figures/fig6.jpg)

### 모델의 함수로써의 뷰

SAM을 익힐 때 가장 좋은 출발점은 뷰와 모델간 기능적 관계를 이해하는 것 입니다. React.js에 익숙하면 React의 컴포넌트 모델의 주요 목표중 하나는 컴포넌트의 렌더링 기능을 만드는 것임을 알 수 있습니다. SAM은 동일한 접근을 하고 있고 뷰를 일련의 순수 함수로 만들 것을 제안합니다.

```javascript
V = f( M )
```

이 개념은 매우 간단하게 구현할 수 있습니다. 일반적으로 프론트엔드 컴포넌트를 테마 싱글톤의 일부로 구현합니다.

```javascript
var theme = {} ;

// React
theme.ready = (counter,intents) => <Counter count={counter} action={intents['start']}/>

var Counter = ({counter,action}) =>  (
    <p>Counter: {counter} </p>
    <button onClick={action}>
      Start!
    </button>
  )

// Angular2 (with Directive)

view.ready = function(model) {
    return (
            `<p>Counter:${model.counter}</p>
            <form onSubmit="return actions.start({});">
                [Directive]<br>
                <input type="text" placeHolder="AutoGrow Directive" autoGrow/><br>
                [/Directive]<br>
                <br>
                <input type="submit" value="Start">
            </form>`
        ) ;

}

// Snabbdom
theme.ready = (counter, intents) => h('div', [
                'Counter: ',
                counter,
                h('br'),
                h('button', {on:{click:function() { intents['start'](); }}}, 'Start')
            ]);

// Vanilla.js - ES6
theme.ready = (counter, intents) => `
            <p>Counter: ${counter} </p>
            <form onSubmit="return ${intents['start']}({});">
                <input type="submit" value="Start">
            </form>`
```

SAM을 사용하면 프론트엔드 개발자는 API에서 벗어나 모델에서 직접 뷰를 작성하는데 집중할 수 있습니다. 즉 모델 속성 값의 조합을 통해 뷰가 어떻게 보여져야 하는지에 관한 질문에 답해야 합니다. 값을 어떻게 얻었는지는 관련성이 없습니다. 이것이 React가 프론트엔드 아키텍처에서 도입한 패러다임의 변화입니다. SAM은 항상 상호작용(요청/응답)의 필요성을 없애지는 않지만 반작용과 상호작용의 균형을 유지하려는 경향이 있습니다. 이 관계는 React와 SAM에 진입하는데 가장 큰 걸림돌입니다.

"템플릿을 사용하지 않는 이유는 무엇입니까?" 라는 질문이 상당히 많이 있습니다. 이유는 간단합니다. 템플릿은 해석기이고 함수는 작은 코드 생성기입니다. 해석기는 표현할 수 있는 기능이 제한적입니다. (다른 템플릿 해석기로 전혀 번역하지 않는 특정 구문을 자주 배울 필요가 없습니다.) 반면에 코드 생성기는 거의 단점이 없어 매우 유연합니다. 개인적으로는 템플릿 이상의 기능을 언제든지 선택할 것 입니다.

SAM을 사용하더라도 원하는 프레임워크를 선택할 수 있으나 Vanilla 구현을 이용하는 때에는 크로스 사이트 스크립팅의 위험을 조심해야합니다.

### 비지니스 로직

SAM은 뷰에 대한 컴포넌트 기반 구조를 지원할 뿐만 아니라 SAM을 사용하여 뷰 컴포넌트에서 애플리케이션 논리를 완전히 분리할 수 있습니다. 믿기 힘들다면 아래의 TODO 샘플과 "theme.js" 파일의 뷰 컴포넌트를 확인하세요

> 좋은 프론트엔드 아키텍처를 사용하면 모듈화 된 기능을 가능한 한 가장 분리된 방식으로 UI 컴포넌트에 고정할 수 있습니다. 이런 식으로 컴포넌트를 뒷받침하는 기술을 스왑 아웃할 수 있으며 모든 비즈니스 로직이 인질로 잡혀있지 않도록 만들 수 있습니다. - Thomas J. Buhr

SAM의 비즈니스 로직은 [TLA+](http://research.microsoft.com/en-us/um/people/lamport/tla/tla.html)에 기반합니다

> TLA+는 사물을 공식적으로 묘사하는 가장 좋은 방법은 단순한 수학을 사용한 다는 것이며, 간단한 언어를 정확하게 작성하는데 필요한 것 이상을 스펙에 포함시켜야한다는 아이디어를 기반으로 합니다. TLA+는 동시 및 분산 시스템의 고급 스펙을 작성하는데 특히 적합합니다. - Dr. Leslie Lamport

TLA+를 익히는데 관심이 있다면 [Stephan Merz의 강의](http://www.loria.fr/~merz/talks/argentina2005/slides.pdf)를 확인하세요. TLA+에 관한 가장 쉬운 소개 입니다.

TAL+는 다음과 같이 시스템에 관한 설명, 분석 및 추론에 사용할 수 있는 공식 스펙입니다.

- 순차 알고리즘
- 대화형 시스템
- 반응 및 분산 시스템
- 실시간 및 하이브리드 시스템
- 보안이 중시되는 시스템

TLA+는 수식으로 표현되는 트랜지션 시스템 및 속성이 있는 통일된 언어를 제공합니다. 여기서 수학은 반응 및 분산 시스템의 설명 및 분석의 기초를 형성합니다.
TLA는 두가지 수준의 구문을 정의합니다. 액션 수식과 임시 수식 입니다.

- 액션 수식은 상태 및 상태 전환을 설명합니다.
- 임시 수식은 상태 시퀀스를 기술합니다.

`경고` SAM이 컴퓨터 과학의 전통적 의미에서 "스테이트"라는 단어를 사용하지 않는다는 점을 강조하는 것이 중요하다는 독자들의 말을 들었습니다. SAM의 스테이트-액션 요소는 시스템의 동작을 지정합니다.

> 스테이트-액션 동작은 시퀀스입니다.
> ```
>     α1    α2
> s1 −→ s2 −→ s3 −→  ...
> ```

단계 s<sub>i</sub>, α<sub>i</sub>, s<sub>i+1</sub>는 액션 α<sub>i</sub>에 의해 수행되는 상태 s<sub>i</sub>로부터 상태 s<sub>i+1</sub>로의 전이를 나타냅니다.

스테이트-액션에 관한 토론은 Dr. Lamport의 [논문](http://research.microsoft.com/en-us/um/people/lamport/pubs/state-machine.pdf)의 2절을 참조하세요

State-Action-Model용어가 마음에 들지 않는다면 [Paxos 프로토콜](https://en.wikipedia.org/wiki/Paxos_(computer_science))을 사용할 수도 있습니다. (PAL, Proposer, Acceptor and Learner)

```
Client   Proposer      Acceptor     Learner
|         |          |  |  |       |  |
X-------->|          |  |  |       |  |  Request
|         X--------->|->|->|       |  |  Prepare(1)
|         |<---------X--X--X       |  |  Promise(1,{Va,Vb,Vc})
|         X--------->|->|->|       |  |  Accept!(1,Vn)
|         |<---------X--X--X------>|->|  Accepted(1,Vn)
|<---------------------------------X--X  Response
|         |          |  |  |       |  |
View     Action       Model         State  (SAM)
```

본질적으로 Paxos 프로토콜의 역할은 SAM 컴포넌트의 역할과 정확히 일치합니다. 액션은 스테이트를 받아들이는 모델에 값을 제안합니다. (스테이트에 따라 표시되는 스테이트 표현을 만들기위해 학습자가 접근합니다)

개인적으로 SAM의 핵심인 "스테이트-액션" 행동의 개념을 묘사하고 자연스럽게 "스테이트 표현(뷰)"를 자연스럽게 만들기때문에 좋아합니다.

따라서 모든 의도와 목적을 위해 SAM의 모델은 애플리케이션 상태의 모든 가능한 변수에 값을 할당하는 반면 SAM의 스테이트는 "제어 상태" (예: "시작됨", "정지함"은 자동차의 두가지 제어 상태입니다.)를 계산하는 함수를 말합니다. 일반적으로 시스템의 "스테이트"는 특정 시점에서 허용하는 작업을 제어합니다.

### 액션

액션은 데이터셋(이벤트)를 받아들이고 데이터셋(제안)를 반환하는 순수 함수입니다. 함수의 목적은 모델이 받아들일 수 있는 값을 계산하는 것 입니다. 이 밖에 다른 것은 없습니다. 예를 들어 Redux에서 "액션"은 데이터 구조로 작업보다 의도 또는 이벤트에 훨씬 가깝습니다. Redux에서는 리듀서가 모델 변이와 의도를 모델 속성으로 변환하는 로직 사이에 원치 않는 결합을 생성합니다. 이는 Redux와 SAM의 근본적인 차이점 입니다.

```javascript
function action(data,present) {
  // 모델이 변이되기를 바라는 값을 계산
  var data_ = the_actual_pure_function_implementing_the_action(data) ;

  // 모델에 값을 제안
  present(data_) ;

  // 반응 루프에 있기 때문에 아무것도 반환하지 않음
  }
```

액션은 컨텍스트에 국한되는 로직을 구현할 책임이 있습니다. "주소 변경" 작업의 예에서 입력 데이터 집합에 지정된 국가가 없는 경우와 같이 컨텍스트 특정 규칙을 구현할 수 있습니다. 국가 값이 필요한 고객주소 모델은 무결성을 담당합니다 기본 국가는 호주입니다. SAM 컨텍스트에서 작업은 다른 API 호출과 관련하여 또 다른 중요한 역할을 합니다. 예를 들어 주소가 주어지면 우편 주소(또는 오류)를 반환하는 제 3자 유효성 검사 서비스를 호출하는 작업을 정의할 수 있습니다. 이는 모델에 표시되는 우편 주소 입니다.

```javascript
function changeOfAddress(address,present) {
  address = address || {} ;
  address.country = address.country || 'Australia' ;
  getPostalAddress( address, function(addr) {
      // assuming the dataset returned by the 3rd party
      // service can be directly presented to the model
      present(addr) ;
  }) ;
}
```

액션 로직은 일반적으로 모델 간 재사용할 수 있으며 SasS 모델을 따르는 SAM 액션을 제공하는 회사가 있다고 생각할 수도 있습니다. 사용자 데이터가 주어진 경우 우편 주소를 반환하는 "주소 변경" 작업은 엔터프라이즈 수준으로 재사용 가능합니다.


### 모델

모델은 모든 애플리케이션의 상태를 가지고 있으며 논리적으로 단일 메소드인 present(data)를 노출합니다. 모델은 액션의 효과를 수락 (또는 거부)할 책임이 있습니다. 모델은 무결성 규칙이 적용되는 곳 입니다. 즉, 액션은 일반적으로 컨텍스트 특정 데이터로 트리거되며 애플리케이션 상태에 액세스할 수 없습니다. 예를 들어 "비밀번호 변경" 액션을 구현할 때 비밀번호의 유효성을 검사합니다. 요청을 보냈지만 모델에서는 마지막 3개의 암호 중 하나와 일치하는 암호를 사용할 수 없도록 추가적인 무결성 규칙을 적용할 수 있습니다.

모델은 애플리케이션 상태의 지속성에 대해서도 단독으로 책임이 있습니다. 이와 같이 모델의 아키텍처는 수용하는 데이터와 관련하여 [이벤트/커맨드 소싱 패턴](https://github.com/eventstore/eventstore/wiki/Event-Sourcing-Basics#event-sourcing)을 따를 수 있습니다.

```javascript
model.present = function(data, render) {
  if (data.address !== undefined) {
      model.shippingAddress = data.address ;
      model.billingAddress = model.billingAddress || data.address ;
  }

  // 스테이트 표현 렌더링을 트리거
  render(model) ;

  // 반응 루프에 있기 때문에 현재 메소드는 아무것도 리턴하지 않습니다
}
```

### 스테이트

이 개념은 SAM의 고유한 특성입니다. 모델의 목적은 모델을 뷰(즉 스테이트 표현)에서 분리하는 것 입니다. 스테이트에는 두가지 역할이 있습니다.

- Decide how to translate the model property values into a State Representation
- 모델 속성 값을 스테이트 표현으로 변환하는 방법 결정
- 다음 액션 속성 처리

직관적이지 않게, 스테이트는 어떠한 "상태"도 가지지 않으며 모델 속성 값으로부터 뷰와 다음 액션 속성을 계산하는 순수한 함수입니다. 스테이트 계산을 돕기위해 시스템의 현재 "제어 상태"를 계산할 수는 있지만 **SAM은 스테이트 구현이 스테이트 시스템의 의미를 기반하는 것을 요구하지 않습니다.** Rocket Launcher 예제에서 각 제어 상태가 다음과 같은 순수 함수로 계산되는 스테이트 시스템의 전체 의미를 구현했습니다.

```javascript
// 다음 모델 구조를 가정합니다
var model = {
  counter: COUNTER_MAX,
  started: false,
  launched: false,
  aborted: false
} ;


// 시스템의 현재 제어 상태를 도출합니다
state.ready = function(model) {
  return ((model.counter === COUNTER_MAX) && !model.started && !model.launched && !model.aborted) ;
}

state.counting = function(model) {
  var status = ((model.counter <= COUNTER_MAX) && (model.counter >= 0) && model.started && !model.launched && !model.aborted) ;
  return status ;
}

state.render = function(model) {
  state.representation(model)
  state.nextAction(model) ;
}

state.representation = function(model, display) {
    var representation = 'oops... something went wrong, the system is in an invalid state' ;

    if (state.ready(model)) {
      representation = state.view.ready(model) ;
    }

    if (state.counting(model)) {
      representation = state.view.counting(model) ;
    }

    if (state.launched(model)) {
      representation = state.view.launched(model) ;
    }

    if (state.aborted(model)) {
      representation = state.view.aborted(model) ;
    }

    // GUI에 해당 표현을 마운트합니다
    display(representation) ;

    // 반응 루프에 있기 때문에, 아무것도 리턴하지 않습니다
}
```

애플리케이션의 제어 상태 수와 모델 크기에 따라 이러한 함수는 다소 지루할 수 있습니다. 다시 말하면, 그러한 접근법을 채택 할 필요는 없으며 어떤 경우에는 도움이 될 수 있고 모델의 핵심 속성에 대한 일련의 if-then-else가 충분히 좋은 다른 곳에서 부담이 될 수 있습니다.

스테이트 표현이 렌더링 되면 스테이트는 모델의 순수한 함수인 다음 액션 술어(nap)을 호출합니다. nap() 함수의 목적은 모델의 현재 제어 상태에 따라 호출해야하는 자동 작업이 있는지 확인하는 것 입니다. 예를 들어 [Rocket Launcher](https://bitbucket.org/snippets/jdubray/9dgKp/sam-sample) 예제에서 시스템은 다음 액션 조건자에 의해 "decrement()" 액션이 호출됩니다. 카운팅 스테이트의 카운터가 0이 되면 "launch()" 액션을 호출합니다.

```javascript
state.nextAction = function (model) {
  if (state.counting(model)) {
    if (model.counter>0) {
        actions.decrement({counter: model.counter},model.present) ;
    }

    if (model.counter === 0) {
        actions.launch({},model.present) ;
    }
  }
}
```

앱에 전체 스테이트 기계가 필요하다고 생각하면 [STAR library](https://bitbucket.org/jdubray/star-javascript)와 같은 라이브러리를 사용할 수 있습니다.

### 스테이트 표현

스테이트 표현 함수는 기술에 의존하지 않습니다. SAM은 이러한 함수가 모델의 순수 함수(즉, 모델의 관점에서 볼 때 스테이트리스)만 요구합니다. 다음은 [Inferno 사용](https://github.com/trueadm/inferno)의 예 입니다.

```javascript
// 카운팅 스테이트의 스테이트 표현
  ...
  theme.counter = function(counter) {
    return {
      tag: 'div',
      children: [
        {
          tag: 'p',
          children: `counter: ${counter}`
        }
      ]
    }
  }
  InfernoDOM.render(theme.counter(10), document.body);
}
```

스테이트리스 React.js 컴포넌트는 잘 작동합니다. Objective-C의 viewDidLoad 메소드와 같은 유형의 함수입니다.

Alex Schepanovski는 [몇줄의 jQuery로 React.js의 함수형 구현](http://hackflow.com/blog/2015/03/08/boiling-react-down-to-few-lines-in-jquery/)을 설명합니다.

프레임워크가 해결해야할 주요 문제는 HTML 이벤트와 이벤트 핸들러 사이의 연결 입니다. 위 코드에서 볼 수 있듯이 "abort" 액션은 카운팅 상태 표현에 하드코딩 되어 있습니다. 이것은 좋은 결합이 아니므로 일반적으로 피해야 합니다. SAM은 상태 기능을 통해 애플리케이션의 뷰 컴포넌트를 분리하는 우아한 솔루션을 제공합니다.

컴포넌트의 핸들러에 애플리케이션의 액션을 매핑하는 "intents" 목록을 받아들이도록 뷰 컴포넌트를 설계할 수 있습니다. [TODOSAM 예제](https://github.com/jdubray/sam-samples/blob/master/todomvc-app/js/app.js)에서 그 접근방식을 구현했습니다. 모든 테마 컴포넌트는 인텐트 맵을 허용합니다.

```javascript
theme.list = (todos, displayActive, displayCompleted, intents) => {
  ...
  const label = `<label ondblclick="return actions.${intents['edit']}({'id':'${todo.id}'});">${todo.name}</label>` ;
  ...
}

// 뷰 인텐트와 액션 매핑
actions.intents = {
  edit: 'edit',
  save: 'save',
  done: 'done',
  displayAll: 'displayAll',
  displayActive: 'displayActive',
  displayCompleted: 'displayCompleted',
  toggleAll: 'toggleAll',
  delete: 'delete'
}
```

컨셉은 액션에 의해 직접 소비가능한 이벤트 타입을 매핑하도록 확장할 수 있지만 이 결합은 뷰와 액션의 결합보다 덜 중요합니다.

### 뷰모델

뷰가 모델의 순수 함수(템플릿 또는 쿼리와 반대)로 표현되면 모델은 뷰의 인터페이스에 인위적으로 순응하지 않아도 됩니다. SAM을 사용하면 View는 일반적으로 컴포넌트로 분해됩니다. 각 컴포넌트는 컴포넌트 구체적이고/중심적인 인터페이스를 표시하는 순수함수로 표현합니다.

예를 들어 뷰에 값 v와 이 값이 좋거나 나쁜 상태를 표시하는 그래픽 표시기를 표시하는데 모델에 표시기 값을 표시할 이유가 없습니다. 컴포넌트의 함수는 모델이 제공한 값 v로부터 표시기 값을 계산 해야합니다.

이러한 계산을 뷰에 직접 포함시키는 것은 좋은 생각이 아니지만 다음과 같은 뷰 모델 함수를 사용하는 것은 어려운 일이 아닙니다.

```javascript
V = f( vm(M) )
```

### 배선(Wiring)

SAM 패턴은 수학 표현식으로 설명할 수 있습니다.

```javascript
V = S( vm(M.present(A(data))), nap(Model) )
```

그러나 이 표현식은 패턴 엘리먼트가 호출되는 순서를 설명하는 논리적 ㅍ현식일 뿐입니다. 표현 자체는 반응 흐름 또는 다른 배선 옵션을 나타내지 않습니다.

SAM은 브라우저 ([React와 Redux와 유사](http://andrewhfarmer.com/react-ajax-best-practices))에서 구현할 수 있지만 SAM의 장점 중 하나는 사실상 모든 토폴로지에서 패턴의 엘리먼트를 배포하고 구성할 수 있다는 것 입니다. 따라서 패턴을 연결하는 방법은 하나가 아닙니다.

또한 SAM은 반응적인 패턴이므로 예상되는 응답이 결코 없다는 것을 명심해야합니다. 뷰는 모델에 데이터 세트인 액션을 트리거 합니다. 이 액션은 스테이트에 스테이트 표현(예: 뷰)를 만들도록 요청합니다. 새로운 스테이트 표현은 모델의 현재 상태에 대한 새로운 뷰입니다. 원래의 견해에 대한 응답이 **아닙니다**.

SAM을 독점적으로 단일 프로세스(예: 브라우저가 모델이 영구 저장소와 통신한다고 가정)로 실행되는 샘플을 보겠습니다.

모델은 싱글톤, 데이터세트의 순수한 함수로, 스테이트(stateRepresentation() 및 nap())를 모델의 순수 함수로 정의할 수 있습니다. 다음은 함수형 래퍼로 달성한 배선의 예입니다.

```javascript
// 모델은 싱글톤입니다  /////////////////////////////////////////////
var model = {} ;

model.present = function(data) {
  // 제안된 값을 허용하거나 거부하는 로직
  // ...

  // -> 반응 루프
  state(model) ;

  // 새 스테이트 유지
  // 일반적으로 보호/최적화 됨
  model.persist() ;
} ;

model.persist = function() {

} ;

// 액션은 순수 함수입니다. /////////////////////////////////////////////
function action1(data) {
  // 모델에 표시할 데이터를 준비하는 로직
  // ...

  // -> 반응 루프
  present(data) ;

  // 페이지 새로고침 방지
  return false ;
}

function action2(data) {
  // 모델에 표시할 데이터를 준비하는 로직
  // ...

  // -> 반응 루프
  present(data) ;

  // 페이지 새로고침 방지
  return false ;
}

// 스테이트는 순수 함수입니다. /////////////////////////////////////////////
function state(model) {
  // 스테이트는 강하게 연결되어 있습니다
  stateRepresentation(model) ;
  nap(model) ;
}

function nap(model) {
  if (condition(model)) {
    var data = f(model) ;
    action2(data,model.present) ;
  }
}


// 뷰는 순수 함수입니다.  /////////////////////////////////////////////
function someView(model) {
  // 뷰를 렌더합니다
  var output = '' ;
  // ...

  // wire the possible actions in the view
  output = output + 'onSubmit="JavaScript:return action1({data:data});'

  // ...
  // -> 반응 루프
  display(output) ;
}

// 배선 /////////////////////////////////////////////
//
// 액션은 stateRepresentation()과  nap()에 알려져 있습니다
//
// Actions -> Model
function present(data) {
  model.present(data) ;
}

// State -> View
function stateRepresentation(model) {
  someView(model) ;
}


// View -> Display
function display(view) {
  var stateRepresentation = document.getElementById("view");
  stateRepresentation.innerHTML = view;
}
```

물론 jQuery 이벤트 핸들러를 사용할 수도 있습니다.

```javascript
...
<input id="username" type="text" class="login-username" value="username">
<input id="password" type="password" class="login-password" value="password">
<button class="button button-green center-button" id="login">Login</button>
...

<script type="text/javascript">
$('#login').click(function() {
  var session = $.post( "http://authserver/v1/login", { username: $( "#username" ).val(), password:$( "#password" ).val() } ) ;
  session.done(function( data ) {
    var loginPanel = document.getElementById("login_panel") ;
    loginPanel.innerHTML = state.render(model.present(data)) ;
  })
})
</script>
...
// 또 다른 옵션은 서버에 스테이트, 모델 및 액션을 구현하는 것 입니다.
<script type="text/javascript">
$('#login').click(function() {
  var session = $.post( "http://authserver/v1/login", { username: $( "#username" ).val(), password:$( "#password" ).val() } ) ;
  session.done(function( stateRepresentation ) {
      var loginPanel = document.getElementById("login_panel") ;
      loginPanel.innerHTML = stateRepresentation ;
  })
})
</script>
```

[RxJS](https://github.com/Reactive-Extensions/RxJS/blob/master/doc/gettingstarted/events.md)를 사용하여 이벤트를 액션에 연결할수도 있습니다.

```javascript
var result = document.getElementById('submit');

var source = Rx.Observable.fromEvent(document, 'click');

var subscription = source.subscribe(function (e) {
  var data = { name: document.getElementById('name').value, ... }
  result.innerHTML = state.render(model.present(data));
});
```

배선은 패턴을 구현할 때 중요합니다. 예를 들어 [mobx framework](https://medium.com/@mweststrate/pure-rendering-in-the-light-of-time-and-state-4b537d8d40b1#.q91pjr4c7)는 개별 속성 값 변경을 뷰 렌더링 기능으로, 시스템의 액션 및 작업 단위로서의 처리를 나타내지 않습니다. 아래 예제 탭에서 볼 수 있듯이 뷰는 스테이트 데이터 변경내용을 렌더링합니다.

```javascript
// 앱의 스테이트
var state = mobx.observable({
  nrOfSeats : 500,
  reservations : [],
  seatsLeft : function() { return this.nrOfSeats - this.reservations.length; }
});

// UI: 스테이트에 적용되는 함수
var ui = mobx.computed(function() {
  return "\nSeats left: " + state.seatsLeft +
      "Attendees: " + state.reservations.join(", ") + "";
});

// 변경될 때마다 UI가 '렌더링'되는지 확인하세요
ui.observe(function(newView) { console.log(newView) }, true);

// Put in some test data
state.reservations[0] = "Michel";
state.reservations.push("You?");
state.reservations[0] = "@mweststrate";
state.nrOfSeats = 750;
```

### 시간 여행(Time Travel)

Gunar Gessner는 모든 액션이 처리된 후 모델의 스냅샷을 찍은 다음 주어진 시스템의 상태를 복원할 수 있는 [클라이언트 측 "시간여행" 개발도구](https://github.com/sam-js/sam-devtools)를 구현했습니다.

SAFE 컨테이너는 [client측과 server측 "시간여행" 개발도구](https://github.com/jdubray/sam-safe/blob/master/README.md#time-travel)를 모두 구현합니다.

<div style="position:relative;height:0;padding-bottom:75.0%"><iframe src="https://www.youtube.com/embed/o7ZIG4jT0xM?ecver=2" width="480" height="360" frameborder="0" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>


## 고급 토픽

### 마이크로 컨테이너

[SAFE 프로젝트](https://github.com/jdubray/sam-safe)(State-Action-Fabric-Element)는 브라우저 또는 node.js에서 실행할 수 있는 SAM 구현을 위한 마이크로 컨테이너입니다. 최소한 SAFE를 사용하면 패턴 엘리먼트를 연결할 수 있습니다. 또한 세션관리, 로깅 및 오류처리를 함께 제공됩니다. 현재 버전에서는 전역 유효성 검사 및 "강제 중지" 작업을 포함한 액션 수행이 가능합니다. 마지막으로 시간여행 도구를 구현하였습니다.

### 구성

SAM 패턴은 관대한 구성 메커니즘을 제공합니다.

첫번째 액션은 순수 함수로 모델에 단일 데이터 셋을 자연스럽게 표시합니다.

```javascript
C(data) = A(B(data))
```

이러한 유형의 구성은 함수형 구성이며 결과 액션은 SAM의 관점에서 시스템에 적용되는 단일 액션으로 간주됩니다.

유사하게, 스테이트 표현(뷰)는 컴포넌트 계층 구조로 분해할 수 있습니다.

```javascript
V = f( M )
f( M ) = f1( g1(M) + g2(M) ) + f2( h1(M) + h2(M) )
```

가장 흥미로운 구성 메커니즘은 패턴 레벨 그 자체에 있습니다. 예를 들어 SAM은 하나의 인스턴스가 브라우저에서 실행되고 하나의 인스턴스가 서버에서 실행되는 인스턴스 레벨 구성을 지원합니다.

![](http://sam.js.org/assets/figures/fig9.jpg)

```javascript
// 구성은 하나의 뷰를 가집니다
V = Ss( Ms ) + Sc( Mc )

// 인스턴스 c(브라우저)는 인스턴스 s(서버)에서 액션을 호출합니다.
// 이 액션은 일반적으로 클라이언트의 nap() 함수에서 호출합니다.
V = Ss( Ms.present( As(Mc) )
```

이론적으로 가능하지만 서버에서 클라이언트 작업을 호출하는 것은 좋지 않습니다. 서버의 역할은 스테이트 표현의 일부로 SAM 클라이언트 인스턴스를 전달하는 것 입니다.

리액트 15.0.2와 JSX/바벨을 이용한 [부모/자식](https://github.com/jdubray/sam-samples/tree/master/react-child-instance) 예제 입니다. 자식 인스턴스와의 복잡한 형태를 구현하고 폼의 내용이 유효하면 부모에게 결과 데이터셋을 제출하는 방법을 보여줍니다.

참고: 단지 초기단계의 생각이며 더 많은 것들을 필요로 합니다.

또한 SAM은 클라이언트/서버측 모델을 동기화할 수 있는 흥미로운 대체 구성 메커니즘을 제공하여 클라이언트 측 모델과 서버측 모델 모두에 동일한 데이터셋을 제공합니다.

![](http://sam.js.org/assets/figures/fig11.jpg)

### 스테이트 머신

> 계산은 컴퓨터 과학의 주요 주제이며, 계산되는 거의 모든 객체는 자연스럽게 스테이트 머신으로 간주됩니다. 그러나 컴퓨터 과학자들은 계산을 설명하기 위해 사용된 언어에 중점을 두고 있기 때문에 이들 언어 모두 스테이트 머신을 묘사한다는 사실을 거의 모릅니다.  - [Dr. Leslie Lamport](http://research.microsoft.com/en-us/um/people/lamport/pubs/state-machine.pdf)

SAM은 단순하지만 근본적인 차이점을 제외하고는 스테이트 머신의 전통적인 의미와 잘 일치합니다. 전통적인 스테이트 머신 의미론은 액션이 어떻게든 두가지 스테이트를 연결한다는 것을 의미하므로 스테이트 머신은 다음과 같은 일련의 튜플로 설명됩니다.

```
initialState = S0
(S0,A01,S1), (S1,A12,S2)...
```

이 정의는 다소 근사치를 보입니다. 결과 상태를 결정하는 액션이아닌 모델이므로 결정과 상태를 결정하는 액션(주어진 상태에서 상태 Si에서 허용된 액션)이 적용(값을 받거나 혹은 아니거나)됩니다.

```
initialState = S0 = S( M0 )
(S0, A00, A01), (S1, A10, A11, A12)...
S0 = S( M0 )
S1 = S( M1 )
```

튜플(S<sub>i</sub>,A<sub>ik</sub>,S<sub>k</sub>)은 런타임의 물리적 표현보다 스테이트 머신의 액션을 관찰한것 일 뿐입니다.
이러한 관점의 변화(의미론적이 아닌)는 근본적인 부분입니다. 스테이트 머신의 전통적인 의미가 코드를 구조화하는 경우 SAM은 작동하지 않습니다. SAM의 의미론은 이러한 전통적인 구조를 포괄하므로 엄격하게 호환되나 SAM은 결코 스테이트 및 액션 그래프를 사용하지 않아도 됩니다.
그래서 일부 사람들에게 SAM이 "자연스럽게" 느낄 것이라 생각합니다.

[Rocket Launcher](https://bitbucket.org/snippets/jdubray/9dgKp/sam-sample) 예제에서는 Sx() 함수를 구현하는 방법을 보여줍니다.

```javascript
// 시스템의 현재 상태를 파생시킵니다.
state.ready = function(model) {
  return ((model.counter === COUNTER_MAX) && !model.started && !model.launched && !model.aborted) ;
}

state.counting = function(model) {
  var status = ((model.counter <= COUNTER_MAX) && (model.counter >= 0) && model.started && !model.launched && !model.aborted) ;
  return status ;
}

state.launched = function(model) {
  return ((model.counter == 0) && model.started && model.launched && !model.aborted) ;
}

state.aborted = function(model) {
  return (
    (  model.counter <= COUNTER_MAX) && (model.counter >= 0)
    && model.started && !model.launched && model.aborted ) ;
}
```

SAM을 사용하기 위해 스테이트 머신 구조를 채택할 필요는 없으며 if-then-else가 적절하다고는 말할 수 없습니다. 그러나 떄로는 엄격한 스테이트 머신 구조를 사용하는 것이 더 쉬워질 수 있습니다. 이 기능을 사용하여 액션이 활성화 되어있는지 여부를 확인할 수 있습니다. 또한 뷰가 렌더링되는 계층 구조를 더 자연스럽게 세분화할 수 있습니다.

### APIs

SAM은 원래 MVC가 프론트엔드 구조와 백엔드 API간에 생성되는 강력한 결합을 해결하기 위해 설계되었습니다. API는 SAM에서 몇 단계로 구성할 수 있습니다.

- 액션: 모델에 부작용이 없는 API의 경우
- 모델: 모델 속성 값 (일반적으로 데이터 액세스 레이어라 함)을 유지/생성하는 CRUD API의 경우

동시성 문제는 다음에 이야기하고, 액션은 API 호출을 처리한 후 다음과 같이 값을 나타낼 수 있습니다.

```javascript
function getRssFeed(data) {
  var options = {
    url: 'http://www.ebpml.org/blog15/feed/',
    headers: {
      'accept': 'application/json'
    }
  };

  request(options, function (error, response, body) {
     if (!error && response.statusCode == 200) {
      // RSS 피드를 JSON 객체로 바꿈
      parseString(body, function (err, result) {
        // 제안할 데이터 셋 준비
        data.blog = result.rss.channel[0].item ;
        // 모델에 데이터 표시
        present(data) ;
     });
     }
   });
}
```

유사하게, 모델은 반응 루프의 제어를 스테이트 객체에 전달하기 전에 모든 지속성 연산을 수행 할 수 있습니다 (일부 제안된 값을 거부할 수 있음). 거의 이해가 안되는 뷰의 렌더링을 트리거하지 않으면서 스테이트를 건드릴 수 없기 때문에 React와 매우 다릅니다. 모델 스테이트를 다양한 컴포넌트에 전파 할 때만 의미가 있으며, 심지어 프론트엔드 반응 흐름을 사용하여 API의 접합을 왜곡합니다.

### Headless SAM

SAM can also be used without a View, whereby it listens on incoming Action requests and returns a response which can be the model of a fraction of the model. As such SAM offers a new API implementation pattern which is particularly well suited for implementating Stateful APIs.

![](http://sam.js.org/assets/figures/fig12.jpg)

A node.js implementation of Headless SAM would look like this:

```javascript
var express = require('express');
var app = express();

//// Model ////////////////////////////

var S0 = { ... } ;

var present = (
  function (initialState) {
    var model = initialState ;
    return (function( data, res) {
      // mutate the model
      ...
      state(model) ;
    }) ;
  }
)(S0);


//// Actions ////////////////////////////

actions.addAPI('submit') ;
app.post(actions.apis.submit.path, function(req,res) {
  // extract intent from request body
  var intent = new Intent('submit',req.body) ;
  // Compute model property values
  data_ = actions.impl.submit(intent) ;
  // present data to the model
  presentToModel(data_, res) ;
}) ;

//// State ////////////////////////////

function state(model) {
  // prepare and return API response
  var response = model.response() ;
  res.send(response) ;

  // execute next action predicate
  nap(model) ;
}
```

The pattern can easily be adapted to mount a user session in the model or rehydrate/dehydrate the context of the request before/after the model mutates.

### Isomorphic JavaScript

Typical SAM's implementations make it easy to move JavaScript code between the client or the server (a.k.a [Isomorphic JavaScript](http://isomorphic.net/)).


| # | Client           | Server  | Implementation  |
| - |:-------------| :-----|:-----|
| 1 | View | Actions, Model, State   | JQuery handlers call Actions Server returns HTML |
| 2 | View, Actions      | Model, State | JQuery handlers implement actions which call present()  Server returns HTML |
| 3 | View, Model, State      | Actions | JQuery handlers invoke Actions, response is presented to the Model on the client Server returns JSON |
| 4 | View, State      | Actions,Model | JQuery handlers invoke Actions, response is presented to the State on the client Server returns JSON |
| 5 | View, Actions, State      | Model | JQuery handlers implement Actions which call present(), response is presented to the State on the client Server returns JSON |

For instance, in the [Blog sample](https://github.com/jdubray/sam-samples/tree/master/crud-blog) we have implemented the Actions on the client and the Model and the State on the server. In that case, we have to implement two "APIs" on the server:

- init()
- present(data)

Option #1 should be preferred when authorization (RBAC) is a concern. Option #5 is the one that pushes as much processing on the client as possible.

```javascript
function present(data) {
  // client side
  //model.present(data) ;

  // server side
  $.post( "http://localhost:5425/app/v1/present", data)
  .done(function( representation ) {
    $( "#representation" ).html( representation );
  });
}

function init() {
  // client side
  //view.display(view.init(model)) ;

  // server side
  $.get( "http://locahost:5425/app/v1/init", function( data ) {
    $( "#representation" ).html( data );
  });
}
```

### Concurrency

We suggest that you download an HTML5 template of your choice and start building an application with it. To make this tutorial more realistic, we are going to build a simple Web Site with Blog and a Contact form.

The [SB Admin template](http://startbootstrap.com/template-overviews/sb-admin/) seems like a great starting point. The list of components are easy to identify, from the index.html file:

- ----- Structure ------
- Navigation Bar
- DropDown
- Search
- Menu
- Footer
- ----- Structure ------
- Stats Summary
- Graphs
- Tables
- ...

![](https://startbootstrap.com/img/templates/sb-admin.jpg)

Many developers dislike the code that will follow (let's call it vanilla.js). Since this is a matter of preferences, there is not much point in arguing about it. I just happen to prefer a raw JavaScript/HTML5/CSS3 style because:

- it enables the greatest number of developers to become full stack developers (with Node.js)
- it enables the greatest decoupling between the View and the Model
- Vanilla.js makes it really easy to develop Isomorphic JavaScript
- And perhaps, most practically, it allows anyone to take the work of a Web designer and turn it into a -beautiful Web app in a matter of minutes

The experienced developers will easily translate this code to meet their neesds. So let's go ahead and start creating the component interfaces without any further apologies:

```javascript
var theme = {} ;

theme.head = function(title, includes, addons ) { } ;

theme.navBar = function(dropDowns,sideBar) { } ;

theme.adminDropDown = function(urls) { } ;

theme.search = function(search) { } ;

theme.menuItem = function(label,symbol, url, submenu, level) { } ;

theme.sideBar =  function(menu,color) { } ;

theme.well = function(size,h,title,body,br) { } ;

theme.footer = function( includes ) { } ;

theme.statSummary = function(fa,label,number,link,linkLabel,color) { } ;

theme.advancedTable = function(size,id,color,heading,headers,columns,data,href,domain) { } ;

theme.ediTable = function(id,headers,data,editable, style, maxSize) { } ;
...
```

The implementation of each function is just a parameterization of the HTML template (the whole theme implementation can be found here):

```javascript
theme.statSummary = function(fa,label,number,link,linkLabel,color) {
  return (
    ```<div class="col-lg-3 col-md-6">
      <div class="panel panel-${color}">
        <div class="panel-heading">
          <div class="row">
            <div class="col-xs-3">
              <i class="fa fa-${fa} fa-5x"></i>
            </div>
            <div class="col-xs-9 text-right">
              <div class="huge">${number}</div>
              <div>${label}</div>
            </div>
          </div>
        </div>
        <a href="${link}">
        <div class="panel-footer">
          <span class="pull-left">${linkLabel}</span>>
          <span class="pull-right"><i class="fa fa-arrow-circle-right"></i></span>
          <div class="clearfix"></div>
        </div>
        </a>
      </div>
    </div>```) ;
} ;
```

## Code Samples

### Donate

We accept donations to fund the development of code samples,
100% of the proceeds is used to hire talented and passionate developers.

### Rocket Launcher

- [Vanilla JavaScript and HTML](https://bitbucket.org/snippets/jdubray/9dgKp/sam-sample) - You can run that sample [here](https://bb.githack.com/!api/2.0/snippets/jdubray/9dgKp/a1dd9b8b9f241600ad108ef741845241c31c9fce/files/rocket.html) (courtesy of [Jose Pedro Dias](https://twitter.com/jose_pedro_dias))
- [MobX + React](https://github.com/jdubray/sam-samples/tree/master/mobx-react-typescript-boilerplate-master) ([Fred Daoud](http://www.linkedin.com/in/fredericdaoud))
- [Cycle.js](http://codepen.io/foxdonut/pen/XXPwRe) ([Fred Daoud](http://www.linkedin.com/in/fredericdaoud))
- [Snabbdom](https://github.com/JosePedroDias/sam_rocket_w_snabbdom) ([Jose Pedro Dias](https://twitter.com/jose_pedro_dias)). You can run the sample [here](https://josepedrodias.github.io/sam_rocket_w_snabbdom/index.html).
- [Knockout.js](https://jsfiddle.net/80xzt848/1/) ([Troy Ingram](http://blog.troyingram.us/))
- [React+Redux(following the SAM pattern)](https://github.com/gunar/sam-redux) ([Gunar Gessner](https://twitter.com/gunar))
- [Angular (following the SAM pattern)](https://github.com/brusand/sam-angular) (Bruno Darrigues)
- [Angular+TypeScript (following the SAM pattern)](https://github.com/brusand/sam-typescript) (Bruno Darrigues)
- [Inferno](https://github.com/jdubray/sam-samples/tree/master/inferno-rocket)
- [Angular2 with Dynamic templates and ComponentFactory](https://github.com/jdubray/sam-samples/tree/master/angular2-rocket-2.0.0)
- [Vanilla.js+Redux](http://codepen.io/foxdonut/pen/BzQzBx?editors=1010) ([Fred Daoud](http://www.linkedin.com/in/fredericdaoud))

### Draw Rectangle

[Vanilla.js](https://hyperdev.com/#!/project/plain-frog)

### ToDo

- [Vanilla JavaScript and HTML](https://bitbucket.org/snippets/jdubray/zr9r6/item-list)
- [Vue.js](http://codepen.io/msolovyov/pen/qbvYpv?editors=1010)([](http://sam.js.org/index.html))
- [TODOMVC Challange (HTML+Vanilla JS, both ES5 and ES6)](https://github.com/jdubray/sam-samples/tree/master/todomvc-app)
- [Angular2 Admin Template with ToDo component ](https://github.com/jdubray/sam-samples/tree/master/angular2-admin-master-2.0.0)

### Blog

[Node.js / Isomorphic JavaScript - BootStrap](https://github.com/jdubray/sam-samples/tree/master/crud-blog)

### Contact

[Simple Web site with Contact App (Node.js+HTML)](https://bitbucket.org/snippets/jdubray/jna7B/sam-sample-app-on-nodejs)

### Themes

- [A simple Web app built on the SB Admin template (Node.js+HTML)](https://github.com/jdubray/sam-samples/tree/master/sbadmin-template)
- [A simple Web Site](https://github.com/imnutz/sam-exmachina) (with actions) built on the [Ex-Machina template](http://templated.co/exmachina) (HTML+Snabbdom) (@imnutz / Son Ngoc)
- [A Vanilla ES6 Builerplate Project](https://github.com/jdubray/sam-samples/tree/master/vanilla-es6-boilerplate-project)
- [A full stack, vanilla.js/Node.js of the Bootstrap Clean Blog project](https://github.com/jdubray/startbootstrap-clean-blog) (ES6)

### Others

- A dual-client (session based) [Tic-Tac-Toe game](https://github.com/509dave16/sam-tic-tac-toe) with a [SAM-based engine](https://github.com/509dave16/tic-tac-toe-engine) (React/Redux+Firebase) via [David Fall](https://github.com/509dave16) A simple Counter App using [Flyd](https://github.com/imnutz/sam-flyd) (@imnutz / [Son Ngoc](https://twitter.com/sonngoc))
- [How to use Mocha and Chai] to write unit tests (BDD) for SAM implementations (via Robert Blixt)
A contact manager web application built with [SAM and CSP](https://github.com/imnutz/sam-csp) ([Communicating Sequential Processes](http://jlongster.com/Taming-the-Asynchronous-Beast-with-CSP-in-JavaScript)) (@imnutz / [Son Ngoc](https://twitter.com/sonngoc))
- [A simple CRUD application](https://github.com/jdubray/sam-samples/tree/master/crud-blog-lambda) running on AWS Lambda and DynamoDB for session management
- [A simple counter](https://github.com/jdubray/sam-samples/tree/master/react-counter) using React 15.0.2 with JSX/Babel
- [A Parent/Child](https://github.com/jdubray/sam-samples/tree/master/react-child-instance) sample using React 15.0.2 with JSX/Babel
- [Another Parent/Child](https://github.com/jdubray/sam-samples/tree/master/vanilla-child-instance) sample in vanilla.js
- [A CSS Spinner](https://github.com/jdubray/sam-samples/blob/master/react-spinner/index.html) using React/JSX without "looping" through the model

This is what Jonathan Siu, Startup Consultant, had to say about SAM:

![](http://sam.js.org/assets/figures/fig0.jpg)

## Frameworks

André Statlz has written a great paper comparing the different [Unidirectional User Interface Architectures](http://staltz.com/unidirectional-user-interface-architectures.html. The pattern closest to SAM is [Famous](https://blog.famous.org/introducing-the-famous-framework/), but unfortunately, they mutate the model directly from the actions (events in Famous' terms) and they do not support a nap() function.

This [talk from Andrew Clark at the React 2016 Conference](https://www.youtube.com/watch?v=Pm0uwncSCo4&feature=youtu.be&t=14366) offers some great perspective on the different approaches (React,Cycle,Elm) starting with a simple formula...:

```javascript
V = f( M )
```

### React.js

| React + Redux + Thunks + Sagas | SAM |
| :------------------------------ |:---|
|Single State Tree               |Single Model Tree |
|State is read-only  | Model is presented with new proposed values|
|Reducers are pure functions Reducers have access to full scope of state when processing actions|Actions are pure functions with respect to the model Actions have no access to the model to compute the values presented to the model|
|Store is updated with the output of the reducer function Listeners are notified of the update|State Representation is created after the model has mutated Next-Action Predicate evaluates whether an automatic action needs to be triggered|
|View is a function of the state|State representation is a function of the model|
|Next action predicate (nap) is implemented as a Saga which couples many operations and generally relies on local state as shown in [this implementation of the Rocket Launcher](https://gist.github.com/yelouafi/0385402a453b1fdb5cb0).|The Next Action Predicate is a pure function of the model which computes the current (control) state of the system and derives the next-action.|

The main issues I see with this framework (as a set of libraries) are:

- Redux combines the logic that computes the values with which you would update the model with the logic that decides whether the model accepts these values or not.
- As a corollary it couples the interface of the reducers to the model. One of the key roles of actions is to adapt the View-Model interface in ways that makes the model insensitive to the view semantics. The Reducers' interfaces are directly shapped by the view, which is an anti-pattern.
- Anemic actions are the default in Redux, you need to add the "thunk" library to be able to use functions as actions
- Sagas are stateful by definition and their imperative style makes it difficult to reason about what is the next action that will occur.



### Meiosis

[Meiosis](https://foxdonut.gitbooks.io/meiosis-guide/content/sam.html) is a library build by Fred Daoud that is well aligned with the SAM pattern. The library's core focus is to help organize the data flow of web applications. The general idea behind Meiosis is to start with a model and write a function that creates the view. When you want a looser coupling between the view and the model, you can optionally configure an "actions object" which translates view events into model proposals. Once the proposal is accepted by the model, Meiosis calls the view function with the updated model. You can also define a function to determine if another action should be automatically triggered, given the new application state. Meiosis is designed to work with plain JavaScript functions which, it wires together, without tying your code to the library. Last, but not least, you can switch from one Web framework to another and by changing the view code. The remainder of the code (the model, receiving proposals, actions, and so on) remains the same.

## Where can I find more information?

- Jean-Jacques Dubray, InfoQ, Feb. 2016 "[Why I no longer use MVC Frameworks?](https://www.infoq.com/articles/no-more-mvc-frameworks)"
- Gunar Gessner, Jean-Jacques Dubray, InfoQ, Oct. 2016 "[Lessons learned in implementing the SAM pattern](https://www.infoq.com/articles/sam-lessons-learned-front-end-reactive-architectures)"
- Alexander Schepanovski, March 2015 "[Boiling React Down to a Few Lines in jQuery](http://hackflow.com/blog/2015/03/08/boiling-react-down-to-few-lines-in-jquery/)"
- Simon Friis Vindum, "[Functional Front-End Architectures](https://github.com/paldepind/functional-frontend-architecture)"
- Tim Branyen, "[DiffHTML](https://github.com/tbranyen/diffhtml)"
- Steven Lambert, "[A proposal to improve the DOM creation API: HTML Tagged Templates](https://github.com/straker/html-tagged-template)"
- Mike Samuel, "[Using type inference to make web templates robust against XSS](https://js-quasis-libraries-and-repl.googlecode.com/svn/trunk/safetemplate.html#security_under_maintenance)"
- owasp.org "[XSS (Cross Site Scripting) Prevention Cheat Sheet](https://www.owasp.org/index.php/XSS_%28Cross_Site_Scripting%29_Prevention_Cheat_Sheet)"
- Google [Application Security](https://www.google.com/about/appsecurity/learning/xss/)
- Greg Young, "[CQRS and Event Sourcing](https://www.youtube.com/watch?v=JHGkaShoyNs)"
- SmartDraw "[Why we ditched AngularJS](https://blog.smartdraw.com/why-we-ditched-angular-js/)"

## Who is using SAM?

- [ebpml.org](http://www.ebpml.org/about):  The about page is built from an off-the-shelf template, the model populates itself with the ebpml.org RSS feed, the contact form invokes an action.
- [Gliiph](http://www.gliiph.com/):  Gliiph is a new kind of Web platform that brings together content, data and APIs to deliver the next generation Web apps
- [MichelDuran.com](http://www.michelduran.com/): Michel Duran is a French-Canadian Actor, Director and Producer who recently engaged xgen.io to rebuild its Web site.
