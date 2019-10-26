---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## start

환경 설정을 한다.

폴더를 만들고, npm으로 package를 설치하고, eslint, prettier 설정을 하자.

```js
cd next-project-front
npm init
npm i react react-dom next
npm i -D nodemon webpack
npm i antd

npm i -D eslint-plugin-import eslint-plugin-react eslint-plugin-react-hooks
```

## Hello, Next.js

`pages/index.js` 폴더와 파일을 생성한다.

```js
const Home = () => {
  return <div>Hello, Next.js</div>;
};

export default Home;

```

`package.json`에 `script` 부분을 수정한다.

```json
"scripts": {
  "dev": "next"
}
```

console에 `npm run dev`라고 입력해보자.

`http://localhost:3000`으로 접속하면 `Hello, Next.js`를 볼 수 있다.

## Layout + antd 추가

### `components/Layout.js`

```js
import React from 'react';
import { Menu, Input } from 'antd';

const Layout = ({ children }) => {
  return (
    <div>
      <Menu>
        <Menu.Item key="home">Home</Menu.Item>
        <Menu.Item key="profile">Profile</Menu.Item>
        <Menu.Item key="search">
          <Input.Search enterButton />
        </Menu.Item>
      </Menu>
      {children}
    </div>
  );
};

export default Layout;

```

`antd`의 `Menu`와 `Input`을 이용하여 `Layout`을 만들었다.

props으로 사용한 `children`은 예약어다.

`<Layout></Layout>` 태그로 감싼 내부의 항목들이 `children`으로 전달된다.

### `pages/index.js`

```js
import React from 'react';
import Head from 'next/head';

import Layout from '../components/Layout';

const Home = () => {
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
        <div>Hello, Next.js</div>
      </Layout>
    </div>
  );
};

export default Home;

```

`components/Layout.js`

`pages/index.js` 파일에 `Layout`을 적용했다.

그리고 `antd`를 적용하기 위해 Next.js의 `Head`를 사용하여 cdn의 css를 링크했다.

브라우저로 확인해보면 깔끔하게 메뉴가 출력되었으나 세로로 정렬되어 있는 것을 볼 수 있다.

가로로 정렬하기 위해 코드를 수정한다.

```js
import React from 'react';
import { Menu, Input } from 'antd';

const Layout = ({ children }) => {
  return (
    <div>
      <Menu mode="horizontal">
        <Menu.Item key="home">Home</Menu.Item>
        <Menu.Item key="profile">Profile</Menu.Item>
        <Menu.Item key="search">
          <Input.Search enterButton style={{ verticalAlign: 'middle' }} />
        </Menu.Item>
      </Menu>
      {children}
    </div>
  );
};

export default Layout;

```

`Input`의 위치를 잡아주기 위해 `style={{ verticalAlign: 'middle' }}`을 추가했다.

![첫 화면](https://drive.google.com/uc?export=view&id=1PVIs38F3B02RMr8zSiU4sF4EXd04LeeL)
