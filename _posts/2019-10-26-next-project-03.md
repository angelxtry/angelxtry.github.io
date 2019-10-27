---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - useInput custom hook 사용"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## useInput - custom hook 사용

`Input`을 위해 반복되는 `useState`를 custom hook을 만들어 코드를 줄일 수 있다.

`components/useInput.js` 파일을 다음과 같이 작성하자.

```js
import { useState, useCallback } from 'react';

const useInput = (initValue = null) => {
  const [value, setter] = useState(initValue);
  const handler = useCallback((e) => {
    setter(e.target.value);
  }, []);
  return [value, handler];
};

export default useInput;

```

`pages/signup.js`에 적용해보자.

```js
import React, { useState, useCallback } from 'react';
import Head from 'next/head';
import { Form, Input, Button, Checkbox } from 'antd';

import Layout from '../components/Layout';
import useInput from '../components/useInput';

const signup = () => {
  const [email, onChangeEmail] = useInput('');
  const [nickname, onChangeNickname] = useInput('');
  const [password, onChangePassword] = useInput('');
  const [passwordCheck, setPasswordCheck] = useState('');
  const [term, setTerm] = useState(false);
  const [passwordError, setPasswordError] = useState(false);
  const [termError, setTermError] = useState(false);

  const onChangePasswordCheck = useCallback(
    (e) => {
      setPasswordError(password !== e.target.value);
      setPasswordCheck(e.target.value);
    },
    [password]
  );

  const onChangeTerm = useCallback((e) => {
    setTermError(false);
    setTerm(e.target.checked);
  }, []);

  const onSubmit = useCallback(
    (e) => {
      e.preventDefault();
      if (passwordError) {
        return;
      }
      if (!term) {
        return setTermError(true);
      }
      console.log({ email, nickname, password, passwordCheck, term });
    },
    [email, nickname, password, passwordCheck, term, passwordError, termError]
  );

  return (
    <div>
      <Head>
        <title>C And C</title>
        <link
          rel="stylesheet"
          href="https://cdnjs.cloudflare.com/ajax/libs/antd/3.23.6/antd.min.css"
        />
      </Head>
      <Layout>
        <Form onSubmit={onSubmit} style={{ padding: '10px' }}>
          <div>
            <label htmlFor="user-email">Email</label>
            <br />
            <Input
              name="user-email"
              required
              value={email}
              onChange={onChangeEmail}
            />
          </div>
          <div>
            <label htmlFor="user-nickname">Nickname</label>
            <br />
            <Input
              name="user-nickname"
              required
              value={nickname}
              onChange={onChangeNickname}
            />
          </div>
          <div>
            <label htmlFor="user-password">Password</label>
            <br />
            <Input
              name="user-password"
              required
              value={password}
              onChange={onChangePassword}
            />
          </div>
          <div>
            <label htmlFor="user-password-check">Password check</label>
            <br />
            <Input
              name="user-password-check"
              required
              type="password"
              value={passwordCheck}
              onChange={onChangePasswordCheck}
            />
            {passwordError && (
              <div style={{ color: 'red' }}>Passwords do not match.</div>
            )}
          </div>
          <div>
            <Checkbox name="user-term" checked={term} onChange={onChangeTerm}>
              I agree.
            </Checkbox>
            {termError && (
              <div style={{ color: 'red' }}>You must agree to the terms</div>
            )}
          </div>
          <div>
            <Button type="primary" htmlType="submit">
              Signup
            </Button>
          </div>
        </Form>
      </Layout>
    </div>
  );
};

export default signup;

```

코드가 많이 줄어든 것을 확인할 수 있다.
