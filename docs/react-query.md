# React Query 개념 정리

## React Query란?

비동기 데이터 처리(예: 서버에서 상품 목록 받아오기)를 간단하고 효율적으로 관리할 수 있게 해주는 라이브러리

---

## 비동기(Asynchronous)란?

나중에 결과가 나오는 작업
HTTP 요청 또한 비동기 처리

예를 들어:

```js
const data = fetch("https://example.com/products");
console.log(data);
```

여기서 fetch는 서버에 요청을 보내서 데이터를 받아오는 작업이에요.
그런데 서버는 1초, 2초… 심지어 더 오래 걸릴 수도 있죠.
그렇다고 자바스크립트가 서버 응답을 기다리면 전체 프로그램이 멈춰버려요.
그래서 서버에 요청을 보낸 후 데이터가 오는 동안 기다리지 않고 다음 코드를 실행을 하는데
이것을 **비동기 처리**라고 합니다.

---

## React Query가 필요한 이유

React에서 데이터를 서버(API)에서 가져오려면?

- `fetch()` 또는 `axios`로 직접 요청하고
- `useState`, `useEffect`로 상태 관리

#### ❗하지만 이 방식은?

- 로딩 상태, 에러 상태를 따로 관리해야 함
- 캐싱(같은 데이터를 여러 번 요청하지 않기) 안 됨
- 재요청(리패치), 자동 갱신 등 부가기능 구현이 번거로움

이런 불편함을 해결해주는 것이 바로 **React Query**

---

## React Query 핵심 개념

### 1. Query

서버에서 데이터를 가져오는 요청
상품 목록 가져오기 같은 GET 요청에 해당

### 2. Mutation

서버에 데이터를 보내는 요청
예: 장바구니에 상품 추가, 주문하기 등 POST/PUT/DELETE 요청

### 3. 캐싱 (Caching)

가져온 데이터를 메모리에 저장해 재사용
같은 데이터를 또 요청하지 않아도 돼서 빠르고 효율적

### 4. 자동 새로고침 (Refetching)

페이지가 다시 열리거나, 시간이 지나면 자동으로 최신 데이터를 가져옴

## React Query의 장점 정리

| 기능                 | React Query의 자동 처리         |
| -------------------- | ------------------------------- |
| 로딩/에러 상태 관리  | `isLoading`, `isError` 등 제공  |
| 데이터 캐싱          | 기본으로 지원                   |
| 리패칭 (재요청)      | 탭 포커스 시 자동 갱신 등       |
| 수동/자동 새로고침   | `refetch()` 혹은 자동 설정 가능 |
| 병렬 요청, 의존 요청 | `useQuery` 여러 개 조합 가능    |

---

## 쇼핑몰 프로젝트에서 쓸 수 있는 예시

- 상품 목록 조회 → `useQuery`
- 상품 상세 조회 → `useQuery(['product', id])`
- 장바구니 담기 → `useMutation`
- 주문하기 → `useMutation`
- 주문 내역 불러오기 → `useQuery`

---

## 사용 예시

### React Query 설정 (QueryClientProvider 사용)

```js
// App.jsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query"; // React Query에서 클라이언트와 Provider를 가져옴

// QueryClient: React Query의 핵심 인스턴스, 캐시 저장소 역할을 함
const queryClient = new QueryClient();

function App() {
  return (
    // QueryClientProvider: React Query가 전체 앱에서 작동할 수 있게 해주는 컨테이너
    <QueryClientProvider client={queryClient}>
      {/* 이 안에서 React Query 관련 훅을 자유롭게 사용할 수 있음 */}
      <MyProductList />
    </QueryClientProvider>
  );
}
```

1. QueryClient 생성 → 데이터 캐싱 및 상태 관리의 중심
2. QueryClientProvider로 앱을 감쌈 → 자식 컴포넌트 어디서든 React Query 사용 가능
3. 이제 MyProductList 안에서 useQuery()를 자유롭게 사용 가능

