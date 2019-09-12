---
layout: post
comments: true
title: "[React] React Hooks, axios를 활용하여 data fetch #3"
tags:
  - react
  - hooks
  - axios
  - fetch
---

hooks & axios 세번째, 이번 글의 마지막!

이전 글

[[React] React Hooks, axios를 활용하여 data fetch #1](https://angelxtry.github.io/React-Hooks-Axios-Fetch-01/)

[[React] React Hooks, axios를 활용하여 data fetch #2](https://angelxtry.github.io/React-Hooks-Axios-Fetch-02/)

## 8. useDataApi custom hook에 Reducer hook 끼얹기

data를 fetch하는 동안 data, loading 그리고 error 같은 많은 state를 관리하게 된다.

이 state들은 같은 관심사를 가진다. 따라서 모아서 같이 관리하는 것이 더 효과적이다.

Reducer hook를 이용하여 이 세가지를 묶어서 관리하도록 해보자.

Reducer hook은 state 객체와 state를 변경하는 함수를 반환한다.

이 함수를 dispatch라고 부른다. 이 함수는 type과 payload(optional)를 가진 action 객체를 이용하여 state를 변경한다.

Reducer hook을 사용해보자.

```js
import { useState, useEffect, useReducer } from "react";

const dataFetchReducer = (state, action) => {
  (일단 생략)
};

const [state, dispatch] = useReducer(dataFetchReducer, {
  isLoading: false,
  isError: false,
  data: initialData
});
```

`useReducer`는 첫 번째 param으로 dataFetchReducer라는 함수를 가진다. 이 함수를 reducer function이라고 한다.

두 번째 param은 initial state다.

```js
const dataFetchReducer = (state, action) => {
  switch (action.type) {
    case "FETCH_INIT":
      return {
        ...state,
        isLoading: true,
        isError: false
      };
    case "FETCH_SUCCESS":
      return {
        ...state,
        isLoading: false,
        isError: false,
        data: action.payload
      };
    case "FETCH_FAILURE":
      return {
        ...state,
        isLoading: false,
        isError: true
      };
    default:
      return new Error();
  }
}
```

reducer function인 dataFetchReducer는 위와 같이 생겼다.

switch case로 각 state를 정의하고 그에 맞는 객체를 반환한다.

data는 action.payload에 담아서 state에 전달한다.

이렇게 정의된 Reducer는 useEffect에서 사용한다.

```js
useEffect(() => {
  const fetchData = async () => {
    dispatch({ type: "FETCH_INIT" });

    try {
      const result = await axios(url);
      dispatch({ type: "FETCH_SUCCESS", payload: result.data });
    } catch (error) {
      dispatch({ type: "FETCH_FAILURE" });
    }
  };
  fetchData();
}, [url]);
```

useEffect에서 loading, error, data를 처리하는 코드를 모두 reducer로 처리했다.

useState를 이용하여 loading, error, data를 관리할 필요가 없어졌다.

마지막으로 return도 수정한다.

```js
return [state, setUrl];
```

return하는 state는 reducer의 state다. 이 state가 이전에 사용했던 객체를 대체한다.

참고로 변경전 코드는 다음과 같다.

```js
return [{ isLoading, isError, data }, setUrl];
```

전체 코드는 다음과 같다.

```js
import { useState, useEffect, useReducer } from "react";
import axios from "axios";

const dataFetchReducer = (state, action) => {
  switch (action.type) {
    case "FETCH_INIT":
      return {
        ...state,
        isLoading: true,
        isError: false
      };
    case "FETCH_SUCCESS":
      return {
        ...state,
        isLoading: false,
        isError: false,
        data: action.payload
      };
    case "FETCH_FAILURE":
      return {
        ...state,
        isLoading: false,
        isError: true
      };
    default:
      throw new Error();
  }
};

const useDataApi = (initialUrl, initialData) => {
  const [url, setUrl] = useState(initialUrl);

  const [state, dispatch] = useReducer(dataFetchReducer, {
    isLoading: false,
    isError: false,
    data: initialData
  });

  useEffect(() => {
    const fetchData = async () => {
      dispatch({ type: "FETCH_INIT" });

      try {
        const result = await axios(url);
        dispatch({ type: "FETCH_SUCCESS", payload: result.data });
      } catch (error) {
        dispatch({ type: "FETCH_FAILURE" });
      }
    };
    fetchData();
  }, [url]);

  return [state, setUrl];
};

export default useDataApi;

```

관심사를 하나로 모아서 관리하게 되어 코드가 간단해졌고, 실수할 가능성이 적어졌다.

## 9. Effect hook을 이용하여 data fetching 취소하기

Effect hook은 component가 unmount 될 때 동작하는 clean up function을 가질 수 있다.

clean up function은 return으로 구현한다.

```js
const useDataApi = (initialUrl, initialData) => {
  const [url, setUrl] = useState(initialUrl);

  const [state, dispatch] = useReducer(dataFetchReducer, {
    isLoading: false,
    isError: false,
    data: initialData
  });

  useEffect(() => {
    let didCancel = false;
    const fetchData = async () => {
      dispatch({ type: "FETCH_INIT" });

      try {
        const result = await axios(url);
        if (!didCancel) {
          dispatch({ type: "FETCH_SUCCESS", payload: result.data });
        }
      } catch (error) {
        if (!didCancel) {
          dispatch({ type: "FETCH_FAILURE" });
        }
      }
    };
    fetchData();
    return () => {
      didCancel = true;
    };
  }, [url]);

  return [state, setUrl];
};

export default useDataApi;
```

didCancel이라는 변수를 만들어 true일 경우 fetch 로직이 실행되지 않도록 수정했다.

사실 실행 중인 fetch로직이 취소되는 것은 아니다. 단지 didCancel을 이용하여 앞으로 fetch로직이 실행되지 않도록 처리하는 것이다.

---

useState, useEfftct, useReducer를 이용하여 data fetching 하는 과정을 진행해봤다.

view와 fetch 로직을 분리하는 것, 분리한 fetch 로직을 좀 더 범용적으로 만드는 것, reducer를 이용하여 연관된 state를 하나로 모으는 것이 재미있었다.

POST request를 처리하는 과정이 없어서 조금 아쉽다. 조금만 수정하면 만들어 볼 수 있을 것 같다.

다음 포스팅은 POST request를 만들어보는 과정이 되었으면 좋겠다.

(POST request를 처리하는 custom hook 만들기는 실패 ㅠㅠ)
