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


### Model

The model contains all the application state and (logically) exposes a single method: present(data). The model is responsible for accepting (or rejecting) the effects of an action. The model is where integrity rules get enforced. In other words, actions are generally triggered with some context specific data, they do not have access to the application state, for instance, when you implement a "change password" action, the action will check if the password is valid in the context of the request, but the model might enforce additional integrity rules, such that you cannot use a password that matches any of your last three passwords.

The model is also solely responsible for the persistence of the application state. As such, the Model's architecture may follow the [Event/Command Sourcing Pattern](https://github.com/eventstore/eventstore/wiki/Event-Sourcing-Basics#event-sourcing) with respect to the data the it accepts

```javascript
model.present = function(data, render) {
  if (data.address !== undefined) {
      model.shippingAddress = data.address ;
      model.billingAddress = model.billingAddress || data.address ;
  }

  // trigger the state representation rendering
  render(model) ;

  // since we are in a reactive loop, the present method returns nothing
}
```

### State

This concept is unique to SAM. Its purpose is to futher decouple the Model from the View (i.e. the State Representation). The State has two roles:

- Decide how to translate the model property values into a State Representation
- Process the next-action predicate

Unintuitively, the State does not hold any “state”, it is a pure function which computes the view and the next-action predicate, both from the model property values. The State may compute the current "control state" of the system to assist the computation, but **SAM does not require the State implementation to be based the semantics of a state machine.** In the Rocket Launcher example, we've have implemented the full semantics of a state machine where each control state is computed with pure functions such as:

```javascript
// Assuming the following model structure
var model = {
  counter: COUNTER_MAX,
  started: false,
  launched: false,
  aborted: false
} ;


// Derive the current control states of the system
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

    // mount the corresponding representation in the GUI
    display(representation) ;

    // since we are in a reactive loop, the present method returns nothing
}
```

Depending on the number of control states of your application and the size of your model, these functions can become rather tedious to write. Again, there is no requirement to adopt such an approach, it may help in some cases and be a burden in another where a series of if-then-else on some key properties of the model is good enough.

