# react-hook-form

```bash
npm install react-hook-form
```

- react-hook-form은 React에서 간단하고 성능 좋은 폼 처리를 가능하게 해주는 라이브러리입니다.
- Formik이나 Redux Form보다 더 가볍고 빠르며, 불필요한 리렌더링을 줄이는 방식으로 설계되어 있습니다.


### 🔧 주요 기능 요약
| 함수/속성          | 설명                                                                      |
|--------------------|---------------------------------------------------------------------------|
| `register`         | input 요소를 폼에 등록하고 유효성 검사 규칙을 연결함                      |
| `handleSubmit`     | 제출 이벤트를 처리하고 유효성 검사 후 데이터를 반환함                     |
| `formState.errors` | 유효성 검사 에러 정보를 담고 있는 객체                                    |
| `reset`            | 폼 상태를 초기화함 (기본값으로 리셋 가능)                                 |
| `watch`            | 특정 필드의 값을 실시간으로 관찰함                                        |
| `setValue`         | 특정 필드의 값을 수동으로 설정함                                          |
| `getValues`        | 현재 폼에 있는 값들을 가져옴                                              |
| `trigger`          | 유효성 검사를 수동으로 실행함                                             |
| `control`          | `Controller`와 함께 사용되며 커스텀 컴포넌트와 폼을 연결함                |
| `Controller`       | 외부 UI 컴포넌트 (예: Material UI, React Select 등)와 `react-hook-form`을 연결할 때 사용 |


### 🧪 간단 예제
```jsx
import { useForm } from 'react-hook-form';

const MyForm = () => {

  const {
    register,
    handleSubmit,
    formState: { errors },
    reset,
  } = useForm();

  const onSubmit = (data) => {
    console.log(data);
    reset(); // 폼 초기화
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("email", { required: "이메일은 필수입니다." })}
        placeholder="Email"
      />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        type="password"
        {...register("password", { required: true, minLength: 6 })}
        placeholder="Password"
      />
      {errors.password && <p>비밀번호는 6자 이상이어야 합니다.</p>}

      <button type="submit">회원가입</button>
    </form>
  );
};
```


### 📌 장점 요약
- ✅ 리렌더링 최소화 → 퍼포먼스 향상
- ✅ 간단한 API → 빠르게 폼 작성 가능
- ✅ 유효성 검사가 쉬움 (Yup 같은 라이브러리와 통합 가능)
- ✅ 컨트롤되지 않은 컴포넌트 기반 → 성능에 유리
