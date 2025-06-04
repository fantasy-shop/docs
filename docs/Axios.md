# Axios 개념 정리

## Axios란?

> HTTP 요청을 쉽게 보낼 수 있게 도와주는 자바스크립트 라이브러리

- 쉽게 말해, **백엔드 서버나 외부 API와 데이터를 주고받을 수 있도록 도와주는 도구**입니다.
- React 같은 프론트엔드 프레임워크와 함께 자주 사용됩니다.

---

## HTTP 요청이란?

> 서버에 "정보 좀 주세요!" 하는 것

- **클라이언트**(브라우저, 앱)가 요청을 보내고  
  **서버(API)**가 응답을 해주는 방식입니다.

---

## HTTP 요청의 4가지 핵심 종류

| 메서드 | 설명               | 예시                      |
| ------ | ------------------ | ------------------------- |
| GET    | 정보를 달라고 요청 | "상품 목록 좀 보여줘!"    |
| POST   | 정보를 서버에 보냄 | "장바구니에 이거 담아줘!" |
| PUT    | 기존 정보를 수정함 | "배송 주소를 바꿀게요!"   |
| DELETE | 정보를 삭제함      | "장바구니에서 이거 빼줘!" |

---

## HTTP 요청을 보내는 표준 도구

### 1. fetch (기본 내장, 표준 API)

- 자바스크립트에 기본 내장된 HTTP 요청 함수
- 브라우저만 있으면 따로 설치 없이 사용 가능

#### ❗ 하지만?

- 응답을 직접 JSON으로 파싱해야 함
- 에러 처리가 조금 까다롭고 복잡함
- 공통 설정이나 요청 취소 등을 하려면 직접 구현해야 함

### 2. Axios (외부 라이브러리)

- 설치가 필요함
  ```bash
  npm install axios
  ```

#### ❗ 하지만?

- 응답을 바로 res.data로 사용 가능 (자동 파싱)
- 에러 처리 명확 (try/catch나 .catch)
- 헤더 설정, 요청 취소, 인터셉터 등 고급 기능도 손쉽게 사용 가능
- 더 직관적이고 사용이 쉬움

---

## 비교 예시 (상품 목록을 GET 요청으로 받아오기)

### fetch 사용

```js
fetch("https://shopping.com/products") // 1) 이 주소로 GET 요청을 보냄
  .then((response) => response.json()) // 2) 응답을 받으면 .json()으로 파싱 (자동 X)
  .then((data) => {
    // 3) JSON 데이터가 준비되면 실행
    console.log(data); // 4) 받아온 데이터 출력
  })
  .catch((err) => {
    // 5) 네트워크 에러 같은 큰 문제만 catch 됨
    console.error(err);
  });
```

- 응답 객체(response)가 먼저 오고 → `response.json()`으로 직접 JSON으로 바꿔야 함
- 400번대나 500번대 에러는 `.catch()`로 안 들어오고 `response.ok`로 따로 검사해야 함

### Axios 사용

```js
axios
  .get("https://shopping.com/products") // 1) 이 주소로 GET 요청을 보냄
  .then((res) => {
    // 2) 서버가 응답하면 실행되는 부분
    console.log(res.data); // 3) 응답에서 실제 데이터만 꺼내서 출력
  })
  .catch((err) => {
    // 4) 요청 도중 문제가 생기면 여기서 에러 처리
    console.error(err); //    (예: 인터넷 끊김, 서버 에러 등)
  });
```

- `res.data`: 응답에서 JSON 데이터를 바로 꺼낼 수 있음 → 자동으로 파싱됨
- 에러도 `.catch()` 안으로 잘 들어옴 (응답 실패 + 네트워크 에러 다 처리됨)

아주 간단한 프로젝트거나 외부 라이브러리 없이 하고 싶을 때는 **fetch**를 추천  
요청이 많고 복잡한 실전 프로젝트(웹 쇼핑몰, SNS 등)에는 **axios** 추천  
결론적으로 2차 프로젝트 주제가 쇼핑몰이기 때문에 **axios 사용하는 게 더 효율적이고 추천됨**

---

## 그렇다면 언제 axios를 사용하게 될까?

