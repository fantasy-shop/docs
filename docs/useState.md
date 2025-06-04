# useState

### Counter (no hook)
```jsx
import React from 'react';

export default class NoUseHookCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  modify = (n) => {
    this.setState({ 
      count: n 
    });
  }

  render() {
    return (
      <div>
        <button onClick={() => this.modify(this.state.count + 1)}>
          No hook Count is {this.state.count}
        </button>
      </div>
    );
  }
}
```

### Counter (with hook)
```jsx
import React, { useState } from 'react'

export function UseStateCounter() {
  const [count, setCount] = useState(0)

  return (
    <div className="card">
      <button onClick={() => setCount((count) => count + 1)}>
        useState Count is {count}
      </button>
    </div>
  )
}
```


## useState
- useState는 React에서 함수형 컴포넌트(Function Component) 내에서 상태(state) 를 관리할 수 있게 해주는 훅(Hook) 입니다. 컴포넌트가 상태를 가지면 사용자 인터랙션, API 응답, 입력 값 등의 변화에 따라 UI를 업데이트할 수 있습니다.

### 기본 문법
- useState(초기값)
  - 상태의 초기값을 설정합니다. 여기선 0.
- \[count, setCount]
  - count: 현재 상태 값
  - setCount: 상태를 변경하는 함수
- setCount(newValue)
  - 상태를 변경하면 컴포넌트가 다시 렌더링됩니다.
```jsx
import { useState } from 'react';

const Example = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>현재 카운트: {count}</p>
      <button onClick={() => setCount(count + 1)}>증가</button>
    </div>
  );
};
```

#### 상태 업데이트
```jsx
setCount(prev => prev + 1);
```

#### 다양한 타입 사용 예시
```jsx
const [name, setName] = useState('');
```
```jsx
const [items, setItems] = useState([]);
```
```jsx
const [user, setUser] = useState({ name: '', age: 0 });
```
- 상태가 객체일 경우에는 전체를 덮어쓰기 때문에 부분 업데이트를 직접 처리해야 합니다.
```jsx
setUser(prev => ({ ...prev, age: 25 }));
```