### 데이터 가져오기 (useQuery 사용)

```js
// MyProductList.jsx
import { useQuery } from "@tanstack/react-query"; // React Query에서 useQuery 훅을 가져옴
import axios from "axios"; // HTTP 요청을 위한 axios 라이브러리 사용

//1. 서버에서 상품 데이터를 받아오는 함수 정의
function fetchProducts() {
  // axios.get()은 비동기로 작동하고 Promise를 반환함
  return axios.get("https://api.example.com/products");
}

//2. 컴포넌트 정의
function MyProductList() {
  //3. useQuery로 데이터 요청
  const { data, isLoading, isError } = useQuery({
    queryKey: ["products"], // 이 쿼리의 고유 이름 (캐시 키 역할)
    queryFn: fetchProducts, // 데이터를 실제로 불러오는 함수
  });

  //4. 로딩 중일 때
  if (isLoading) return <p>로딩 중...</p>;

  //5. 에러가 났을 때
  if (isError) return <p>에러 발생!</p>;

  //6. 데이터가 준비되면 화면에 출력
  return (
    <ul>
      {/* data는 axios 응답 객체이므로, 실제 데이터는 data.data에 있음 */}
      {data.data.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

### 작동 흐름 요약

#### 1단계: 컴포넌트가 렌더링됨

MyProductList라는 React 컴포넌트가 화면에 처음 나타나요.

#### 2단계: useQuery()가 실행됨

React Query의 useQuery() 훅이 호출되면서 데이터를 불러오는 함수인 fetchProducts()를 실행해요.

#### 3단계: Axios가 HTTP 요청을 보냄

fetchProducts() 함수 안에서 axios.get('https://api.example.com/products') 코드가 실행되어 서버로 아래와 같은 요청을 보냅니다.

```
GET /products HTTP/1.1
Host: api.example.com
```

브라우저가 이 요청을 서버에 전달해요.

#### 4단계: 서버가 응답을 보냄

서버는 상품 목록 데이터를 JSON 형식으로 응답해 줍니다.

```
[
{ "id": 1, "name": "셔츠", "price": 20000 },
{ "id": 2, "name": "청바지", "price": 30000 }
]
```

#### 5단계: React Query가 데이터를 받아 캐시에 저장함

서버 응답이 오면 React Query는 자동으로 데이터를 받아서 내부 캐시에 저장하고, isLoading 상태를 false로 바꿔줘요.

#### 6단계: 컴포넌트가 받은 데이터를 화면에 렌더링함

useQuery() 훅에서 받은 데이터를 data 변수로 전달받고, 이를 사용해 상품 목록을 화면에 보여줍니다.

#### 7단계: 에러가 나면 에러 상태를 보여줌

만약 요청 중에 문제가 생기면 React Query가 자동으로 isError를 true로 바꿔주고, 화면에 에러 메시지를 표시해 줍니다.

#### 8단계: 동일 쿼리키 재요청 시 캐시 활용으로 빠르게 동작함

queryKey가 같은 쿼리가 다시 호출되면 React Query는 캐시에 저장된 데이터를 바로 보여주고, 백그라운드에서 최신 데이터로 업데이트를 시도해 사용자 경험이 빨라집니다.

#### 9단계: 전체 흐름 자동 관리

React Query는 이 모든 과정을 자동으로 처리해주기 때문에 개발자는 단순히 데이터를 요청하고 화면에 표시하는 코드만 작성하면 됩니다.

---

## 쇼핑몰 프로젝트에서 알아두면 좋은 React Query 기능

- `useQuery` — 상품 목록, 상품 상세, 리뷰 가져오기 등
- `useMutation` — 상품 구매하기, 리뷰 작성, 회원가입 등
- `invalidateQueries` — 주문 완료 후 목록 갱신
- `queryKey` 잘 나누기 — 상품 ID별 쿼리 분리
- `onSuccess`, `onError` 등 — 요청 성공/실패 후 알림 처리
