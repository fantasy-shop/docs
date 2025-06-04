# 함수형 컴포넌트가 클래스형 컴포넌트보다 선호되는 이유

- 함수형 컴포넌트(Functional Components)가 클래스형 컴포넌트(Class Components)보다 선호되는 이유는 다음과 같은 여러 개발자 경험(Developer Experience) 및 기술적 이점 때문입니다.


### ✅ 1. 코드가 더 간결하고 가독성이 좋음
- 함수형 컴포넌트는 단순히 함수로 구성되기 때문에 문법이 직관적이고 코드가 짧고 명확합니다.
```jsx
import React from "react";
import ReactDOM from "react-dom";

// 함수형 Greeting
function Greeting1({ name }) {
  return <h1>Hello, {name}</h1>;
}

// 또는 클래스형 Greeting
class Greeting2 extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

function App() {
  return (
    <div>
      <Greeting1 name="Alice" />
      <Greeting2 name="Bob" />
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById("root"));
```


### ✅ 2. React Hooks로 상태와 사이드 이펙트를 쉽게 관리
- Hooks (useState, useEffect, etc.) 덕분에 함수형 컴포넌트에서도 상태 관리, 생명주기 처리, 컨텍스트 등을 모두 처리할 수 있음.
- 클래스에서는 this.state, this.setState, componentDidMount 등 복잡하고 장황한 구조를 써야 함.


### ✅ 3. this 키워드 없음
- 함수형 컴포넌트는 this가 없어서 버그 유발 요소 감소.
- 클래스형은 this 바인딩 문제로 .bind(this)나 화살표 함수 사용이 필수적인 경우가 많았음.
```js
// bind(this)를 하지 않으면 handleClick 안의 this는 undefined가 되거나 원하는 컴포넌트 인스턴스를 가리키지 않게 됨.
this.handleClick = this.handleClick.bind(this); // 클래스에서 흔한 실수
```

### ✅ 4. 컴포넌트 간 로직 재사용이 쉬움
- 커스텀 훅(Custom Hook)을 통해 로직을 추상화하고 여러 컴포넌트에서 재사용 가능.
```js
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);
  useEffect(() => {
    const onResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', onResize);
    return () => window.removeEventListener('resize', onResize);
  }, []);
  return width;
}
```

### ✅ 5. 미래 지향적: React 팀의 주요 개발 방향
- React 팀은 함수형 컴포넌트 + 훅을 앞으로의 표준으로 삼고 있으며, 새로운 기능은 클래스 컴포넌트에 잘 지원되지 않음.
- 예: Server Components, React Suspense, Concurrent Features 등은 함수형 중심 설계


### ✅ 6. 테스트와 디버깅이 쉬움
- 사이드 이펙트가 적고, 단순 함- 수로 구성되어 있어 단위 테스트가 용이함.
- 상태와 props에 따라 동작이 결정되므로 Pure Function 패턴을 따르기 쉬움.


### ✅ 7. 퍼포먼스 이점 가능
- 함수형 컴포넌트는 필요한 곳에만 상태를 두고, 필요 시 React.memo, useMemo, useCallback 등으로 최적화 도구를 쉽게 적용 가능.
- 클래스형에서는 메서드나 렌더링 최적화가 더 복잡하고 코드가 장황함.


### 결론
| 항목                 | 함수형 컴포넌트           | 클래스형 컴포넌트         |
|----------------------|----------------------------|----------------------------|
| 문법 간결성           | ✅ 간결하고 직관적           | ❌ boilerplate가 많음       |
| 상태 및 생명주기 관리 | ✅ Hooks로 처리 가능         | ✅ 내장 메서드로 처리        |
| `this` 사용 여부      | ✅ 없음 (간편함)             | ❌ 있음 (바인딩 필요함)     |
| 로직 재사용           | ✅ 커스텀 훅으로 쉬움         | ❌ 복잡 (HOC, 믹스인 등)    |
| 최신 React 기능 지원  | ✅ 대부분 지원됨             | ❌ 일부만 지원됨            |
| 코드 테스트 용이성    | ✅ 단순함 (순수 함수처럼)     | ❌ 상대적으로 복잡함        |
| 퍼포먼스 최적화       | ✅ `useMemo`, `memo` 등 지원 | ❌ `shouldComponentUpdate` 등 필요 |
