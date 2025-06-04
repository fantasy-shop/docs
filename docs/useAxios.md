# useAxios
- useAxios는 React에서 Axios 라이브러리를 활용해 HTTP 요청을 간편하게 수행하도록 만든 커스텀 훅입니다. 서버와 통신하는 로직을 재사용하고 관리하기 쉽게 해줍니다.
- https://axios-http.com/



### 🧠 기본 개념
- useAxios 훅은 일반적으로 다음 기능을 포함합니다:
  - 요청 상태 관리 (loading, error, data)
  - 자동 또는 수동 요청(trigger 방식)
  - Axios 설정 공유 (base URL, 헤더 등)


### ✅ Axios의 요청 두 가지 방법
#### 1) 체이닝 방식 (메서드 체이닝 방식)
```js
axios.get("https://shopping.com/products", {
  headers: { Authorization: "Bearer token" }
});
```
- axios.get(url, config) 형식
- 메서드 이름(get, post, put, delete)으로 HTTP 메서드를 직접 지정
- 간결하지만 동적으로 구성하기엔 조금 덜 유연함

#### 2) 객체 기반 방식 (config object 방식)
```js
axios({
  url: "https://shopping.com/products",
  method: "GET",
  headers: {
    Authorization: "Bearer token"
  }
});
```
- 하나의 객체로 모든 정보를 넘김
- url, method, headers, params, data, timeout, baseURL 등 다양한 설정 가능
- useAxios나 axiosInstance 등을 쓸 때 자주 사용됨

#### 3) useAxios는 왜 config object를 쓰나?
- useAxios는 재사용 가능한 훅이기 때문에 다음과 같은 이유로 config object 방식이 더 적합합니다:
  - GET, POST, PUT 등 다양한 요청을 한 구조에서 처리 가능
  - props나 로직에 따라 동적으로 url, method 등을 바꾸기 쉬움
  - axios 인스턴스를 써도 쉽게 확장 가능

### 📦 React에서 useAxios가 필요한 이유
- React에서는 다음을 자주 반복해야 합니다:
  - 로딩 상태 관리
  - 에러 처리
  - 재요청
  - 요청 취소
  - 마운트 시 자동 요청

- 그래서 이런 반복 로직을 깔끔하게 추상화하려고 다음과 같은 custom hook을 만듭니다:
```jsx
function useAxios(config, options = { manual: false }) {
  // 내부적으로 axios(config)를 실행하지만, 상태를 자동 관리해줌
}
```

- 이런 방식은 컴포넌트에서 다음처럼 간단하게 쓸 수 있다는 장점이 있습니다:
```jsx
const { data, loading, error, refetch } = useAxios({
  url: "/products",
  method: "GET",
});
```


### ✅ 기본 버전 예시: useAxios
```jsx
import { useState, useEffect } from "react";
import axios from "axios";

function useAxios(config, options = { manual: false }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(!options.manual);
  const [error, setError] = useState(null);

  const fetchData = async () => {
    setLoading(true);
    try {
      const response = await axios(config);
      setData(response.data);
      setError(null);
    } catch (err) {
      setError(err);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    if (!options.manual) {
      fetchData();
    }
  }, []); // 의존성 없음 → 한 번만 실행

  return { data, loading, error, refetch: fetchData };
}

export default useAxios;
```


### Component 
```jsx
import React from "react";
import useAxios from "../hooks/useAxios";

function Users() {
  const { data, loading, error, refetch } = useAxios({
    url: "https://jsonplaceholder.typicode.com/users",
    method: "GET",
  });

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h2>User List</h2>
      <button onClick={refetch}>Refetch</button>
      <ul>
        {data.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```


### ⚙️ 확장 아이디어
- Axios 인스턴스를 받아서 전역 설정 적용 (e.g. 토큰)
- cancelToken으로 요청 취소
- debounce 또는 polling
- POST, PUT, DELETE 요청 지원
