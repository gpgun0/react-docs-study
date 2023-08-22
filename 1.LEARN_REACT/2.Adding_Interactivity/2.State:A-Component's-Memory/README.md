# State: A Component's Memory

컴포넌트는 인터랙션 결과로써 화면 속 무언가를 바꿀 필요가 있습니다. form에 타이핑을 하여 input field를 업데이트하고, image carousel의 "next"를 눌러 어느 이미지가 보여야 할지 정해줘야 합니다. 컴포넌트는 이러한 것들을 "기억할" 필요가 있습니다. 리액트에선 이러한 종류의 컴포넌트별 메모리를 *state*라고 부릅니다.

## You Will Learn

- [useState](https://react.dev/reference/react/useState) 훅으로 state 변수 추가하기
- useState 훅이 반환하는 것
- 한개 이상의 state를 추가하는 법
- state가 local인 이유

## When a regular variable isn’t enough 

index를 let으로 선언한 뒤 button을 클릭하면 의도한대로 동작하지 않습니다.

```jsx
import { sculptureList } from './data.js';

export default function Gallery() {
  let index = 0;

  function handleClick() {
    index = index + 1;
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
      <p>
        {sculpture.description}
      </p>
    </>
  );
}
```

아래 두가지가 지역 변수 index의 변경이 화면에 보이는 것을 막습니다.

1. **Local variables don’t persist between renders.** 리액트가 이 컴포넌트를 두번 렌더링할 때, 처음부터 렌더링합니다. 따라서 지역 변수의 어떠한 변경 사항도 고려하지 않습니다.
2. **Changes to local variables won’t trigger renders.** 리액트는 새 데이터로 (index+1) 컴포넌트를 다시 렌더링해야 되는 것을 인지하지 못합니다.

컴포넌트를 새 데이터로 업데이트하려면 두 조건이 필요합니다.

1. 렌더링 사이에서 데이터를 **유지하기**
2. 리액트가 새로운 데이터로 컴포넌트를 렌더링할 수 있도록 **트리거하기** (리렌더링)

`useState` 훅은 이러한 두 특성을 제공합니다.

1. 두 렌더링 사이에서 data를 유지할 수 있는 **state**
2. 변수를 업데이트하고 리액트가 컴포넌트를 다시 렌더링하도록 트리거하는 **state setter 함수**

## Adding a state variable 

```jsx
let index = 0;
```

대신 아래 코드를 사용해야 합니다.

```jsx
import { useState } from 'react';

const [index, setIndex] = useState(0);
```

> [ and ] 문법은 [array destructing](https://javascript.info/destructuring-assignment)이며 배열로 부터 변수들을 읽을 수 있게 해줍니다.

`handleClick`안에서 동작하는 코드입니다.

```jsx
import { useState } from 'react';
import { sculptureList } from './data.js';

export default function Gallery() {
  const [index, setIndex] = useState(0);

  function handleClick() {
    setIndex(index + 1);
  }

  let sculpture = sculptureList[index];
  return (
    <>
      <button onClick={handleClick}>
        Next
      </button>
      <h2>
        <i>{sculpture.name} </i> 
        by {sculpture.artist}
      </h2>
      <h3>  
        ({index + 1} of {sculptureList.length})
      </h3>
      <img 
        src={sculpture.url} 
        alt={sculpture.alt}
      />
      <p>
        {sculpture.description}
      </p>
    </>
  );
}
```

### Meet your first Hook

리액트에서 `useState`처럼 `use`로 시작하는 함수는 **Hook**이라고 부릅니다.

*Hooks*는 리액트가 렌더링 되는 동안에만 사용 가능한 특별한 함수입니다. 이것으로 다양한 리액트 기능을 사용할 수 있습니다.

State는 이 기능들 중 하나이며 다른 것들은 추후에 다뤄볼 것입니다.

#### Pitfall

`use`로 시작하는 함수인 Hooks은 컴포넌트의 최상단에서 호출되어야만 합니다. 훅을 조건문, 반복문 또는 nested 함수 안에서 호출할 수 없습니다. 훅을 컴포넌트가 요구하는 무조건적인 선언이라고 생각하면 편합니다. 마치 파일 최상단에 모듈을 "import"하는 것처럼 컴포넌트 최상단에서 리액트가 필요한 기능을 "사용(use)"하는 것입니다.

### Anatomy of `useState`

`useState`를 호출하면 리액트에게 이 컴포넌트가 기억해야할 것이 있다고 말합니다.

```jsx
const [index, setIndex] = useState(0);
```

이 경우는 React가 `index`를 기억하길 원합니다.

`useState`의 유일한 인자는 state의 **초기값**입니다. 위 예시에선 `index`의 초기값을 `0`으로 둡니다.

컴포넌트가 렌더링될 때마다 `useState`는 두 값을 포함한 배열을 반환합니다.

1. 저장한 값을 지닌 **state** (`index`)
2. state를 업데이트하고 다시 컴포넌트를 렌더링하도록 트리거해주는 **state setter function** (`setIndex`)


```jsx
const [index, setIndex] = useState(0);
```

1. 컴포넌트가 **처음 렌더링** 됩니다. `useState(0)` 반환값: `[0, setIndex]`
2. **state를 업데이트** 합니다. 
3. 컴포넌트가 **다시 렌더링**됩니다. `useState(0)`의 반환값: `[1, setIndex]`
4. 이 과정이 이어집니다.

## Giving a component multiple state variables 

하나의 컴포넌트는 여러개의 state를 가질 수 있습니다.

state가 서로 관련 없다면 여러개의 state를 가지는 것은 좋은 생각입니다. 하지만, 두개 이상의 state가 서로 관련 있다면 이를 하나로 묶는 것이 좋습니다. 예를 들어, 양식에 여러 인풋값을 입력해야 한다면 이를 하나의 객체 state로 관리하는 것이 용이합니다.

[Choosing the State Structure](https://react.dev/learn/choosing-the-state-structure)

### Deep Dive - How does React know which state to return? 

생각해보면 `useState` 훅은 이것이 어떤 state인지에 대한 정보나 식별자를 받지 않습니다. 그런데 `useState`는 어떻게 어떤 state를 반환할지 아는 것일까요? 

훅은 컴포넌트가 렌더링될 때 항상 같은 순서대로 호출되기 때문입니다. 이것이 항상 훅이 같은 순서로 호출되어야 하는 이유입니다. 

내부적으로 리액트는 모든 컴포넌트에 대한 state 쌍을 가지고 있습니다. 또한 훅은 렌더링 되기 전에 현재 state 쌍을 가리키고 있는 `0`으로 초기화된 `index`를 유지합니다. `useState`가 호출될 때마다 리액트는 다음 state 쌍을 주고 `index`를 증가시킵니다.

[React Hooks: Not Magic, Just Arrays](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)

## State is isolated and private 

state는 화면에 놓인 컴포넌트 인스턴스에서 지역적으로 존재합니다. 다시 말해 같은 컴포넌트를 두번 렌더링하면 각각의 복사본은 완전히 독립적인 state를 가지게 됩니다! 둘 중 하나를 변경해도 다른것에 영향을 주지 않습니다.

이것으로 state가 모듈 최상단에 선언된 일반 변수와의 차이점을 가집니다. state는 특정 함수 호출이나 code 공간에 묶여 있는 것이 아니라, 스크린에 놓인 특정 장소에 "local"하게 놓입니다. `<Gallery />`를 두번 호출하면 state는 개별적으로 저장됩니다.

그리고 `Page`컴포넌트가 `Gallery` 컴포넌트 state 또는 그것이 무얼 가지고 있는지에 대해 전혀 모른다는 사실이 중요합니다. props와는 다르게 state는 그것이 선언된 컴포넌트에서 완전히 private합니다. 부모 컴포넌트는 이 state를 변경할 수 없습니다. state를 어느 컴포넌트에 추가해도 그 외 다른 컴포넌트에는 영향을 끼치지 않습니다.