Once the state representation is rendered, the State is responsible for invoking the next-action predicate (nap), which is a pure function of the model. The purpose of the nap() function is to determine if, given the current control state of the model, there are some automatic actions that need to be invoked. For instance in the [Rocket Launcher](https://bitbucket.org/snippets/jdubray/9dgKp/sam-sample) example, the "decrement()" action is invoked by the next-action predicate while the system is in the counting state. When the counter reaches zero, it invokes the "launch()" action.

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

When you feel that a full state machine is required for your app, you may use a library such as the [STAR library](https://bitbucket.org/jdubray/star-javascript).



### State Representation

The State Representation function(s) are technology agnostic. SAM only requires that these functions be pure functions of the model (i.e. stateless from the model's perspective). Here is an example [using Inferno](https://github.com/trueadm/inferno):

```javascript
// State representation of the counting state
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

Stateless React.js components would work great. The same type of function could we writen in Objective-C for the viewDidLoad method.

Alex Schepanovski explains how to achieve [React.js functionality with just a few lines of JQuery](http://hackflow.com/blog/2015/03/08/boiling-react-down-to-few-lines-in-jquery/).

On of the key problems that frameworks try to tackle is the wiring between the HTML events and event handlers. As you can see in the code above, the "abort" action has been hard coded in the counting state representation. This is not a great coupling and in general it should be avoided. SAM provides an elegant solution to decouple the view components from your application via the State function.

You can design your view components to accept a list of "intents" which map the application's action to the component's handlers. We have implemented that approch in [the TODOSAM sample](https://github.com/jdubray/sam-samples/blob/master/todomvc-app/js/app.js). All the theme's components accept an intent map:

```javascript
theme.list = (todos, displayActive, displayCompleted, intents) => {
  ...
  const label = `<label ondblclick="return actions.${intents['edit']}({'id':'${todo.id}'});">${todo.name}</label>` ;
  ...
}

// mapping view intents -> actions
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

The concept could also be extended to map the event format to be directly consumable by the action, but this coupling is less important than the coupling of the actions with the view.

### View Model

When the View is expressed as a pure function of the Model (as opposed to a template or a query), the Model does not have to conform artificially to the interface of the view. With SAM, the View is generally decomposed into components, each of them expressed as pure functions exposing a component specific/centric interface.

For instance, when a view displays a value v and a graphical indicator as to whether this value is great, good or bad, there is no reason to have the indicator’s value in your model: the component's function should simply compute the value of the indicator from the value v provided by the model.

Now, it’s not a great idea to directly embed these computations in the the view, but it is not difficult to use a view-model function, such as:

```javascript
V = f( vm(M) )
```

### Wiring

The SAM pattern can be described as a Mathematical expression (formula):

```javascript
V = S( vm(M.present(A(data))), nap(Model) )
```

However, that expression is only a logical expression that describes the sequence in which the elements of the pattern are invoked. The expression itself is not representative of the Reactive Flow or the different wiring options.

SAM can be implemented in the browser ([like React and Redux](http://andrewhfarmer.com/react-ajax-best-practices)), but one of SAM's advantages is that the elements of the pattern can be distributed and composed in virtually any topology. So there is not a single way to wire the pattern.

You should also keep in mind that SAM is a reactive pattern so there is never a response that is expected: The view triggers an action, which presents is dataset to the model, which asks the state to create a state representation (i.e. View). The new state representation is just that, a new view of the current state of the model. It is **not** a response to the original view.

Let's start with a sample where running SAM exclusively in a single process (e.g. the browser, perhaps assuming the model will communicate with a persistent store).

The model can be defined as a singleton, the actions as pure functions of a dataset, the state (stateRepresentation() and nap()) as a pure function of the model. Here is an example of wiring achieved with functional wrappers.

```javascript
// Model is a singleton /////////////////////////////////////////////
var model = {} ;

model.present = function(data) {
  // Logic that accepts or rejects the proposed values
  // ...

  // -> Reactive Loop
  state(model) ;

  // persist the new state
  // this is generally guarded and optimized
  model.persist() ;
} ;

model.persist = function() {

} ;

// Actions are pure functions /////////////////////////////////////////////
function action1(data) {
  // Logic that prepares the data to be presented to the model
  // ...

  // -> Reactive Loop
  present(data) ;

  // to avoid a page reload
  return false ;
}

function action2(data) {
  // Logic that prepares the data to be presented to the model
  // ...

  // -> Reactive Loop
  present(data) ;

  // to avoid a page reload
  return false ;
}

// State is a pure function /////////////////////////////////////////////
function state(model) {
  // the state behavior is hard wired
  stateRepresentation(model) ;
  nap(model) ;
}

function nap(model) {
  if (condition(model)) {
    var data = f(model) ;
    action2(data,model.present) ;
  }
}


// View is a pure function /////////////////////////////////////////////
function someView(model) {
  // render the view
  var output = '' ;
  // ...

  // wire the possible actions in the view
  output = output + 'onSubmit="JavaScript:return action1({data:data});'

  // ...
  // -> Reactive Loop
  display(output) ;
}

// Wiring /////////////////////////////////////////////
//
// Actions are known to the stateRepresentation() and nap()
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

Of course, JQuery event handlers could also be used:

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
// another option is to implement the state, model and actions on the server:\
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

One can also use [RxJS](https://github.com/Reactive-Extensions/RxJS/blob/master/doc/gettingstarted/events.md) to wire events to actions.

```javascript
var result = document.getElementById('submit');

var source = Rx.Observable.fromEvent(document, 'click');

var subscription = source.subscribe(function (e) {
  var data = { name: document.getElementById('name').value, ... }
  result.innerHTML = state.render(model.present(data));
});
```

Wiring is an important concern when implementing the pattern. For instance, the [mobx framework](https://medium.com/@mweststrate/pure-rendering-in-the-light-of-time-and-state-4b537d8d40b1#.q91pjr4c7) wires individual property value changes to the view rendering function, which is not representative of the behavior of the system and the processing of an action as a unit of work. As you can see in the example tab below, the view renders for state data changes:

```javascript
// The state of our app
var state = mobx.observable({
  nrOfSeats : 500,
  reservations : [],
  seatsLeft : function() { return this.nrOfSeats - this.reservations.length; }
});

// The UI; a function that is applied to the state
var ui = mobx.computed(function() {
  return "\nSeats left: " + state.seatsLeft +
      "Attendees: " + state.reservations.join(", ") + "";
});

// Make sure the UI is 'rendered' whenever it changes
ui.observe(function(newView) { console.log(newView) }, true);

// Put in some test data
state.reservations[0] = "Michel";
state.reservations.push("You?");
state.reservations[0] = "@mweststrate";
state.nrOfSeats = 750;
```

### Time Travel

Gunar Gessner has implemented [a client-side "Time Travel" dev tool](https://github.com/sam-js/sam-devtools) that enables you to take snapshots of the model after every action is processed and then restore the state of the system with a given snapshot.

The SAFE container implements [both a client-side and server-side "Time Travel" dev tool](https://github.com/jdubray/sam-safe/blob/master/README.md#time-travel).

<div style="position:relative;height:0;padding-bottom:75.0%"><iframe src="https://www.youtube.com/embed/o7ZIG4jT0xM?ecver=2" width="480" height="360" frameborder="0" style="position:absolute;width:100%;height:100%;left:0" allowfullscreen></iframe></div>


## Advanced Topics

### Micro Container

The [SAFE project](https://github.com/jdubray/sam-safe)(State-Action-Fabric-Element) is a micro-container for SAM implementations which can run in the browser or on node.js. At a minimum SAFE enables you to wire the elements of the pattern. It also comes with Session Management, Logging and Error Handling. The current version enables global validation and enforcement of actions, including action "hang back". Last but not least, it implements the Time Travel dev tool.

### Composition

The SAM pattern offers some generous composition mechanisms.

First Actions, as pure functions, compose naturally to present a single dataset to the model:

```javascript
C(data) = A(B(data))
```

This type of composition is a functional composition and the resulting action is considered to be a single action applied to the system from SAM's point of view.

Similarly, the State Representation (View) can be decomposed in a hierarchy of components:

```javascript
V = f( M )
f( M ) = f1( g1(M) + g2(M) ) + f2( h1(M) + h2(M) )
```

The most interesting composition mechanisms are at the pattern level itself. For instance, SAM supports an instance level composition where one instances runs in the browser and one instance runs in the server.

![](http://sam.js.org/assets/figures/fig9.jpg)

```javascript
// The composition has a single view
V = Ss( Ms ) + Sc( Mc )

// Instance c (browser) invokes an action on instance s (server)
// this action is generally invoked in the nap() function of the client
V = Ss( Ms.present( As(Mc) )
```

Though it is theoretically possible, it is highly recommended to refrain from invoking client actions from the server. The role of the server is rather to deliver the SAM client instance as part of its state representation.

Here is a [Parent/Child](https://github.com/jdubray/sam-samples/tree/master/react-child-instance) sample using React 15.0.2 with JSX/Babel. It shows how you can implement a complex form of wizard with a child instance and submit the resulting dataset to the parent, once the content of the form is valid.

NOTE: THIS IS JUST SOME INITIAL THOUGHTS, NEED MORE WORK:

SAM also offers an interesting alternative composition mechanism which enable to synchronize a client side and server side model, by which you present the same dataset to both the client side and server side model.

![](http://sam.js.org/assets/figures/fig11.jpg)

### State Machines

> Computation is a major topic of computer science,
and almost every object that computes is naturally viewed as a state machine.
Yet computer scientists are so focused on the languages used to
describe computation that they are largely unaware that those languages
are all describing state machines - [Dr. Leslie Lamport](http://research.microsoft.com/en-us/um/people/lamport/pubs/state-machine.pdf)

SAM is particularly well aligned with the traditional semantics of State Machines except for one simple, yet fundamental difference. Traditional State Machine semantics imply that the actions somehow connect two states, so State Machines are described as a series of tuples such as:

```
initialState = S0
(S0,A01,S1), (S1,A12,S2)...
```

This definition is somewhat of an approximation: it is not the action that decides the resulting state, it is the model, which, once the action has been applied (its value have been accepted, or not) that decides the resulting state (Aik are the allowed actions in a given state Si) :

```
initialState = S0 = S( M0 )
(S0, A00, A01), (S1, A10, A11, A12)...
S0 = S( M0 )
S1 = S( M1 )
```

The tuples (S<sub>i</sub>,A<sub>ik</sub>,S<sub>k</sub>) are merely an observation of the behavior of the state machine, rather than a physical representation of its runtime.
This change of perspective (not semantics) is minute, yet fundamental. SAM would not work (as well) if the traditional semantics of State Machine would be structuring the code. The SAM semantics are inclusive of this traditional structure, and therefore strictly compatible, but SAM by no means require that one uses a graph of State and Actions.

I believe that is why some people have expressed that SAM "feels natural".

The [Rocket Launcher](https://bitbucket.org/snippets/jdubray/9dgKp/sam-sample) example shows how to implement the Sx() functions:

```javascript
// Derive the current state of the system
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

I cannot emphasize enough that it is not necessary to adopt a State Machine structure to use SAM and more often than not if-then-else will be adequate. However, sometimes it may become easier to use a strict State Machine structure. These functions can be used to validate that an action is enabled or not. They can also more naturally break down the hierarchy in from which the view is rendered.

### APIs

SAM was originally designed to solve the strong coupling that MVC creates between the Front-End structure and the Back-End APIs. APIs can be composed at a couple levels in SAM:

- Actions: for APIs with no side-effect whatsoever on the model
- Model: for CRUD APIs that persist/populate the model property values (commonly called the Data Access Layer)

Concurrency issues aside, an action can present its values when after processing an API call, for instance:

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
      // parse the RSS feed into a json object
      parseString(body, function (err, result) {
        // prepare proposed dataset
        data.blog = result.rss.channel[0].item ;
        // present data to the model
        present(data) ;
     });
     }
   });
}
```

Similarly, the model can perform all the persistence operations (which could result in rejecting some proposed values) before passing control of the Reactive Loop to the State object. That is very different from React because you cannot touch the State without triggering a rendering of the view, which makes very little sense, when you think of it. It only makes sense when you spread the model state into the various components, and even then it warps the articulation of APIs with the Front-End reactive Flow.



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
