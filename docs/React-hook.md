# React Hook


## 1. 리액트 훅이란?
- React 16.8부터 도입된 기능으로, 클래스 없이 상태 관리, 사이드 이펙트 처리 등을 할 수 있게 해줌.

## 2. React에서 “Hook”이라는 용어를 쓴 이유
- React 팀은 함수형 컴포넌트에 “기능을 걸 수 있는(연결할 수 있는)” 방식을 원했어요.
- 기존에는 클래스 컴포넌트만 상태(state)나 생명주기(lifecycle)를 가질 수 있었지만,
```jsx
function MyComponent() {
  // 예전에는 불가능했음
  const [count, setCount] = useState(0);
}
```
- useState, useEffect 등은 이런 기능을 **“함수형 컴포넌트에 걸어준다(hook into)”**는 의미를 갖고 있어요.


## 3. “Hook into”라는 표현

- 영어 프로그래밍 관용어로 자주 쓰이는 표현:
- “Hook into something”
- 어떤 시스템이나 동작 흐름에 탭(tap)하거나 연결(hook)해서 기능을 확장하거나 개입하는 행위.
- React 공식 문서에도 다음과 같은 표현이 있어요:
- “Hooks let you hook into React features from function components.”
- 즉, 기존에 클래스에서만 가능했던 React의 기능을, 함수형 컴포넌트에서도 사용할 수 있도록 “연결(hook)“한다는 개념이에요.
### 정리
| 항목               | 설명 |
|--------------------|------|
| **용어**           | Hook |
| **의미**           | React 기능에 "걸어(hook)" 사용할 수 있도록 만든 API |
| **어원**           | 영어 표현 "hook into" — 어떤 시스템/기능에 연결하거나 개입하는 의미 |
| **왜 이 이름인가** | 함수형 컴포넌트에 상태, 효과 등 기능을 "추가로 연결"하는 역할이기 때문 |


## 4. 대표적인 내장 훅
| 훅 이름          | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| `useState`       | 상태(state)를 함수형 컴포넌트에서 사용할 수 있게 해줌                |
| `useEffect`      | 컴포넌트 생명주기 함수처럼 동작. 사이드 이펙트(데이터 fetch 등) 처리 |
| `useRef`         | DOM 참조 또는 렌더링과 무관한 값을 저장할 때 사용                    |
| `useContext`     | Context API를 사용하여 전역 상태를 공유할 수 있게 해줌               |
| `useReducer`     | 복잡한 상태 로직을 관리할 때 `useState` 대신 사용                    |
| `useMemo`        | 연산 결과를 메모이제이션하여 불필요한 계산 방지                      |
| `useCallback`    | 함수를 메모이제이션하여 불필요한 리렌더링 방지                        |
| `useLayoutEffect`| `useEffect`와 비슷하지만, DOM 업데이트 직후 동기적으로 실행됨         |
| `useImperativeHandle` | `ref`를 사용할 때 부모가 자식 컴포넌트의 특정 함수를 제어하게 함 |
| `useId`          | 접근성과 관련된 고유 ID 생성 (React 18+)                             |
| `useTransition`  | UI 응답성과 관련된 트랜지션 처리 (React 18+)                         |
| `useDeferredValue` | 입력 지연 처리 (React 18+)                                           |
