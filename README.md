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

| 메서드 | 설명                          | 예시                           |
|--------|-------------------------------|--------------------------------|
| GET    | 정보를 달라고 요청           | "상품 목록 좀 보여줘!"         |
| POST   | 정보를 서버에 보냄           | "장바구니에 이거 담아줘!"      |
| PUT    | 기존 정보를 수정함           | "배송 주소를 바꿀게요!"        |
| DELETE | 정보를 삭제함                | "장바구니에서 이거 빼줘!"      |

---

## HTTP 요청을 보내는 표준 도구

### 1. fetch (기본 내장, 표준 API)

- 자바스크립트에 기본 내장된 HTTP 요청 함수  
- 브라우저만 있으면 따로 설치 없이 사용 가능

#### ❗ 하지만?

- 응답을 직접 JSON으로 파싱해야 함
- 에러 처리가 조금 까다롭고 복잡함
- 공통 설정이나 요청 취소 등을 하려면 직접 구현해야 함

---

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

# 비교 예시 (상품 목록을 GET 요청으로 받아오기)

## fetch 사용 (JS)

```js
fetch('https://shopping.com/products')          // 1) 이 주소로 GET 요청을 보냄
  .then((response) => response.json())          // 2) 응답을 받으면 .json()으로 파싱 (자동 X)
  .then((data) => {                             // 3) JSON 데이터가 준비되면 실행
    console.log(data);                          // 4) 받아온 데이터 출력
  })
  .catch((err) => {                             // 5) 네트워크 에러 같은 큰 문제만 catch 됨
    console.error(err);
  });
```

응답 객체(response)가 먼저 오고 → `response.json()`으로 직접 JSON으로 바꿔야 함  
400번대나 500번대 에러는 `.catch()`로 안 들어오고 `response.ok`로 따로 검사해야 함  

## Axios 사용 (JS)

```js
axios.get('https://shopping.com/products')      // 1) 이 주소로 GET 요청을 보냄
  .then((res) => {                               // 2) 서버가 응답하면 실행되는 부분
    console.log(res.data);                       // 3) 응답에서 실제 데이터만 꺼내서 출력
  })
  .catch((err) => {                              // 4) 요청 도중 문제가 생기면 여기서 에러 처리
    console.error(err);                          //    (예: 인터넷 끊김, 서버 에러 등)
  });
```
- `res.data`: 응답에서 JSON 데이터를 바로 꺼낼 수 있음 → 자동으로 파싱됨  
- 에러도 `.catch()` 안으로 잘 들어옴 (응답 실패 + 네트워크 에러 다 처리됨)  

---

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

## 사용 예시 — 상품 목록을 서버에서 받아와서 화면에 보여준다 (JSX)

```jsx
import React, { useEffect, useState } from 'react';
import axios from 'axios';

function ProductList() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    // GET 요청 보내기
    axios.get('https://shopping.com/products')
      .then((res) => {
        setProducts(res.data); // 받아온 데이터를 상태에 저장
      })
      .catch((err) => {
        console.error('데이터 불러오기 실패:', err);
      });
  }, []);

  return (
    <ul>
      {products.map((p) => (
        <li key={p.id}>{p.name} - {p.price}원</li>
      ))}
    </ul>
  );
}
```

---

# React 컴포넌트가 서버에서 데이터 받아와 화면에 렌더링되는 과정

1단계: 컴포넌트가 렌더링됨  
ProductList라는 React 컴포넌트가 화면에 처음 나타나요.

---

2단계: `useEffect()` 실행됨  
이 훅(`useEffect`)은 컴포넌트가 처음 화면에 나타났을 때 한 번 실행되는 로직이에요.  
이 안에서 Axios로 HTTP GET 요청을 보냅니다.

---

3단계: Axios가 HTTP 요청을 보냄  
Axios는 이 코드를 실행하면서 아래처럼 생긴 HTTP 요청 메시지를 자동으로 만들어 서버로 보냅니다.

GET /products HTTP/1.1
Host: shopping.com

브라우저가 실제로 이 메시지를 서버로 날려요.

---

4단계: 서버가 응답을 보냄

서버는 products 목록을 JSON으로 돌려줍니다.

```json
[
  { "id": 1, "name": "셔츠", "price": 20000 },
  { "id": 2, "name": "청바지", "price": 30000 }
]
```

---

5단계: `res.data`에 데이터가 들어감

Axios는 서버 응답을 받고, 그 응답의 `data` 부분을 꺼내서 `setProducts()`에 넣습니다.

```js
setProducts(res.data);
```

이건 products라는 상태(state)를 바꿔주는 함수

---

6단계: 상태가 바뀌면 다시 렌더링됨

`products` 상태가 바뀌면 React는 컴포넌트를 자동으로 다시 렌더링합니다.  
즉, 화면에 상품 목록이 나타나는 거예요.
