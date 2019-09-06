---
layout: post
comments: true
title: "[React] React Hooks, axios를 활용하여 data fetch #2"
tags:
  - react
  - hooks
  - axios
  - fetch
---

오늘도 hooks & axios를 진행한다.

이전 글

[[React] React Hooks, axios를 활용하여 data fetch #1"](https://angelxtry.github.io/React-Hooks-Axios-Fetch-01/)

## 4. 로딩 상태 표시하기

```js
import React, { Fragment, useState, useEffect } from "react";
import axios from "axios";

const HackerNews = () => {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState("");
  const [url, setUrl] = useState(
    `https://hn.algolia.com/api/v1/search?query="redux"`
  );
  const [isLoading, setIsLoading] = useState(false);

  useEffect(() => {
    const fetchData = async () => {
      setIsLoading(true);
      const result = await axios(url);
      console.log(result);
      setData(result.data);
      setIsLoading(false);
    };
    fetchData();
  }, [url]);

  return (
    <Fragment>
      <input
        type="text"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <button
        type="button"
        onClick={() =>
          setUrl(`https://hn.algolia.com/api/v1/search?query=${query}`)
        }
      >
        Search
      </button>
      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
};

export default HackerNews;
```

useState를 이용하여 isLoading을 추가했다.

useEffect에서 GET request를 보내기 전에 isLoading을 true로, response를 받으면 false로 변경한다.

그리고 jsx 코드에서 isLoading을 이용하여 출력을 결정한다.

이제 검색어를 입력하고 버튼을 클릭하면 잠깐동안 `Loading...`이라고 출력되는 것을 볼 수 있다.

## 5. 에러 처리

```js
import React, { Fragment, useState, useEffect } from "react";
import axios from "axios";

const HackerNews = () => {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState("");
  const [url, setUrl] = useState(
    `https://hn.algolia.com/api/v1/sea?query="redux"`
  );
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);

  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);

      try {
        const result = await axios(url);
        console.log(result);
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }
      setIsLoading(false);
    };
    fetchData();
  }, [url]);

  return (
    <Fragment>
      <input
        type="text"
        value={query}
        onChange={event => setQuery(event.target.value)}
      />
      <button
        type="button"
        onClick={() =>
          setUrl(`https://hn.algolia.com/api/v1/search?query=${query}`)
        }
      >
        Search
      </button>
      {isError && <div>Something wrong...</div>}
      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
};

export default HackerNews;
```

useState를 이용하여 isError를 추가하고, useEffect에서 setIsError를 사용했다.

GET request를 보내기 전까지는 isError가 false인 상태다.

try catch로 axios 코드를 감싸고 error가 발생할 경우 isError를 true로 변경한다.

그리고 jsx코드에 에러 처리 항목을 추가한다.

에러를 발생시키기 위해 url의 초기값을 에러가 발생하도록 수정했다.

이제 다시 브라우저를 실행해보면 Loading... 에서 Something wrong...으로 변경되는 것을 볼 수 있다.

input에 검색어를 입력하고 버튼을 클릭하면 Something wrong... 이 사라지고 정상적으로 데이터가 출력된다.

useEffect에 에러 처리를 넣은 것은 이렇게 다시 정상적인 결과를 받았을 경우 에러 상태를 벗어날 수 있게 하기 위해서다.

## 6. form 활용

```js
import React, { Fragment, useState, useEffect } from "react";
import axios from "axios";

const HackerNews = () => {
  const [data, setData] = useState({ hits: [] });
  const [query, setQuery] = useState("");
  const [url, setUrl] = useState(
    `https://hn.algolia.com/api/v1/search?query="redux"`
  );
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);

  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);

      try {
        const result = await axios(url);
        console.log(result);
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }
      setIsLoading(false);
    };
    fetchData();
  }, [url]);

  return (
    <Fragment>
      <form
        onSubmit={event => {
          setUrl(`https://hn.algolia.com/api/v1/search?query=${query}`);
          event.preventDefault();
        }}
      >
        <input
          type="text"
          value={query}
          onChange={event => setQuery(event.target.value)}
        />
        <button type="submit">Search</button>
      </form>
      {isError && <div>Something wrong...</div>}
      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
};

