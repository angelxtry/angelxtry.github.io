---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - signup page 만들기"

tags:
  - Next.js
  - reac기
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## signup page 만들기

```js
import React, { useState, useCallback } from 'react';
import Head from 'next/head';
import { Form, Input, Button } from 'antd';

import Layout from '../components/Layout';

const signup = () => {
  const [email, setEmail] = useState('');

  const onChangeEmail = useCallback(e => {
    setEmail(e.target.value);
  }, []);

  const onSubmit = useCallback(e => {
    e.preventDefault();
    console.log({ email });
  }, [email]);

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

단순한 회원가입 Form이다. 사용자에게 입력받는 항목은 이메일 밖에 없다.

먼저 jsx 코드 부터 보자면 label, `Input`, `Button`을 추가했다.

`Button`은 `Form` 태그 안에 위치해야 한다.

`Input` 태그에는 `value`, `onChange` 속성이 포함되어 있고, 해당 항목은 `useState`를 이용하여 구현된다.

동일한 방식으로 `Form`에 필요한 항목들을 추가해보자.

```js
import React, { useState, useCallback } from 'react';
import Head from 'next/head';
import { Form, Input, Button, Checkbox } from 'antd';

import Layout from '../components/Layout';

const signup = () => {
  const [email, setEmail] = useState('');
  const [nickname, setNickname] = useState('');
  const [password, setPassword] = useState('');
  const [passwordCheck, setPasswordCheck] = useState('');
  const [term, setTerm] = useState(false);

  const onChangeEmail = useCallback(e => {
    setEmail(e.target.value);
  }, []);

  const onChangeNickname = useCallback(e => {
    setNickname(e.target.value);
  }, []);

  const onChangePassword = useCallback(e => {
    setPassword(e.target.value);
  }, []);

  const onChangePasswordCheck = useCallback(e => {
    setPasswordCheck(e.target.value);
  }, []);

  const onChangeTerm = useCallback(e => {
    setTerm(e.target.checked);
  }, []);

  const onSubmit = useCallback(
    e => {
      e.preventDefault();
      console.log({ email, nickname, password, passwordCheck, term });
    },
    [email, nickname, password, passwordCheck, term]
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
          </div>
          <div>
            <Checkbox name="user-term" checked={term} onChange={onChangeTerm}>
              I agree.
            </Checkbox>
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

이메일, 닉네임, 페스워드, 패스워드 체크, 약관 동의 다섯 항목을 만들었다.

`Checkbox`는 `onChangeCheckbox`에서 처리할 때 `e.target.checked`를 사용한다.

`onSubmit`은 `useCallback`으로 감싸져 있다.

따라서 두 번째 인자인 dependency에 변수명을 넣지 않으면 해당 변수의 변화에 반응하지 못한다.

이제 검증 로직을 추가해보자.

```js
import React, { useState, useCallback } from 'react';
import Head from 'next/head';
import { Form, Input, Button, Checkbox } from 'antd';

import Layout from '../components/Layout';

const signup = () => {
  const [email, setEmail] = useState('');
  const [nickname, setNickname] = useState('');
  const [password, setPassword] = useState('');
  const [passwordCheck, setPasswordCheck] = useState('');
  const [term, setTerm] = useState(false);
  const [passwordError, setPasswordError] = useState(false);
  const [termError, setTermError] = useState(false);

  const onChangeEmail = useCallback((e) => {
    setEmail(e.target.value);
  }, []);

  const onChangeNickname = useCallback((e) => {
    setNickname(e.target.value);
  }, []);

  const onChangePassword = useCallback((e) => {
    setPassword(e.target.value);
  }, []);

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

암호와 약관 동의에 검증 로직을 추가했다.

```js
import React, { useState, useCallback } from 'react';
import Head from 'next/head';
import { Form, Input, Button, Checkbox } from 'antd';

import Layout from '../components/Layout';

const signup = () => {
  const [email, setEmail] = useState('');
  const [nickname, setNickname] = useState('');
  const [password, setPassword] = useState('');
  const [passwordCheck, setPasswordCheck] = useState('');
  const [term, setTerm] = useState(false);
  const [passwordError, setPasswordError] = useState(false);
  const [termError, setTermError] = useState(false);

  const onChangeEmail = useCallback((e) => {
    setEmail(e.target.value);
  }, []);

  const onChangeNickname = useCallback((e) => {
    setNickname(e.target.value);
  }, []);

  const onChangePassword = useCallback((e) => {
    setPassword(e.target.value);
  }, []);

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

암호와 약관 동의에 검증 로직을 추가했다.

```js
const [passwordError, setPasswordError] = useState(false);

const onChangePasswordCheck = useCallback(
  (e) => {
    setPasswordError(password !== e.target.value);
    setPasswordCheck(e.target.value);
  },
  [password]
);

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
```

`passwordError` 선언 시 최초 상태는 `false`다.

그래야 최초 화면 출력 시점에 에러 메시지가 화면에 표시되지 않는다.

`passwordCheck`가 입력되면 `passwordError`가 `true`가 되어 화면에 에러 메시지가 출력된다.

`password`, `passwordCheck`가 동일해지는 시점에 `false`가 되고, 에러 메시지가 사라진다.

약관 동의도 비슷한 로직을 따른다.

![화면](https://drive.google.com/uc?export=view&id=1BnAFrrmW-ZwH2e5yQNuEiEQCSDAn4aDs)
