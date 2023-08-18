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

<!-- *Hooks*는 리액트가 렌더링되는 동안에만 사용 가능한 특별한 함수입니다. 그들은 "hook into" -->