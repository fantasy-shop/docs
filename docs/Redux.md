# 🛠️ 언제 Redux를 쓰는 게 가장 좋을까?

Redux는 **많은 컴포넌트가 공통된 상태를 공유하거나 바꿔야 할 때** 가장 효과적입니다.  
상태가 **복잡하거나 깊이 중첩되어 있고**, 여러 컴포넌트에서 동시에 접근해야 할 경우 특히 유용합니다.

---

## ✅ Redux 사용이 적합한 상황

| 상황                      | 이유                                             |
| ------------------------- | ------------------------------------------------ |
| 로그인/로그아웃 상태 공유 | 헤더, 사이드바, 여러 페이지에서 로그인 상태 필요 |
| 장바구니(cart) 기능       | 다양한 페이지에서 장바구니 상태 추가/수정/삭제   |
| 관리자 대시보드           | 필터, 검색, 선택, 테이블 등 다양한 상태 처리     |
| 비동기 요청 처리          | 로딩, 성공, 에러 상태를 일관되게 관리해야 할 때  |
| SPA에서 탭 간 데이터 공유 | 채팅, 예약, 폼 작성 등 상태 유지 필요 시         |

---

## 🚀 Redux Toolkit(RTK)을 사용하는 이유

Redux Toolkit(RTK)은 Redux를 더 쉽고 간편하게 사용하도록 도와주는 **Redux 공식 도구**입니다.  
기존 Redux의 보일러플레이트 문제(액션 타입, 리듀서 분리 등)를 해결하고, **더 빠르고 안전하게 상태를 관리**할 수 있게 도와줍니다.

| 구성 요소        | 역할                                                        |
| ---------------- | ----------------------------------------------------------- |
| `createSlice`    | 상태(state) + 액션(actions) + 리듀서(reducer)를 하나로 생성 |
| `configureStore` | 여러 슬라이스를 통합해 전역 상태 관리                       |

---

## 🧩 Redux Toolkit 관련 훅

| 훅 이름       | 역할                                             |
| ------------- | ------------------------------------------------ |
| `useSelector` | Redux에 저장된 **상태(state)**를 조회            |
| `useDispatch` | Redux에 **액션(action)**을 보냄 (상태 변경 요청) |

---

## 🔄 `createAsyncThunk`란?

Redux Toolkit에서 **비동기 API 요청**을 쉽게 처리하게 도와주는 비동기 액션 생성기입니다.  
예: 로그인 요청, 상품 목록 가져오기, 댓글 저장 등

| 개념                         | 설명                                 |
| ---------------------------- | ------------------------------------ |
| `createAsyncThunk`           | 비동기 액션 생성기 (fetch, axios 등) |
| `pending`                    | 요청 시작됨 → 로딩 중 표시           |
| `fulfilled`                  | 요청 성공 → 서버 데이터 저장         |
| `rejected`                   | 요청 실패 → 에러 메시지 저장         |
| `thunkAPI.rejectWithValue()` | 에러 메시지를 직접 전달할 때 사용    |

---

## 🔁 Redux Toolkit 사용 흐름

```
[1] Slice 생성
   → 상태, 액션 정의
        ↓
[2] Store에 등록
   → configureStore로 슬라이스 통합
        ↓
[3] Provider로 앱에 연결
   → 컴포넌트에서 상태 사용 가능
        ↓
[4] 컴포넌트에서
   → useSelector()로 상태 조회
   → useDispatch()로 액션 실행
```

---

## 🧪 예시: 장바구니(cart) 기능

### 0. `cartSlice.js`

```js
import { createSlice } from "@reduxjs/toolkit";

const initialState = {
  items: [], // { id, name, price, quantity }
};

const cartSlice = createSlice({
  name: "cart",
  initialState,
  reducers: {
    addToCart: (state, action) => {
      const item = action.payload;
      const existing = state.items.find((i) => i.id === item.id);
      if (existing) {
        existing.quantity += item.quantity;
      } else {
        state.items.push(item);
      }
    },
    removeFromCart: (state, action) => {
      state.items = state.items.filter((item) => item.id !== action.payload);
    },
    clearCart: (state) => {
      state.items = [];
    },
  },
});

export const { addToCart, removeFromCart, clearCart } = cartSlice.actions;
export default cartSlice.reducer;
```

---

### 1. `store.js`

```js
import { configureStore } from "@reduxjs/toolkit";
import cartReducer from "./cartSlice";

const store = configureStore({
  reducer: {
    cart: cartReducer,
  },
});

export default store;
```

---

### 2. Provider로 앱에 스토어 연결

```js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import { Provider } from "react-redux";
import store from "./store";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

---

### 3. 상품 페이지: `ProductPage.js`

```jsx
import { useDispatch } from "react-redux";
import { addToCart } from "./cartSlice";

const ProductPage = ({ product }) => {
  const dispatch = useDispatch();

  const handleAdd = () => {
    dispatch(addToCart({ ...product, quantity: 1 }));
  };

  return <button onClick={handleAdd}>장바구니에 담기</button>;
};
```

---

### 4. 헤더 아이콘: `CartIcon.js`

```jsx
import { useSelector } from "react-redux";

const CartIcon = () => {
  const itemCount = useSelector((state) =>
    state.cart.items.reduce((total, item) => total + item.quantity, 0)
  );

  return <div>🛒 {itemCount}</div>;
};
```

---

### 5. 장바구니 페이지: `CartPage.js`

```jsx
import { useSelector, useDispatch } from "react-redux";
import { removeFromCart, clearCart } from "./cartSlice";

const CartPage = () => {
  const items = useSelector((state) => state.cart.items);
  const dispatch = useDispatch();

  return (
    <div>
      {items.map((item) => (
        <div key={item.id}>
          {item.name} - {item.quantity}개
          <button onClick={() => dispatch(removeFromCart(item.id))}>
            삭제
          </button>
        </div>
      ))}
      <button onClick={() => dispatch(clearCart())}>장바구니 비우기</button>
    </div>
  );
};
```

---

## 🧠 요약

- Redux는 **여러 컴포넌트 간 상태를 공유하거나, 복잡한 상태를 효율적으로 관리할 때 유리**
- Redux Toolkit(RTK)은 Redux를 **더 쉽게, 빠르게, 안전하게 사용할 수 있게 해주는 도구**
- `createSlice`로 상태와 액션을 통합 관리하고, `createAsyncThunk`로 비동기 요청을 처리
- 단순한 앱이라면 `useState`, `useContext`로도 충분할 수 있음
