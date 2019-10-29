---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - antd를 이용한 반응형 기능 추가"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## antd를 이용한 반응형 기능 추가

```js
import React from 'react';
import Link from 'next/link';
import PropTypes from 'prop-types';
import { Menu, Input, Button, Row, Col } from 'antd';

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
      <Row gutter={8}>
        <Col xs={24} md={6}>
          <Link href="/signup">
            <Button>Signup</Button>
          </Link>
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

`antd`의 그리드 시스템은 `Col`의 전체 크기가 24이다.

```js
<Col xs={24} md={6}></Col>
```

`xs`는 모바일, `md`는 테스크탑 사이즈라고 생각하면 된다.

위와 같이 작성하면 브라우저의 화면이 `xs` 사이즈일 때 가로 영역 전체를 다 사용하고, `md` 사이즈일 때 가로 전체의 4분의 1을 사용한다는 의미다.
