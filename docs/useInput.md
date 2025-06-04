# useInput
- useInput은 일반적으로 React에서 폼 입력(input) 상태를 관리하기 위해 사용하는 **커스텀 훅(custom hook)**입니다. React 자체에는 useInput이라는 훅이 내장되어 있지 않지만, 개발자가 자주 만들어서 사용하는 패턴입니다.

### 기본 형태의 useInput 예제
#### useInput
```jsx
// useInput.jsx
import { useState } from 'react';

function useInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  const onChange = (event) => {
    setValue(event.target.value);
  };

  const reset = () => {
    setValue(initialValue);
  };

  return {
    value,
    bind: { value, onChange },   // input에 바인딩할 데이터만 따로
    reset,
    setValue,
  };
}

export default useInput
```
#### UseInput component
```jsx
// component
import useInput from '../../Hooks/useInput'

function UseInput() {
  const nameInput = useInput("Mr.");
  const emailInput = useInput('@');

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log('Name:', nameInput.value);
    console.log('Email:', emailInput.value);
    nameInput.reset();
    emailInput.reset();
  };

  return (
    <div>
      <h1>Hello</h1>
      <form onSubmit={handleSubmit}>
        <input type="text" placeholder="Name" {...nameInput.bind} />
        <input
          type="email"
          placeholder="Email" {...emailInput.bind}
        />
        <button type="submit">Submit</button>
      </form>
    </div>
  )
}

export default UseInput
```


### {...nameInput.bind}가 전개되고 HTML-like로 변환되는 과정
#### 1단계 (축약문법)
- ES6의 객체 리터럴 축약 문법 (shorthand property names)이 적용되어 있다.
```jsx
bind: {
  value: value,
  onChange: onChange
}
```
```jsx
bind: { value, onChange }
```

#### 2단계 (전개)
- bind를 쓰게 되면, bind로 묶은 value와 onChange가 나란히 전개 되므로,
```jsx
const value = "abc"
const onChange = () => {}
{ value: value, onChange: onChange }
```
```jsx
<input type="text" {...nameInput.bind} />                           // 이 코드는 아래의 코드로 변환됨.
<input type="text" {...{ value: "abc", onChange: () => {} }} />     // 이 코드는 JSX에서 아래의 HTML-like로 변환됨.

```

#### 3.단계 (JSX 에서 쓰는 HTML-like로 변환)
- 최종적으로 아래 코드로 변횐됨.
```jsx
<input type="text" value="abc" onChange={() => {}}  />
```


### 확장된 형태의 useInput
- 유효성 검사 추가 : 최소길이 
- trimOnBlur 기능 추가 : 입력 후 포커스를 잃었을 때 자동으로 앞뒤 공백을 제거합니다.
```jsx
import { useState } from 'react';

function useInput(initialValue, validator) {
  const [value, setValue] = useState(initialValue);

  const onChange = (event) => {
    const value = event.target.value;

    // validator(maxLen)를 사용한 업데이트
    let willUpdate = true;

    if (typeof validator === 'function') {
      willUpdate = validator(value);         // validator가 false를 반환하면 업데이트 방지
    }

    if (willUpdate) {
      setValue(value);
    }
  };

  // input에 포커스를 잃었을 때 자동으로 공백을 제거
  const onBlur = () => {
    const trimmed = value.trim();

    if (trimmed !== value) {
      setValue(trimmed);
    }
  };

  // inputValue를 초기화
  const reset = () => {
    setValue(initialValue);
  };

  return {
    value,
    bind: { value, onChange, onBlur }, // trimOnBlur 포함
    reset,
    setValue,
  };
}

export default useInput;
```
- 사용 예시
```jsx
const nameInput = useInput("Mr.", maxLen);

<input type="text" {...nameInput.bind} />
```
