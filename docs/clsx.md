# clsx


### clsx
- clsx는 문자열, 불리언, 객체 등 다양한 타입을 인자로 받아서 적절히 필터링하고 하나의 className 문자열로 반환해주는 함수야.

## 🔗 왜 Tailwind와 clsx를 같이 쓰는가?
### ✅ 1. 조건부 스타일 적용이 깔끔해짐

- Tailwind는 유틸리티 클래스를 직접 쓰는 방식이라 상태에 따라 다른 클래스를 적용하려면 조건문이 필요해.
- clsx 없이 하면 코드가 지저분해져.

- 예: clsx 없이 조건부 Tailwind
```jsx
<button className={isActive ? "bg-blue-500" : "bg-gray-500"}>
```

- 예: clsx로 조건부 Tailwind
```jsx
<button className={clsx(
  "text-white px-4 py-2 rounded",
  isActive ? "bg-blue-500" : "bg-gray-500"
)}>
```


### ✅ 2. 불필요한 클래스 무시
- clsx는 false, null, undefined 등을 자동으로 무시해 줘서 코드가 안전하고 깔끔함.
```jsx
clsx("base", isLoading && "opacity-50", isError && "text-red-500")
```
- → isLoading, isError가 false일 경우엔 해당 클래스가 자동으로 생략됨.


### ✅ 3. 배열, 객체 등 다양한 방식 지원
- Tailwind를 쓰다 보면 상태에 따라 여러 클래스를 넣고 빼야 하는데, clsx는 이를 쉽게 관리 가능:
```jsx
clsx(
  "p-4",
  { "bg-green-500": success, "bg-red-500": error }
)
```
- → 조건부 스타일 로직이 깔끔하게 분리됨.


### ✅ 4. Tailwind는 CSS만, clsx는 로직 담당
- Tailwind는 스타일 정의만 하고,
- clsx는 “어떤 클래스를 적용할지 결정하는 로직”을 담당.
- 역할이 명확히 분리돼서 가독성과 유지보수성이 좋아짐.

### 요약
| 이유                             | 설명 |
|----------------------------------|------|
| 조건부 스타일 적용               | 상태(isActive 등)에 따라 클래스를 유연하게 추가/제거 |
| 깔끔한 문법                      | 불필요한 if문 없이 클래스 처리 가능 |
| 다양한 형식 지원                 | 배열, 객체, 논리값 등 다양한 형태로 조건 지정 가능 |
| Tailwind와 역할 분리             | Tailwind는 스타일, clsx는 조건 로직 처리 |
| 유지보수성과 확장성 향상         | 조건 많아질수록 코드가 더 깔끔하게 유지됨 |


## 사용법
### ✅ 기본사용 
```jsx
import clsx from 'clsx'

const buttonClass = clsx(
  'w-full',
  'px-4 py-2',
  isActive && 'bg-blue-500',
  !isActive && 'bg-gray-300'
)

return <button className={buttonClass}>버튼</button>
```


### ⚙️ 유용한 패턴
#### 1. 조건에 따라 클래스 조합
```jsx
import clsx from 'clsx';

const buttonClass={clsx(
    'px-4 py-2 rounded',
    isPrimary ? 'bg-blue-500 text-white' : 'bg-gray-200 text-black',
    isDisabled && 'opacity-50 cursor-not-allowed'
)}

const Button = ({ isPrimary, isDisabled }) => {
  return (
    <button className={buttonClass} >
      버튼
    </button>
  );
};
```
#### 2. 조건과 키-값 스타일
```jsx
// 예제 2-1

clsx({
  'bg-red-500': error,
  'bg-green-500': success,
  'text-white': true,
})
```

```jsx
// 예제 2-2
const Badge = ({ type }) => {
  return (
    <span
      className={clsx(
        'text-xs font-semibold px-2.5 py-0.5 rounded',
        {
          'bg-green-100 text-green-800': type === 'success',
          'bg-red-100 text-red-800': type === 'error',
          'bg-yellow-100 text-yellow-800': type === 'warning',
        }
      )}
    >
      {type}
    </span>
  );
};
```

#### 3. Array와 Object 혼합
```jsx
const cardClass = clsx(
  'p-4 shadow',
  ['bg-white', 'text-gray-900'],
  { 'hover:shadow-lg': true }
);
```


### 📌 요약
| 목적                 | 사용 방식 예시                                                |
|----------------------|---------------------------------------------------------------|
| 기본 클래스 조합     | `clsx('text-white', 'bg-black')`                              |
| 조건부 Tailwind 조합 | `clsx(isDark && 'bg-gray-800')`                               |
| 여러 조건 조합       | `clsx({ 'bg-red-500': hasError, 'bg-green-500': isSuccess })` |
| 배열/객체 혼합       | `clsx('base', ['mt-2'], { hidden: !visible })`                |