export default HackerNews;
```

input과 button만을 이용했던 것을 form으로 변경했다.

imput과 button을 form tag로 감싸고 button에 있던 setUrl을 form의 onSubmit 이벤트로 옮겼다.

button을 클릭했을 때 화면 전체가 re-render되지 않도록 onSubmit 이벤트에 event.preventDefault()를 설정했다.

## 7. Data fetch 코드를 custom hook으로 변경

기존의 코드에서 data fetch 부분을 분리한다.

```js
import { useState, useEffect } from "react";
import axios from "axios";

const useHackerNewsApi = () => {
  const [data, setData] = useState({ hits: [] });
  const [url, setUrl] = useState(
    `https://hn.algolia.com/api/v1/search?query="redux"`
  );
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);

  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);

      try {
        const result = await axios(url);
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }

      setIsLoading(false);
    };
    fetchData();
  }, [url]);

  return [{ data, isLoading, isError }, setUrl];
};

export default useHackerNewsApi;
```

```js
import React, { Fragment, useState } from "react";

import useHackerNewsApi from './useHackerNewsApi';

const HackerNews = () => {
  const [query, setQuery] = useState("");
  const [{ data, isLoading, isError }, doFetch] = useHackerNewsApi();

  return (
    <Fragment>
      <form
        onSubmit={event => {
          doFetch(`https://hn.algolia.com/api/v1/search?query=${query}`);
          event.preventDefault();
        }}
      >
        <input
          type="text"
          value={query}
          onChange={event => setQuery(event.target.value)}
        />
        <button type="submit">Search</button>
      </form>
      {isError && <div>Something wrong...</div>}
      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
};

export default HackerNews;
```

화면을 그리는 부분과 data fetch 로직을 분리했다.

이 부분이 매우 인상적이었다.

custom hook이 이렇게 만들어지는구나 하는 느낌이 생겼다.

앞으로 코드를 작성할 때 최대한 로직과 뷰를 분리할 수 있도록 생각하면서 작성해야겠다는 생각이 들었다.

`useHackerNewsApi`를 좀 더 범용적으로 변경해보자.

```js
import { useState, useEffect } from "react";
import axios from "axios";

const useDataApi = (initialUrl, initialData) => {
  const [data, setData] = useState(initialData);
  const [url, setUrl] = useState(initialUrl);
  const [isLoading, setIsLoading] = useState(false);
  const [isError, setIsError] = useState(false);

  useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);

      try {
        const result = await axios(url);
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }

      setIsLoading(false);
    };
    fetchData();
  }, [url]);

  return [{ data, isLoading, isError }, setUrl];
};

export default useDataApi;
```

data와 url에서 사용하던 초기값을 hook의 param으로 변경했다.

이것만을 수정한 것으로도 이전과 다르게 다른 프로젝트에서 활용할 만한 코드가 됐다.

`useDataApi`를 사용하는 코드는 다음과 같다.

```js
import React, { Fragment, useState } from "react";

import useHackerNewsApi from "./useDataApi";

const HackerNews = () => {
  const [query, setQuery] = useState("");
  const [{ data, isLoading, isError }, doFetch] = useHackerNewsApi(
    `https://hn.algolia.com/api/v1/search?query="redux"`,
    { hits: [] }
  );

  return (
    <Fragment>
      <form
        onSubmit={event => {
          doFetch(`https://hn.algolia.com/api/v1/search?query=${query}`);
          event.preventDefault();
        }}
      >
        <input
          type="text"
          value={query}
          onChange={event => setQuery(event.target.value)}
        />
        <button type="submit">Search</button>
      </form>
      {isError && <div>Something wrong...</div>}
      {isLoading ? (
        <div>Loading...</div>
      ) : (
        <ul>
          {data.hits.map(item => (
            <li key={item.objectID}>
              <a href={item.url}>{item.title}</a>
            </li>
          ))}
        </ul>
      )}
    </Fragment>
  );
};

export default HackerNews;
```

(내일에 이어서...)
