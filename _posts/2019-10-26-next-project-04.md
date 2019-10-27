---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - _app.js"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## _app.js로 코드 분리

react devtools를 사용하면 state가 변경될 때 rerendering 되는 항목을 확인할 수 있다.

현재는 `Input`에 데이터를 입력할 때 마다 모든 항목이 rerendering 되는 것을 볼 수 있다.

컴포넌트를 잘게 쪼개면 rerendering을 막을 수 있다. 그리고 이렇게 하는 것이 유지보수를 쉽게 만든다.

`pages` 폴더 내에 `_app.js`라는 파일을 만들자.

`_app.js`는 `pages`내의 모든 컴포넌트들의 부모 컴포넌트 같은 역할을 한다.

```js
import React from 'react';
import Head from 'next/head';

import Layout from '../components/Layout';

const CandC = ({ Component }) => {
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
        <Component />
      </Layout>
    </div>
  );
};

export default CandC;

```

`pages` 폴더 내의 파일들에 공통 코드를 `_app.js`로 복사하고 나머지 파일에서는 모두 삭제했다.

`Component`는 Next.js에서 제공하는 props다.

각 페이지가 render될 때 `_app.js`가 먼저 호출되고 하위 컴포넌트들이 `Component`로 전달된다.

이렇게 작성하면 `Input`에 데이터가 입력될 때 `Head` 부분은 다시 rendering 되지 않는다.

## _document.js, _app.js, _error.js

`_app.js` 외에도 `_docuemnt.js`, `_error.js`가 있다.

`_document.js`는 html, head, body의 역할을 한다.

`_app.js`는 react에서 주로 사용하는 root의 역할을 한다.

만약 에러가 발생한다면 `_error.js`가 에러가 출력될 화면이 된다.
