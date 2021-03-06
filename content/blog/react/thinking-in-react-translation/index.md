---
title: '[번역] Thinking in React'
date: '2019-03-20'
category: 'React'
---

## 들어가기에 앞서

이 글은 React 공식문서 중 [Thinking in React](https://reactjs.org/docs/thinking-in-react.html)를 번역한 글입니다. 어투가 어색한 부분은 알려주시면 반영하도록 하겠습니다.

---

React 는 Javascript 로 규모가 크고 빠른 웹 앱을 만드는데에 최고의 방법이라고 생각합니다. 우리는 페이스북과 인스타그램도 리액트로 잘 확장해왔습니다.

React 의 가장 중요한 부분 중 하나는 앱을 만들 때 앱에 대해 어떻게 생각하고 만드는지 입니다. 이 문서에서는 React 를 이용하여 검색을 할 수 있는 상품 데이터 테이블을 만드는 과정을 보여줄 것입니다.

## 하나의 시안으로 시작하기

우리가 이미 JSON API 와 디자이너에게서 받은 시안이 있다고 상상해봅시다. 그 시안은 다음과 같습니다.

![Mockup](https://reactjs.org/static/thinking-in-react-mock-1071fbcc9eed01fddc115b41e193ec11-4dd91.png)

우리의 JSON API 는 아래와 같이 생긴 데이터를 반환합니다.

```
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## 1 단계: UI 를 하나의 컴포넌트 계층구조로 만들기

첫 번째로 해야할 일은 모든 컴포넌트마다 박스를 그리고 각자에게 이름을 주는 것입니다. 만약 디자이너와 일하고 있다면 이미 해놨을 수도 있으니 물어보세요! 포토샵 레이어 이름이 컴포넌트 이름으로 될 수 있습니다.

어떤 것이 한 컴포넌트가 될 수 있는지는 어떻게 알 수 있을까요? 새로운 함수 혹은 객체를 만들지 말지 결정할 때 사용되는 기술을 똑같이 사용해보세요. 그러한 기술 중 하나가 바로 [단일 책임 원칙](https://ko.wikipedia.org/wiki/%EB%8B%A8%EC%9D%BC_%EC%B1%85%EC%9E%84_%EC%9B%90%EC%B9%99) 입니다. 즉, 이상적으로 컴포넌트 하나는 한 가지 일만 수행해야 합니다. 만약 컴포넌트가 커진다면 작은 하위 컴포넌트들로 해체되어야 합니다.

JSON 데이터 모델을 자주 사용자에게 보여줘야하기 때문에 모델이 올바르게 작성되었다면 UI(컴포넌트 구조)와 잘 매핑될 것입니다. UI 와 데이터 모델은 같은 정보 아키텍처를 띠는 경향이 있기 때문에 UI 를 컴포넌트로 분리하는 작업은 보통 사소한 작업입니다. 정확히 하나의 데이터 모델을 나타내는 컴포넌트들로 분해하세요.

![Component diagram](https://reactjs.org/static/thinking-in-react-components-eb8bda25806a89ebdc838813bdfa3601-82965.png)

이 간단한 앱에는 다섯 개의 컴포넌트가 있습니다. 아래에 각 컴포넌트가 나타내는 데이터를 이태리체로 표시했습니다.

1. **`FilterableProductTable` (오렌지색):** 이 예제 전체를 포함
2. **`SearchBar` (파란색):** 모든 _사용자 입력_ 을 받음
3. **`ProductTable` (초록색):** _사용자 입력_ 에 기반한 _데이터 collection_ 을 필터링해서 보여줌
4. **`ProductCategoryRow` (옥색):** 각 _카테고리_ 이름을 보여줌
5. **`ProductRow` (빨간색):** 각 _상품_ 을 행 단위로 보여줌

`ProductTable`를 보면 테이블 헤더("Name"과 "Price" 라벨을 포함하는)가 하나의 컴포넌트가 아닌 것을 볼 수 있습니다. 자신의 취향에 따라 다른 방법을 사용해도 좋습니다. 이 예제에서는 `ProductTable`의 단일 책임인 _데이터 collection_ 을 렌더링 하는 것의 한 부분으로 봤기 때문에 이렇게 표현했습니다. 그러나 만약 이 헤더가 더 복잡해진다면(예를 들어 정렬을 위한 부분을 추가하는 것), `ProductTableHeader` 컴포넌트를 만드는 것이 합리적일 것입니다.

시안을 컴포넌트화 시켰으니 이제 하나의 계층구조로 정리해봅시다. 쉬운 작업입니다. 시안에서 컴포넌트 안에 있는 컴포넌트들을 자식으로 만들어주면 됩니다.

- `FilterableProductTable`
  - `SearchBar`
  - `ProductTable`
    - `ProductCategoryRow`
    - `ProductRow`

## 2 단계: React 로 정적인 앱 만들기

<p data-height="600" data-theme-id="0" data-slug-hash="BwWzwm" data-default-tab="js" data-user="lacker" data-embed-version="2" class="codepen"><a href="https://codepen.io">CodePen</a>에서 <a href="https://codepen.io/gaearon/pen/BwWzwm">리액트로 생각하기: 2단계 </a>코드를 확인하세요.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

드디어 컴포넌트 계층구조가 생겼으니 이제 앱으로 만들 시간입니다. 가장 쉬운 방법은 데이터를 가져와 UI 를 그리지만 상호작용은 없는 버전을 만드는 것 입니다. 이런 정적인 버전을 만드는 것은 단순하게 많은 타이핑만을 필요로 하지만 상호 작용을 추가한 버전을 만드는 것은 타이핑이 아닌 많은 생각을 필요로 하기때문에 이 두 과정은 따로 하는게 좋습니다. 그 이유를 살펴보겠습니다.

정적인 버전을 만들기 위해선 다른 컴포넌트들을 재사용하고 *props*를 이용해 데이터를 넘기는 컴포넌트를 만들어야 합니다. *props*는 부모 컴포넌트로부터 자식 컴포넌트에게 데이터를 전달하는 방식입니다. 만약 *state*의 개념에 익숙하다면 이런 정적인 버전을 만들 때에는 **state 를 쓰지 마세요.** State 는 오로지 상호작용 작업 즉, 시간이 지남에 따라 변할 수 있는 데이터를 위한 것입니다. 지금은 정적인 버전이기 때문에 state 는 필요하지 않습니다.

당신은 앱을 상향식 혹은 하향식으로 만들 수 있습니다. 즉, 계층구조에서 가장 상위의 컴포넌트(예를 들어 `FilterableProductTable`) 혹은 가장 하위의 컴포넌트(`ProductRow`)부터 시작할 수 있습니다. 간단한 예제에서는 하향식, 큰 프로젝트에서는 상향식으로 진행하는 것이 만들기에도, 테스트를 하기에도 쉽습니다.

이 단계가 끝나면 데이터를 렌더링하는 재사용가능한 컴포넌트 라이브러리를 갖게될 것입니다. 그 컴포넌트들은 정적인 버전이라면 오직 `render()` 메소드만을 가질 것입니다. 가장 상위의 컴포넌트(`FilterableProductTable`)는 데이터 모델을 props 로 가지고 있을 것입니다. 주어진 데이터 모델에 변화를 주고 `ReactDOM.render()`를 다시 호출한다면 UI 가 업데이트 될 것입니다. 복잡한 것이 없기 때문에 UI 가 어떻게 업데이트 되는지, 어디서 변화를 줘야하는지 알기 쉽습니다. 이렇게 React 의 **one-way data flow**(_one-way binding_ 이라고도 불려지는)는 모든 것을 모듈화하고 빠르게 유지합니다.

이 단계를 수행하는데 있어서 도움이 필요하다면 [React 문서들](/docs/)을 살펴보세요.

### 짤막 상식: Props vs State

리액트에는 props 와 state 라는 두 유형의 데이터 모델이 있습니다. 이 둘의 차이점을 이해하는 것은 매우 중요합니다. 만약 차이점을 명확하게 모르신다면 이 [React 공식 문서](/docs/interactivity-and-dynamic-uis.html)를 살펴보세요.

## 3 단계: UI 의 state 를 최소화(하지만 온전한 것으로)하기

UI 가 상호작용을 할 수 있으려면 주어진 데이터 모델에 변화를 줄 수 있어야 합니다. React 는 이를 **state**로 쉽게 구현할 수 있습니다.

앱을 올바르게 구현하기 위해 먼저 앱에 필요한 최소한의 변경 가능한 state 에 대해 생각해 봐야합니다. 여기서 핵심은 [DRY: _Don't Repeat Yourself_](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)입니다. 앱이 필요로 하는 상태를 꼭 최소한으로 표현하고 필요한 것들을 계산하세요. 예를 들어 TODO 리스트를 만들 때 TODO 항목들을 배열로 가지면서 그 갯수는 따로 state 변수로 가지고 있지 마세요. TODO 항목들의 갯수를 렌더링 하고 싶으면 배열의 길이를 가지고 하면됩니다.

이제 예제의 데이터들로 생각해 보세요. 우리는 다음과 같은 데이터를 가지고 있습니다:

- 원래의 상품 목록
- 사용자가 입력한 검색어
- 체크박스의 체크 값
- 필터링 된 상품 목록

각각을 살펴보고 어떤 것이 state 인지 알아봅시다. 각 데이터에 대해 세 가지의 질문만 하면됩니다.

1. 상위 컴포넌트로부터 props 로 전달됩니까? 그렇다면 아마도 state 가 아닙니다.
2. 시간이 지나도 변하지 않습니까? 그렇다면 아마도 state 가 아닙니다.
3. 컴포넌트의 다른 state 나 props 를 기반으로 계산될 수 있습니까? 그렇다면 state 가 아닙니다.

원래의 상품 목록은 props 로 전달되므로 state 가 아닙니다. 검색어와 체크박스 값은 변할 수 있고 다른 어떤 것으로부터 계산이 되지 않기 때문에 state 인 것 같습니다. 마지막으로 필터링 된 상품 목록은 원래의 상품 목록과 검색어, 체크박스에 의해 계산될 수 있으므로 state 가 아닙니다.

결론적으로 state 는 다음과 같습니다.

- 사용자가 입력한 검색어
- 체크박스의 체크 값

## 4 단계: State 가 어디에 있어야 하는지 알아보기

<p data-height="600" data-theme-id="0" data-slug-hash="qPrNQZ" data-default-tab="js" data-user="lacker" data-embed-version="2" class="codepen"><a href="https://codepen.io">CodePen</a>에서 <a href="https://codepen.io/gaearon/pen/qPrNQZ">리액트로 생각하기: 4단계</a>코드를 확인하세요.</p>

이제 우리는 앱의 최소한의 state 가 무엇인지 알았습니다. 다음은 어떤 컴포넌트가 이 state 를 변경하거나 _소유_ 해야 하는지 알아야 합니다.

기억해야 할 것: React 는 컴포넌트 계층구조에서 단방향 데이터 흐름에 관한 모든 것이라 할 수 있습니다. 어떤 컴포넌트가 어떤 state 를 소유해야 하는지 바로 명백하게 알 수 없을 수 있습니다. **이는 React 를 처음 접하는 사람에겐 종종 가장 이해하기 어려운 부분입니다.** 그러니 다음 단계들을 따라해보세요.

앱의 각 state 마다:

- 해당 state 를 기반으로 무언가를 렌더링 하는 모든 컴포넌트들을 찾으세요.
- 공통의 소유자 컴포넌트(위에서 찾았던 모든 컴포넌트들의 상위에 있는 컴포넌트) 하나를 찾으세요.
- 공통의 소유자 컴포넌트 혹은 다른 상위 컴포넌트가 그 state 를 소유해야 합니다.
- 해당 state 를 소유할 만한 컴포넌트가 무엇인지 못찾겠다면 단순히 그 state 를 가질 새로운 컴포넌트를 만들고 공통의 소유자 컴포넌트 상위 어딘가에 추가하세요.

이제 이 과정을 우리의 앱에 적용시켜봅시다:

- `ProductTable`은 state 를 기반으로 상품 목록을 필터링 해야하고 `SearchBar`는 검색어와 체크박스 state 를 그려야합니다.
- 공통의 소유자 컴포넌트는 `FilterableProductTable`이 됩니다.
- 따라서 검색어와 체크박스 state 는 `FilterableProductTable`에 있는 것이 개념적으로 말이 됩니다.

좋습니다, 우리의 state 는 `FilterableProductTable`에 있게 됐습니다. 먼저 앱의 초기 state 를 반영하기 위해 `this.state = {filterText: '', inStockOnly: false}`를 `FilterableProductTable`의 `constructor`에 추가합니다. 그리고 `filterText`와 `inStockOnly`를 `ProductTable`과 `SearchBar`에 props 로 전달합니다. 마지막으로 이 props 를 `ProductTable`에 있는 행들을 필터링 하는데에 사용하고 `SearchBar`에 form 요소들의 값으로 설정하세요.

당신은 앱이 어떻게 동작할 것인지 보이기 시작할 수 있습니다: `filterText`를 `"ball"`로 설정하고 앱을 새로고침 해보세요 데이터 테이블이 잘 업데이트 되는 걸 볼 수 있습니다.

## 5 단계: 정반대의 데이터 흐름 추가하기

<p data-height="600" data-theme-id="0" data-slug-hash="LzWZvb" data-default-tab="js,result" data-user="rohan10" data-embed-version="2" data-pen-title="Thinking In React: Step 5" class="codepen"><a href="https://codepen.io">CodePen</a>에서 <a href="https://codepen.io/gaearon/pen/LzWZvb">리액트로 생각하기: 5단계</a>코드를 확인하세요.</p>

지금까지 우리는 계층구조를 따라 내려오는 props 와 state 를 올바르게 렌더링하는 앱을 만들어 봤습니다. 이제 다른 방향의 데이터 흐름도 만들어 볼 차례입니다. 계층구조 깊숙히 있는 form 컴포넌트는 `FilterableProductTable`에서 state 를 업데이트 해야합니다.

React 는 프로그램이 어떻게 동작하는지 이해하기 쉽게하기 위해서 데이터의 흐름을 명백하게 합니다. 하지만 전통적인 양방향 데이터 바인딩보다 더 많은 타이핑을 필요로 합니다.

만약 우리의 지금까지 예제에서 체크박스에 타이핑 하거나 체크를 해보면 React 가 그 입력을 무시하는 걸 볼 수 있습니다. 이는 의도적인 것인데, `input`의 `value` 속성을 `FilterableProductTable`에서 전달된 `state`와 항상 같도록 설정했기 때문입니다.

우리가 원하는 것을 생각해봅시다. 우리는 사용자가 입력양식에 변화를 줄 때마다 해당 입력을 반영하도록 state 를 업데이트 하고 싶습니다. 컴포넌트는 오직 자신의 state 만을 업데이트 해야하기 때문에 `FilterableProductTable`는 state 를 업데이트 해야할 때마다 실행되는 `SearchBar`에 콜백함수를 전달할 것입니다.
우리는 입력에 대해 `onChange` 이벤트를 사용하여 이를 알릴 수 있습니다. 그렇게 `FilterableProductTable`에 의해 전달된 콜백함수는 `setState()`를 호출할 것이고 앱은 업데이트 될 것입니다.

복잡하게 들리겠지만, 실제로는 코드가 몇 줄 되지 않습니다. 그리고 앱 전체에서 데이터가 어떻게 흐르는지가 분명해집니다.

## 이게 답니다

이 과정이 당신에게 React 로 컴포넌트와 앱을 만드는 방법에 대한 아이디어를 줄 수 있으면 좋겠습니다. 당신이 해오던 것보다 약간 더 많은 타이핑을 할 수도 있지만, 코드를 작성하는 것보다 훨씬 많이 읽는다는 것을 기억하세요. 그리고 이 모듈화된 명백한 코드는 매우 읽기 쉽습니다. 당신이 방대한 컴포넌트 라이브러리를 만들기 시작할 때 이 명백성과 모듈성에 감사할 것입니다. 그리고 코드의 재사용과 함께 당신의 코드 줄은 줄어들기 시작할 것입니다. :)
