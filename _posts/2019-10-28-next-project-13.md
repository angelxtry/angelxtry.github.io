---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - 로그인에 redux-saga 활용"

tags:
  - Next.js
  - react
  - redux
  - redux-saga
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## login에 saga 활용

`sagas/user.js`에 작성한다.

```js
import { all, call } from 'redux-saga/effects';

function loginApi() {
}

function* login() {
}

function* watchLogin() {
}

function* userSaga() {
  yield all([fork(watchLogin)]);
}

export default userSaga;

```

로그인 같은 비동기 로직을 구현하려면 제네레이터 2개, 서버를 호출할 함수 하나가 필요하다.

상세 코드를 작성하자.

```js
import {
  all,
  fork,
  takeLatest,
  call,
  put,
  delay
} from 'redux-saga/effects';
import {
  LOG_IN_REQUEST,
  LOG_IN_FAILURE,
  LOG_IN_SUCCESS
} from '../reducers/user';

function loginAPI() {
  // 서버 api 호출
  return true;
}

function* login() {
  try {
    // yield call(loginAPI);
    yield delay(2000);
    yield put({ type: LOG_IN_SUCCESS });
  } catch (error) {
    console.error(error);
    yield put({ type: LOG_IN_FAILURE });
  }
}

function* watchLogin() {
  yield takeLatest(LOG_IN_REQUEST, login);
}

function* userSaga() {
  yield all([fork(watchLogin)]);
}

export default userSaga;

```

`watchLogin` 제네레이터의 `takeLatest` 함수는 계속 대기 상태에 있다가 `LOG_IN_REQUEST` 요청이 발생하면 동작한다.

`takeLatest` 함수는 2번째 인자가 호출할 함수다.

`login` 제네레이터는 `loginAPI`를 호출하고 그 결과에 따라 성공, 실패를 결정한다.

`put`은 redux에 action을 전달한다.

`loginAPI` 함수는 서버 API를 호출하는 역할을 한다.

현재 login 시도는 2초 후에 무조건 성공하도록 되어있다.

`call`과 `fork`는 모두 함수 호출에 사용한다.

차이점은 `call`은 동기, `fork`는 비동기다.

이제 `reducers/user.js` 파일을 작성하자.

```js
export const LOG_IN_REQUEST = 'LOG_IN_REQUEST';
export const LOG_IN_SUCCESS = 'LOG_IN_SUCCESS';
export const LOG_IN_FAILURE = 'LOG_IN_FAILURE';
export const LOG_OUT = 'LOG_OUT';

const initialState = {
  isLoggedIn: false,
  isLoggingIn: false,
  me: null,
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
    case LOG_IN_REQUEST: {
      return {
        ...state,
        isLoggedIn: false,
        isLoggingIn: true,
        me: null
      };
    }
    case LOG_IN_SUCCESS: {
      return {
        ...state,
        isLoggedIn: true,
        isLoggingIn: false,
        me: dummyUser
      };
    }
    case LOG_IN_FAILURE: {
      return {
        ...state,
        isLoggedIn: false,
        isLoggingIn: false,
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

대부분의 비동기 요청은 REQUEST, SUCCESS, FAILURE로 구분된다.

각 요청의 행동을 reducer에 넣어버렸다.

`initialState`에 `isLoggingIn`이라는 변수를 추가했다.

로그인 요청을 한 후 로그인 결과를 확인할 때까지의 구간을 의미한다.

`isLoggingIn`이 true일 경우 `Login` 버튼에 로딩 effect가 동작할 것이다.

마지막으로 `components/LoginForm.js`를 확인하자.

```js
import React, { useCallback } from 'react';
import Link from 'next/link';
import { useDispatch, useSelector } from 'react-redux';
import { Form, Input, Button } from 'antd';

import useInput from '../components/useInput';
import { LOG_IN_REQUEST } from '../reducers/user';

const LoginForm = () => {
  const [email, onChangeEmail] = useInput('');
  const [password, onChangePassword] = useInput('');

  const { isLoggingIn } = useSelector((state) => state.user);

  const dispatch = useDispatch();
  const onSubmit = useCallback((e) => {
    e.preventDefault();
    dispatch({
      type: LOG_IN_REQUEST
    });
  }, []);

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
        <Button type="primary" htmlType="submit" loading={isLoggingIn}>
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

`useSelector`로 `isLoggingIn`을 불러와서 `Login` 버튼의 loading 속성에 넣었다.

전체적인 로직의 순서를 다시 정리해보자.

`Login` 버튼을 클릭하면 `LOG_IN_REQUEST`가 호출되고, 대기중이던 saga의 `watchLogin`이 동작한다.

saga 내부에서 서버 api를 호출하고 그 응답 상태에따라 다시 redux의 reducer에 action(`LOG_IN_SUCCESS` or `LOG_IN_FAILURE`)을 전달한다.

그 결과 redux state가 변경되고, 그에 맞게 화면이 다시 rendering 된다.

추가로 가운데가 `signup` 화면일 때 로그인이되면 자동으로 `Home`으로 넘어가는 것이 좋다.

`pages/signup.js`에 다음의 코드를 추가한다.

```js
const { isLoggedIn } = useSelector((state) => state.user);
  useEffect(() => {
    if (isLoggedIn) {
      Router.push('/');
    }
  }, [isLoggedIn]);

```

`Router`는 `next/router`를 import하면 사용할 수 있다.
