---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - redux 도입하기"

tags:
  - Next.js
  - react
  - redux
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## redux intro

redux를 사용하기 위한 package를 설치한다.

```js
> npm i redux react-redux next-redux-wrapper
```

`reducers`라는 폴더를 만들고 해당 폴더에 다음과 같은 파일을 생성한다.k

```js
> mkdir reducers

index.js
post.js
user.js
```

`user.js` 부타 작성해보자.

`reducers/user.js`

```js
export const initialState = {
  isLoggedIn: false,
  me: null,
};

const dummyUser = {
  nickname: 'angelx',
  Post: [],
  Followings: [],
  Followers: []
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    default: {
      return state;
    }
  }
};

export default reducer;

```

초기 상태와 더미 데이터 그리고 `reducer`를 만들었다.

여기에 상태를 변경하기 위한 코드들이 추가될 것이다.

`post.js`도 비슷하게 작성한다.

`reducers/post.js`

```js
const initialState = {
  mainPosts: [
    {
      postId: 1,
      me: {
        id: 1,
        nickname: 'angelx'
      },
      content: 'Study hard more!',
      img: ''
    }
  ]
};

const dummyPost = {
    postId: 2,
    me: {
      id: 1,
      nickname: 'angelx'
    },
    content: 'Make money more',
    img: ''
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    default: {
      return state;
    }
  }
};

export default reducer;

```

`user.js`, `post.js`의 reducer를 통합하여 관리하기 위해 `index.js`를 작성한다.

`reducers/index.js`

```js
import { combineReducers } from 'redux';
import user from './user';
import post from './post';

const rootReducer = combineReducers({
  user,
  post
});

export default rootReducer;

```

마지막으로 `rootReducer`를 `Next.js`에 연결하기 위해 `pages/_app.js` 파일을 수정한다.

`pages/_app.js`

```js
import React from 'react';
import Head from 'next/head';
import PropTypes from 'prop-types';
import { applyMiddleware, compose, createStore } from 'redux';
import { Provider } from 'react-redux';
import withRedux from 'next-redux-wrapper';

import rootReducer from '../reducers';
import Layout from '../components/Layout';

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

export default withRedux((initialState, options) => {
  const middlewares = [];
  const enhancer = compose(
    applyMiddleware(...middlewares),
    !options.isServer &&
      typeof window.__REDUX_DEVTOOLS_EXTENSION__ !== 'undefined'
      ? window.__REDUX_DEVTOOLS_EXTENSION__()
      : (f) => f
  );
  const store = createStore(rootReducer, initialState, enhancer);
  return store;
})(CandC);

```

## Layout에 redux 적용해보기

`components/Layout.js` 파일에 로그인 상태에 따라 로그인 폼과 사용자 프로필이 출력되는 코드가 있다.

여기에 redux를 적용해보자.

```js
import React from 'react';
import Link from 'next/link';
import PropTypes from 'prop-types';
import { useSelector } from 'react-redux'
import { Menu, Input, Row, Col } from 'antd';

import LoginForm from './LoginForm';
import UserProfile from './UserProfile';

const dummy = {
  isLoggedIn: false
};

const Layout = ({ children }) => {
  const { isLoggedIn } = useSelector(state => state.user);
  return (
    <div>
      <Menu mode="horizontal">
        <Menu.Item key="home">
          <Link href="/index">
            <a>Home</a>
          </Link>
        </Menu.Item>
        <Menu.Item key="profile">
          <Link href="/profile">
            <a>Profile</a>
          </Link>
        </Menu.Item>
        <Menu.Item key="search">
          <Input.Search enterButton style={{ verticalAlign: 'middle' }} />
        </Menu.Item>
      </Menu>
      <Row gutter={10}>
        <Col xs={24} md={6}>
          {isLoggedIn ? <UserProfile /> : <LoginForm />}
        </Col>
        <Col xs={24} md={12}>
          {children}
        </Col>
        <Col xs={24} md={6}></Col>
      </Row>
    </div>
  );
};

Layout.propTypes = {
  children: PropTypes.node
};

export default Layout;

```

`react-redux`의 `useSelector`를 이용하여 redux의 state에 접근할 수 있다.

기존에는 `dummy`의 `isLoggedIn` 데이터를 사용했지만, 이번에는 redux의 state인 `isLoggedIn`을 사용한다.

`reducers/user.js`에 있는 `initialState`의 `isLoggedIn` 값에 따라 로그인, 로그아웃이 결정된다.

`dummy`는 삭제하자.

## UserProfile에 redux 적용

`reducers/user.js`의 `initialState`를 적절히 수정한다.

```js
export const initialState = {
  isLoggedIn: true,
  me: {
    nickname: 'angelx',
    myCafes: [{}, {}],
    Post: [],
    Followings: [],
    Followers: []
  }
};

```

`conponents/UserProfile.js` 파일을 수정해보자.

```js
import React from 'react';
import { useSelector } from 'react-redux';

import { Card, Avatar } from 'antd';

const UserProfile = () => {
  const { user } = useSelector(state => state.user);
  return (
    <Card
      actions={[
        <div key="my-cafe">
          My Cafe
          <br />
          {user.myCafes.length}
        </div>
      ]}
    >
      <Card.Meta
        avatar={<Avatar>{user.nickname[0]}</Avatar>}
        title={user.nickname}
      />
    </Card>
  );
};

export default UserProfile;

```

`useSelector`를 이용하여 redux의 state에 접근했다.

`pages/index.js`의 dummy도 redux를 이용하여 제거해보자.
