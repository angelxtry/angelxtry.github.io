---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - redux action 사용(Login, Logout)"

tags:
  - Next.js
  - react
  - redux
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## redux를 사용하여 Login, Logout 구현

redux의 state를 사용해봤으니 action을 사용해보자.

아직 서버가 없으니 더미 데이터로 구현해보자.

일단 로그인, 로그아웃을 구현하기 위해 `reducers/user.js`를 수정한다.

```js
export const LOG_IN_REUQEST = 'LOG_IN_REQUEST';
export const LOG_IN_SUCCESS = 'LOG_IN_SUCCESS';
export const LOG_IN_FAILURE = 'LOG_IN_FAILURE';
export const LOG_OUT = 'LOG_OUT';

const initialState = {
  isLoggedIn: false,
  me: null
};

const dummyUser = {
  nickname: 'dummy user',
  myCafes: [{}, {}],
  Post: [],
  Followings: [],
  Followers: []
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case LOG_IN_REUQEST: {
      return {
        ...state,
        isLoggedIn: false,
        me: null
      };
    }
    case LOG_IN_SUCCESS: {
      return {
        ...state,
        isLoggedIn: true,
        me: dummyUser
      };
    }
    case LOG_IN_FAILURE: {
      return {
        ...state,
        isLoggedIn: false,
        me: null
      };
    }
    case LOG_OUT: {
      return {
        ...state,
        isLoggedIn: false,
        me: null
      };
    }
    default: {
      return state;
    }
  }
};

export default reducer;

```

`components/LoginForm.js`

```js
import React, { useCallback } from 'react';
import Link from 'next/link';
import { useDispatch } from 'react-redux';
import { Form, Input, Button } from 'antd';

import useInput from '../components/useInput';
import { LOG_IN_SUCCESS } from '../reducers/user';

const LoginForm = () => {
  const [email, onChangeEmail] = useInput('');
  const [password, onChangePassword] = useInput('');

  const dispatch = useDispatch();
  const onSubmit = useCallback(
    (e) => {
      e.preventDefault();
      dispatch({
        type: LOG_IN_SUCCESS
      });
      console.log({ email, password });
    },
    [email, password]
  );

  return (
    <Form onSubmit={onSubmit} style={{ padding: '10px' }}>
      <div>
        <label htmlFor="user-email">Email</label>
        <Input
          name="user-email"
          required
          value={email}
          onChange={onChangeEmail}
        />
      </div>
      <div>
        <label htmlFor="user-password">Password</label>
        <Input
          name="user-password"
          type="password"
          required
          value={password}
          onChange={onChangePassword}
        />
      </div>
      <div style={{ marginTop: '10px' }}>
        <Button type="primary" htmlType="submit" loading={false}>
          Login
        </Button>
        <Link href="/signup">
          <Button>Signup</Button>
        </Link>
      </div>
    </Form>
  );
};

export default LoginForm;

```

action을 redux에 전달하기 위해 `react-redux`의 `useDispatch`를 사용한다.

이메일과 암호를 입력하는 폼에 적당한 데이터를 넣고 `Login` 버튼을 클릭하면 reducer에 정해 놓은 action에 따라 동작한다.

서버가 운영 중이라면 `Login` 버튼을 클릭했을 때 `LOG_IN_REQUEST` action을 전달하고, 서버에서 전달받은 결과에 따라 `LOG_IN_SUCCESS`, `LOG_IN_FAILURE`가 결정된다.

아직 서버가 없기 때문에 `Login` 버튼을 클릭하면 바로 `LOG_IN_SUCCESS`가 전달된다.

로그아웃은 `components/UserProfile.js`에 구현한다.

```js
import React, { useCallback } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { Card, Avatar, Button } from 'antd';

import { LOG_OUT } from '../reducers/user';

const UserProfile = () => {
  const dispatch = useDispatch();
  const onLogout = useCallback(() => {
    dispatch({
      type: LOG_OUT
    })
  }, []);

  const { me } = useSelector((state) => state.user);

  return (
    <Card
      actions={[
        <div key="my-cafe">
          My Cafe
          <br />
          {me.myCafes.length}
        </div>
      ]}
    >
      <Card.Meta
        avatar={<Avatar>{me.nickname[0]}</Avatar>}
        title={me.nickname}
      />
      <Button onClick={onLogout}>Logout</Button>
    </Card>
  );
};

export default UserProfile;

```

동일한 방식으로 구현했다.
