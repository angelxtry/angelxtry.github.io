---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - redux-saga 설치 및 연결"

tags:
  - Next.js
  - react
  - redux
  - redux-saga
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## redux-saga 설치

먼저 `redux-saga` package를 설치하고 기본 설정을 한다.

```js
npm i redux-saga
```

`sagas` 폴더를 생성하고 다음의 파일을 생성한다.

```js
index.js
user.js
post.js
```

redux-saga는 redux의 reducers와 구조가 비슷하다.

user, post 각각이 saga를 가지고 index에서 합쳐준다.

`sagas/user.js`

```js
import { all } from 'redux-saga/effects';

function* userSaga() {
  yield all([]);
}

export default userSaga;

```

`postSaga`도 동일한 구조다.

```js
import { all } from 'redux-saga/effects';

function* postSaga() {
  yield all([]);
}

export default postSaga;

```

`sagas/index.js`에서 두 saga를 합쳐준다.

```js
import { all, call } from 'redux-saga/effects';

import user from './user';
import post from './post';

function* rootSaga() {
  yield all([call(user), call(post)]);
}

export default rootSaga;

```

## saga를 Next.js에 연결

이것 역시 redux의 reducer 처럼 `pages/_app.js`에 연결한다.

```js
import React from 'react';
import Head from 'next/head';
import PropTypes from 'prop-types';
import { applyMiddleware, compose, createStore } from 'redux';
import { Provider } from 'react-redux';
import withRedux from 'next-redux-wrapper';
import createSagaMiddleware from 'redux-saga';

import Layout from '../components/Layout';
import rootReducer from '../reducers';
import rootSaga from '../sagas';

const CandC = ({ Component, store }) => {
  return (
    <Provider store={store}>
      <Head>
        <title>C And C</title>
        <link
          rel="stylesheet"
          href="https://cdnjs.cloudflare.com/ajax/libs/antd/3.23.6/antd.min.css"
        />
      </Head>
      <Layout>
        <Component />
      </Layout>
    </Provider>
  );
};

CandC.propTypes = {
  Component: PropTypes.elementType
};

const configureStore = (initialState, options) => {
  const sagaMiddleware = createSagaMiddleware();
  const middlewares = [sagaMiddleware];
  const enhancer =
    process.env.NODE_ENV === 'production'
      ? compose(applyMiddleware(...middlewares))
      : compose(
          applyMiddleware(...middlewares),
          !options.isServer &&
            typeof window.__REDUX_DEVTOOLS_EXTENSION__ !== 'undefined'
            ? window.__REDUX_DEVTOOLS_EXTENSION__()
            : (f) => f
        );
  const store = createStore(rootReducer, initialState, enhancer);
  sagaMiddleware.run(rootSaga);
  return store;
};

export default withRedux(configureStore)(CandC);

```