1. 상품 목록을 서버에서 가져올 때
2. 로그인할 때 사용자 정보를 보낼 때
3. 장바구니에 상품을 담을 때
4. 주문 정보를 서버로 전송할 때

---

## 사용 예시 — 상품 목록을 서버에서 받아와서 화면에 보여준다

```jsx
import React, { useEffect, useState } from "react";
import axios from "axios";

function ProductList() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    // GET 요청 보내기
    axios
      .get("https://shopping.com/products")
      .then((res) => {
        setProducts(res.data); // 받아온 데이터를 상태에 저장
      })
      .catch((err) => {
        console.error("데이터 불러오기 실패:", err);
      });
  }, []);

  return (
    <ul>
      {products.map((p) => (
        <li key={p.id}>
          {p.name} - {p.price}원
        </li>
      ))}
    </ul>
  );
}
```

### 작동 흐름 요약

#### 1단계: 컴포넌트가 렌더링됨

ProductList라는 React 컴포넌트가 화면에 처음 나타나요.

#### 2단계: `useEffect()` 실행됨

이 훅(`useEffect`)은 컴포넌트가 처음 화면에 나타났을 때 한 번 실행되는 로직이에요.  
이 안에서 Axios로 HTTP GET 요청을 보냅니다.

#### 3단계: Axios가 HTTP 요청을 보냄

Axios는 이 코드를 실행하면서 아래처럼 생긴 HTTP 요청 메시지를 자동으로 만들어 서버로 보냅니다.

```http
GET /products HTTP/1.1
Host: shopping.com
```

브라우저가 실제로 이 메시지를 서버로 날려요.

#### 4단계: 서버가 응답을 보냄

서버는 products 목록을 JSON으로 돌려줍니다.

```json
[
  { "id": 1, "name": "셔츠", "price": 20000 },
  { "id": 2, "name": "청바지", "price": 30000 }
]
```

#### 5단계: `res.data`에 데이터가 들어감

Axios는 서버 응답을 받고, 그 응답의 `data` 부분을 꺼내서 `setProducts()`에 넣습니다.

```js
setProducts(res.data);
```

이건 products라는 상태(state)를 바꿔주는 함수

#### 6단계: 상태가 바뀌면 다시 렌더링됨

`products` 상태가 바뀌면 React는 컴포넌트를 자동으로 다시 렌더링합니다.  
즉, 화면에 상품 목록이 나타나는 거예요.

---

## Axios Instance란?

**Axios를 커스터마이징해서 만들어 놓은 사본**입니다.

---

## Axios Instance가 필요한 이유?

기본 사용법은 간단하지만, 요청을 여러 번 보내야 할 때 **매번 같은 설정을 반복하게 되는 문제**가 있어요.

예를 들어 요청할 때마다 다음과 같은 설정을 반복해야 한다면 매우 불편하고, 코드도 지저분해집니다:

- 같은 API 주소 (`baseURL`)
- 같은 헤더 (예: 인증 토큰)
- 같은 응답 처리 방식

이런 경우를 해결하기 위해 **Axios Instance를 만들면**, 공통 설정을 미리 정의해두고 **필요할 때마다 꺼내 써서 코드의 재사용성과 가독성을 높일 수 있습니다.**

---

## 예시 코드: Axios 인스턴스 만들기

```javascript
// src/api/axiosInstance.js
import axios from 'axios';

// axios 인스턴스 생성
const instance = axios.create({
  baseURL: 'https://shopping.com/api',   // 모든 요청의 기본 URL
  timeout: 5000,                          // 요청 타임아웃 (5초)
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer my-token',  // 공통으로 붙일 토큰
  },
});

export default instance;
```

## 예시 코드: 인스턴스 사용하기

```javascript
// ProductList.jsx
import React, { useEffect, useState } from 'react';
import api from './api/axiosInstance'; // 위에서 만든 인스턴스 불러오기

function ProductList() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    api.get('/products') // baseURL이 있기 때문에 경로만 적으면 됨
      .then((res) => {
        setProducts(res.data);
      })
      .catch((err) => {
        console.error('에러:', err);
      });
  }, []);

  return (
    <ul>
      {products.map(p => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}
```
실무에서는 거의 항상 인스턴스를 만들어서 사용한다고 합니다.

---

## Axios 인스턴스 vs 커스텀 훅

