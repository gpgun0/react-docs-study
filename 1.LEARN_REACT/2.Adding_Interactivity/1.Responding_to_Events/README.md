# Responding to Events

이벤트 핸들러는 클릭, 호버, 포커싱 등의 인터랙션에 반응하여 트리거되는 함수입니다.

## You will learn
- 이벤트 핸들러를 작성하는 다양한 방법
- 부모 컴포넌트로부터 이벤트 핸들러 로직을 전달하는 방법
- 이벤트가 propagate되는 방식과 이를 멈추는 방법

## Adding event handlers 

이벤트 핸들러를 추가하기 위해서 먼저 함수를 정의하고 [prop으로 전달](https://react.dev/learn/passing-props-to-a-component)합니다.

```jsx
export default function Button() {
  return (
    <button>
      I don't do anything
    </button>
  );
}
```

사용자 클릭에 반응하여 메세지를 띄우고 싶으면 다음 단계를 따라야 합니다.

1. `handleClick` 함수를 `Button`컴포넌트 안에 선언한다.
2. 함수안에 로직을 구현한다.
3. `onClick={handleClick}`을 `<button>` JSX에 추가한다.


```jsx
export default function Button() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```

여기서 `handleClick`이 바로 **이벤트 핸들러**입니다.

- 주로 컴포넌트 내부에 선언됩니다.
- `handle`로 시작하고 이벤트명이 뒤따르는 이름을 가집니다.

JSX 내부에 인라인으로 선언할 수도 있습니다.

```jsx
<button onClick={function handleClick() {
  alert('You clicked me!');
}}>

<button onClick={() => {
  alert('You clicked me!');
}}>
```

### Pitfall

이벤트 핸들러로 함수를 전달할 때 함수 자체를 그대로 전달해야지 호출하면 안됩니다.

| passing a function (correct) | calling a function (incorrect) |
|:---:|:---:|
| `<button onClick={handleClick}>` | `<button onClick={handleClick()}>` |

첫번째 경우는 리액트에게 이 함수를 기억했다가 사용자가 버튼을 클릭했을 때만 함수를 실행하라고 말하는 겁니다.

두번째 경우는 [rendering](https://react.dev/learn/render-and-commit)동안에 즉시 함수를 실행하게 됩니다. [JSX { and }](https://react.dev/learn/javascript-in-jsx-with-curly-braces) 내부의 자바스크립트는 바로 실행되기 때문입니다.

인라인 코드로 작성할 때도 마찬가지 입니다.

| passing a function (correct) | calling a function (incorrect) |
|:---:|:---:|
| `<button onClick={() => alert('...')}>` | `<button onClick={alert('...')}>` |

아래와 같은 코드는 클릭할 때 실행되지 않고 렌더링될 때마다 실행됩니다.

```jsx
// This alert fires when the component renders, not when clicked!
<button onClick={alert('You clicked me!')}>
```

의도한대로 클릭할 때 함수를 실행하려면 익명함수로 감싸야 합니다.

```jsx
<button onClick={() => alert('You clicked me!')}>
```

[Read more about arrow functions.](https://javascript.info/arrow-functions-basics)


### Reading props in event handlers

이벤트 핸들러는 컴포넌트 내부에 있기 때문에 prop에 접근할 수 있습니다.

```jsx
function AlertButton({ message, children }) {
  return (
    <button onClick={() => alert(message)}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <AlertButton message="Playing!">
        Play Movie
      </AlertButton>
      <AlertButton message="Uploading!">
        Upload Image
      </AlertButton>
    </div>
  );
}
```

### Passing event handlers as props 

종종 부모 컴포넌트가 자식 컴포넌트의 이벤트 핸들러를 명시하길 원할 것입니다. 아래 `Button` 컴포넌트는 prop으로 이벤트 핸들러를 받습니다. 부모 컴포넌트에서 원하는 이벤트 핸들러를 선언하여 `Button`컴포넌트에게 prop으로 전달할 수 있습니다.

```jsx
function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}

function PlayButton({ movieName }) {
  function handlePlayClick() {
    alert(`Playing ${movieName}!`);
  }

  return (
    <Button onClick={handlePlayClick}>
      Play "{movieName}"
    </Button>
  );
}

function UploadButton() {
  return (
    <Button onClick={() => alert('Uploading!')}>
      Upload Image
    </Button>
  );
}

export default function Toolbar() {
  return (
    <div>
      <PlayButton movieName="Kiki's Delivery Service" />
      <UploadButton />
    </div>
  );
}
```

[디자인 시스템](https://uxdesign.cc/everything-you-need-to-know-about-design-systems-54b109851969)을 사용한다면 button같은 컴포넌트는 styling만 포함하고 행동은 명시하지 않는 것이 일반적입니다. 대신, `PlayButton`나 `UploadButton`같은 컴포넌트들이 이벤트 핸들러를 전달해주게 됩니다.

### Naming event handler props 

`<button>`이나 `<div>`같은 내장 컴포넌트들은 오직 `onClick`같은 [browser event names](https://react.dev/reference/react-dom/components/common#common-props) 만 지원합니다. 그러나, 직접 컴포넌트를 만들 땐 원하는대로 이벤트 핸들러 prop 이름을 짓는게 가능합니다.

컨벤션에 따르면 이벤트 핸들러 prop은 `on`으로 시작해야 합니다.

```jsx
function Button({ onSmash, children }) {
  return (
    <button onClick={onSmash}>
      {children}
    </button>
  );
}

export default function App() {
  return (
    <div>
      <Button onSmash={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onSmash={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```

[Learn more about writing accessible markup.](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML)

## Event propagation

이벤트 핸들러는 컴포넌트에 포함되어 있을 수 있는 하위 컴포넌트의 이벤트 또한 캐치합니다. 우리는 이를 이벤트 "bubbles" 또는 "propagates"라 부릅니다. 이벤트가 발생한 곳에서 시작하여 트리를 따라 올라갑니다.

`<div>`는 두 버튼을 포함합니다. `<div>`와 각각의 버튼 둘 다 자신만의 `onClick` 핸들러를 가집니다. 버튼을 클릭했을 때 어느 핸들러가 실행될까요?

button을 클릭한다면 그것의 `onClick`이 실행된 뒤 `<div>`의 `onClick` 이벤트 핸들러가 뒤따라 실행됩니다. 

### Pitfall

리액트에서 `onScroll`을 제외한모든 이벤트는 propagte됩니다. 

### Stopping propagation 

이벤트 핸들러는 자신만의 인자로 **이벤트 객체**를 받습니다. 컨벤션에 따라 이는 주로 `e`라고 부릅니다.

이 이벤트 객체로 propagation을 멈출 수 있습니다. 부모 컴포넌트까지 이벤트가 도달하는 것을 방지하기 위해 `e.stopPropagation()`을 사용할 수 있습니다.

```jsx
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}

export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!');
    }}>
      <Button onClick={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onClick={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```

1. 리액트는 `<button>`에게 전달된 `onClick` 핸들러를 호출합니다.
2. `Button`에 명시된 핸들러는 다음을 따릅니다.
   - `e.stopPropagation()`을 호출하여 버블링을 막습니다.
   - `Toolbar`가 전달한 `onClick`함수를 실행합니다.
3. `ToolBar`에 명시된 함수가 button의 alert를 보여줍니다.
4. propagation이 멈췄기 때문에, `<div>`의 `onClick`은 실행되지 않습니다.

#### Deep Dive - Capture phase events

드물게, `propagation`을 멈췄을지라도 자식 요소들의 모든 이벤트를 캐치할 필요가 있습니다. 예를 들어, 분석을 위해 클릭 로그를 기록하고 싶다면 이벤트 핸들러 뒤에 `Capture`를 붙여야 합니다.

```jsx
<div onClickCapture={() => { /* this runs first */ }}>
  <button onClick={e => e.stopPropagation()} />
  <button onClick={e => e.stopPropagation()} />
</div>
```

1. 아래로 이동하며 모든 `onClickCapture` 핸들러를 호출합니다.
2. 클릭한 요소의 `onClick` 핸들러를 실행합니다.
3. 위로 이동하며 모든 `onClick` 핸들러를 실행합니다.

### Passing handlers as alternative to propagation 

### Preventing default behavior 

## Can event handlers have side effects? 

당연합니다. 이벤트 핸들러는 side effect에 가장 적합한 곳입니다.

렌더링 함수와는 달리 이벤트 핸들러는 [순수](https://react.dev/learn/keeping-components-pure)하지 않아도 됩니다. 따라서 무언가 변경되기에 가장 좋은 곳입니다. 예를 들어, 타이핑하여 인풋 값을 바꾸거나 버튼을 눌러 리스트를 변경하는 것이 있습니다. 그러나, 어떤 정보를 변경하기 위해선 이를 저장할 방법이 필요합니다. 리액트에선 [state, a component’s memory](https://react.dev/learn/state-a-components-memory)을 사용하여 해결할 수 있습니다.