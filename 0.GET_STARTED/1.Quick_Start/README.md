# Quick Start


## Creating and nesting components

리액트 앱은 컴포넌트들로 구성됩니다. 컴포넌트는 하나의 UI 조각이며 자신만의 로직과 형태를 가집니다. 

리액트 컴포넌트는 **마크업**을 반환하는 자바스크립트 함수입니다.

```jsx
function MyButton() {
  return (
    <button>I'm a button</button>
  );
}

export default function MyApp() {
  return (
    <div>
      <h1>Welcome to my app</h1>
      <MyButton />
    </div>
  );
}
```

리액트 컴포넌트는 항상 대문자로 시작해야 합니다.

## Writing markup with JSX

위에서 본 markup 문법은 JSX라 불립니다.

JSX는 HTML보다 엄격합니다. 컴포넌트는 여러개의 JSX tags를 반환할 수 없고 `<></>`나 `<div>...</div>`로 감싸야 합니다.

## Adding styles

리액트에선 `className`으로 CSS class를 명시합니다. HTML의 `class` 속성과 동일합니다.

```jsx
<img className="avatar" />
```

리액트는 CSS 파일을 추가하는 방법을 따로 규정하지 않습니다. 

## Displaying data

JSX를 사용하면 markup을 자바스크립트에 집어 넣을 수 있습니다. `{}`를 사용하면 코드에서 일부 변수를 삽입하여 사용자에게 표시할 수 있습니다. 

```jsx
return (
  <h1>
    {user.name}
  </h1>
);
```

## Conditional rendering

조건부 렌더링을 위해선 자바스크립트 문법인 `if`문을 사용합니다.

```jsx
let content;
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}
return (
  <div>
    {content}
  </div>
);
```

더 간단한 코드를 짜고 싶으면 `?` 조건 연산자나 `&&` 논리 연산자를 사용하면 됩니다.

```jsx
<div>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```

```jsx
<div>
  {isLoggedIn && <AdminPanel />}
</div>
```

## Rendering lists

컴포넌트내에서 `map()`를 사용하여 상품 정보 배열을 `<li>` 배열로 변환할 수 있습니다.

```jsx
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];

const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return (
  <ul>{listItems}</ul>
);
```

`<li>`가 `key`속성을 가진 것을 주목해야 합니다. list의 요소마다 고유하게 식별할 수 있는 문자열 또는 숫자 값을 전달해줘야 합니다. <u>리액트는 key값을 사용하여 요소를 삽입하거나 삭제 또는 요소들의 순서가 재조정됐는지를 알 수 있습니다.</u>

## Responding to events

이벤트 핸들러 함수를 컴포넌트 내부에 선언함으로써 특정 이벤트에 반응할 수 있습니다.

```jsx
function MyButton() {
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

`onClick={handleClick}`에서 `handleClick()`로 호출하지 않는다는 점을 주목해야 합니다. 그저 함수를 전달만 해주면 됩니다. 

## Updating the screen

종종 컴포넌트가 정보를 "기억"하고 화면에 표시해야할 필요가 있습니다. 이럴땐 `state`를 컴포넌트에 추가해야 합니다.

```jsx
function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

`count`: 현재 상태 값
`setCount`: 상태 값을 업데이트하는 함수

초기 `count`값은 0이며 버튼을 클릭하면 `setCount`가 `count`에 1을 더해줍니다.


*만약 상태를 가진 같은 컴포넌트를 여러번 렌더링한다면 각각의 컴포넌트는 독립된 상태값을 가집니다.*
각각의 버튼들은 자신만의 `count`를 "기억"하고 다른 버튼들에는 영향을 주지 않습니다.

## Using Hooks

`use`로 시작하는 함수를 Hooks라 부릅니다. `useState`는 리액트 빌트인 Hook입니다. 다른 빌트인 Hooks는 [API reference](https://react.dev/reference/react)에서 볼 수 있습니다. 이것들을 조합하여 자신만의 Hooks를 작성할 수 있습니다.

Hooks는 다른 함수들과는 달리 몇가지 규칙이 있습니다. Hook은 컴포넌트의 최상단에서 호출해야 합니다. 만약 조건 또는 반복문 내에서 `useState`를 사용하고 싶다면, 새로운 컴포넌트로 추출하고 그곳에 Hook을 둬야 합니다.

## Sharing data between components

앞서 예시에선 `count`값을 두 버튼이 공유하지 않았습니다.

그러나, 종종 상태값을 공유해야 하는 경우가 생깁니다. 이를 위해 두 컴포넌트를 포함하는 가장 가까운 component로 `count`상태 값을 이동시켜야 합니다. 그러고 나서, state값이 필요한 하위 컴포넌트에게 `props`로 전달해줍니다.

```jsx
export default function MyApp() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}
```

그리고 MyButton 컴포넌트에서 전달 받은 props를 읽을 수 있도록 다음처럼 작성합니다.

```jsx
function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
}
```

이것을 "lifting state up"라고 합니다. state를 위로 끌어올림으로써 컴포넌트간 state 공유가 가능합니다.