### 공통점: **"중복을 줄이고, 재사용하기 위한 도구"**

| 목적         | Axios 인스턴스                           | 커스텀 훅                                 |
|--------------|-------------------------------------------|-------------------------------------------|
| 중복 제거     | 반복되는 API 설정 제거                    | 반복되는 상태 관리/로직 제거              |
| 재사용 가능   | 여러 곳에서 동일 설정으로 요청 가능       | 여러 컴포넌트에서 동일 로직 재사용 가능   |
| 유지보수 쉬움 | 설정을 한 곳에서 변경 가능               | 로직을 한 곳에서 관리 가능                |

---

### 차이점: **"무엇을 위한 도구냐"가 다름**

| 구분             | Axios 인스턴스                         | 커스텀 훅                                 |
|------------------|----------------------------------------|-------------------------------------------|
| 대상             | HTTP 요청 자체의 설정                  | 리액트 컴포넌트 안의 로직                |
| 형태             | 함수 객체 (`axios.create()`)           | React Hook (`useXxx()`)                  |
| 쓰는 환경        | Axios 기반의 네트워크 계층             | React 컴포넌트 내부                       |
| 상태(state)와 관계 | 상태 없음                             | 상태와 밀접 (예: `useState`, `useEffect`) |

---

### 결론 요약

- **Axios 인스턴스**: 요청 설정(주소, 헤더 등)을 묶는 도구  
- **커스텀 훅**: 리액트 안에서 로직과 상태를 묶는 도구  
➡️ **함께 사용하면 매우 효율적입니다!**

---

### Axios 인스턴스 + 커스텀 훅: 실전에서 유용한 설계

쇼핑몰 프로젝트처럼 여러 컴포넌트에서 API 요청이 자주 반복되는 경우,  
**커스텀 훅 + Axios 인스턴스 조합**이 좋은 설계 방식입니다.

- **중복 제거**: 같은 API 요청 코드가 여기저기 흩어지지 않음  
- **깔끔한 컴포넌트**: 컴포넌트는 UI만 담당, 로직은 훅에 위임  
- **유지보수 용이**: API 주소 바뀌어도 훅만 고치면 됨  
- **테스트 쉬움**: 가짜 데이터를 주입해서 테스트 가능  

---

### 커스텀 훅으로 만들면 좋은 API 요청 예시

| 기능                     | 커스텀 훅 이름                 | 설명                                |
|--------------------------|-------------------------------|-------------------------------------|
| 상품 목록 가져오기        | `useProducts()`               | 메인/카테고리 페이지 등에서 사용    |
| 상품 상세 정보 가져오기   | `useProductDetail(productId)` | 상세 페이지용                        |
| 장바구니 가져오기         | `useCart()`                   | 장바구니 페이지                     |
| 사용자 정보 가져오기      | `useUser()`                   | 마이페이지, 로그인 후 헤더 등       |
| 주문 내역 불러오기        | `useOrders()`                 | 주문 목록 페이지                    |
| 검색 결과 가져오기        | `useSearch(query)`            | 검색 페이지                         |

---

## 예시 코드: `useProducts()` 훅

```javascript
// src/hooks/useProducts.js
import { useEffect, useState } from 'react';
import api from '../api/axiosInstance'; // 만든 인스턴스 사용

function useProducts() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true); // 로딩 상태
  const [error, setError] = useState(null);     // 에러 상태

  useEffect(() => {
    api.get('/products')
      .then((res) => setProducts(res.data))
      .catch((err) => setError(err))
      .finally(() => setLoading(false));
  }, []);

  return { products, loading, error };
}

export default useProducts;
```

## 사용 예시: `useProducts()` 훅을 컴포넌트에서 사용하기

```javascript
// src/pages/ProductList.jsx
import useProducts from '../hooks/useProducts';

function ProductList() {
  const { products, loading, error } = useProducts();

  if (loading) return <p>로딩 중...</p>;
  if (error) return <p>에러 발생: {error.message}</p>;

  return (
    <ul>
      {products.map((p) => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}
```

## 대략적인 구조
```css
src/
├── api/
│   └── axiosInstance.js   ← Axios 인스턴스 설정
├── hooks/
│   └── useProducts.js     ← 상품 목록
├── pages/
```
