---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - Layout 코드 분할"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## Layout 코드 분할

```js
import React from 'react';
import Link from 'next/link';
import PropTypes from 'prop-types';
import { Menu, Input, Button, Row, Col, Card, Avatar, Form } from 'antd';

const dummy = {
  nickname: 'angelx',
  myCafes: [{}, {}],
  isLoggedIn: false
};

const Layout = ({ children }) => {
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
      <Row>
        <Col xs={24} md={6}>
          {dummy.isLoggedIn ? (
            <Card
              actions={[
                <div key="my-cafe">My Cafe<br />{dummy.myCafes.length}</div>
              ]}
            >
              <Card.Meta
                avatar={<Avatar>{dummy.nickname[0]}</Avatar>}
                title={dummy.nickname}
              />
            </Card>
          ) : (
            <Form>
              <div>
                <label htmlFor="user-email">Email</label>
                <Input name="user-email" required />
              </div>
              <div>
                <label htmlFor="user-password">Password</label>
                <Input name="user-password" type="password" required />
              </div>
              <Button type="primary" htmlType="submit" loading={true}>
                Login
              </Button>
              <Link href="/signup">
                <Button>Signup</Button>
              </Link>
            </Form>
          )}
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

지금까지 작성한 `Layout.js` 코드다.

일단 코드가 지저분하게 길고, `submit` 버튼을 클릭하였을 경우 화면 전체가 reredering 될 수 도 있다.

이런 경우 컴포넌트를 분리하는 것이 좋다.

컴포넌트를 분리할 때 반복분이나 분기문, 삼항 연산자를 기준으로 분리할 대상을 찾아보자.

```js
import React from 'react';
import Link from 'next/link';
import PropTypes from 'prop-types';
import { Menu, Input, Row, Col } from 'antd';

import LoginForm from './LoginForm';
import UserProfile from './UserProfile';

const dummy = {
  nickname: 'angelx',
  myCafes: [{}, {}],
  isLoggedIn: false
};

const Layout = ({ children }) => {
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
      <Row>
        <Col xs={24} md={6}>
          {dummy.isLoggedIn ? <UserProfile /> : <LoginForm />}
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

`components/UserProfile.js`, `componets/LoginForm.js` 파일을 생성하여 사용자 카드, 로그인 폼을 분리했다.

코드가 훨씬 단순해졌다.

### LoginForm 완성하기

```js
import React, { useCallback } from 'react';
import Link from 'next/link';
import { Form, Input, Button } from 'antd';

import useInput from '../components/useInput';

const LoginForm = () => {
  const [email, onChangeEmail] = useInput('');
  const [password, onChangePassword] = useInput('');
  const onSubmit = useCallback((e) => {
    e.preventDefault();
    console.log({ email, password });
  }, [email, password]);

  return (
    <Form onSubmit={onSubmit}>
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
      <Button type="primary" htmlType="submit" loading={false}>
        Login
      </Button>
      <Link href="/signup">
        <Button>Signup</Button>
      </Link>
    </Form>
  );
};

export default LoginForm;

```

email, password를 처리할 `useInput`을 추가했다.

`pages/signup.js`와 크게 다르지 않다.

### UserProfile 완성

```js
import React from 'react';

import { Card, Avatar } from 'antd';

const dummy = {
  nickname: 'angelx',
  myCafes: [{}, {}],
  isLoggedIn: false
};

const UserProfile = () => {
  return (
    <Card
      actions={[
        <div key="my-cafe">
          My Cafe
          <br />
          {dummy.myCafes.length}
        </div>
      ]}
    >
      <Card.Meta
        avatar={<Avatar>{dummy.nickname[0]}</Avatar>}
        title={dummy.nickname}
      />
    </Card>
  );
};

export default UserProfile;

```

`components/UserProfile.js`은 `components/Layout.js`에 있던 코드를 그대로 옮겨왔다.
