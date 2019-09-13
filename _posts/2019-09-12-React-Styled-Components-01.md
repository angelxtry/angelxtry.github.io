---
layout: post
comments: true
title: "[React] React styled components #1"
tags:
  - react
  - styled components
---

React styled components를 사용해 보려고 공부 중이다.

Youtube 노마드코더의 [Make CSS Great Again! Styled Components!](https://www.youtube.com/watch?v=MqGxMOhPqeI&list=PL7jH19IHhOLNUIOJcGj6egl-dNB-QXjEm) 를 보고 일부 내용을 요약하고, 일부 수정, 내 생각을 추가했다.

## 기본 환경 설정

먼저 create-react-app으로 프로젝트를 하나 만들고 styled-components를 설치한다.

```cmd
create-react-app styled-components-intro
cd styled-components-intro
yarn add styled-components
```

src 디렉토리에서 App.js, index.js를 제외하고 다른 파일들은 삭제하자.

App.js에 button을 하나 추가했다.

```js
import React from "react";

function App() {
  return (
    <button>Hello</button>
  );
}

export default App;

```

`yarn start`로 실행해보면 웹 브라우져에 기본 스타일의 버튼 하나가 만들어진 것을 확인할 수 있다.

## styled components "Hello World"

`styled components`를 import 하고 `Button`을 만들어보자.

```js
import React from "react";
import styled from "styled-components";

function App() {
  return (
    <Button>Hello World</Button>
  );
}

const Button = styled.button``;


export default App;
```

`const Button = styled.button``;`에서 button은 html tag다.

`h1`, `h2`, `button`, `input` 등의 html tag를 모두 사용할 수 있다.

이렇게 수정하면  `<Button>`을 사용한 것이 `<button>`을 사용한 것과 동일한 효과가 난다.

이제 `<Button>`에 스타일을 적용해보자.

```js
import React, { Fragment } from "react";
import styled from "styled-components";

function App() {
  return (
    <Fragment>
      <Button>Hello World</Button>
      <button>Hello World</button>
    </Fragment>
  );
}

const Button = styled.button`
  border-radius: 50px;
  padding: 5px;
  min-width: 120px;
  font-weight: 600;
  color: white;
  cursor: pointer;
  &:active,
  &:focus {
    outline: none;
  }
`;

export default App;

```

비교를 위해 button을 하나 더 만들고 `Fragment`로 감싸주었다.

`styled.button` 뒤에 백틱으로 감싸고 그 안에 적용할 스타일을 입력한다.

## styled components에 param 전달하기

```js
import React, { Fragment } from "react";
import styled from "styled-components";

function App() {
  return (
    <Fragment>
      <Button>Hello</Button>
      <Button danger>Hello</Button>
    </Fragment>
  );
}

const Button = styled.button`
  border-radius: 50px;
  padding: 5px;
  min-width: 120px;
  font-weight: 600;
  color: white;
  cursor: pointer;
  &:active,
  &:focus {
    outline: none;
  }
  background-color: ${props => (props.danger ? "#eb2f06" : "#78e08f")};
`;

export default App;
```

`<Button danger>Hello</Button>` 이렇게 하면 Button에 danger가 param으로 전달된다.

전달된 param을 background-color에 사용했다.

## 만들어진 styled components 재활용 1

anchor를 하나 만들어보자.

추가할 anchor는 기존에 만들어 둔 Button과 동일한 스타일을 적용하려고 한다.

`as`라는 키워드를 이용하여 간단하게 구현할 수 있다.

```js
import React, { Fragment } from "react";
import styled from "styled-components";

function App() {
  return (
    <Fragment>
      <Button>Hello</Button>
      <Button danger>Hello</Button>
      <Button as="a" href="https://angelxtry.github.io">
        My static git page
      </Button>
    </Fragment>
  );
}

const Button = styled.button`
  border-radius: 50px;
  padding: 5px;
  min-width: 120px;
  font-weight: 600;
  color: white;
  cursor: pointer;
  &:active,
  &:focus {
    outline: none;
  }
  background-color: ${props => (props.danger ? "#eb2f06" : "#78e08f")};
`;

export default App;
```

세 번째로 추가한 Button에 `as="a"`를 추가했다.
`as`는 v4에 추가된 기능으로 polymorphic prop이라고 부른다.
`as`뒤에 다른 html tag나 components를 입력하여 기존 component를 다르게 동작하게 할 수 있다.
지금은 Button에 `as="a" href="https://angelxtry.github.io"` 이렇게 입력하여 ancher로 동작하게 만들었다.

## 만들어진 styled components 재활용 2

anchor에 자동으로 생긴 under line이 마음에 들지 않는다면 `text-decoration`을 이용하여 under line을 제거할 수도 있다.

```js
import React, { Fragment } from "react";
import styled from "styled-components";

function App() {
  return (
    <Fragment>
      <Button>Hello</Button>
      <Button danger>Hello</Button>
      <Button as="a" href="https://angelxtry.github.io">
        My static git page
      </Button>
    </Fragment>
  );
}

const Button = styled.button`
  border-radius: 50px;
  padding: 5px;
  min-width: 120px;
  font-weight: 600;
  color: white;
  cursor: pointer;
  &:active,
  &:focus {
    outline: none;
  }
  background-color: ${props => (props.danger ? "#eb2f06" : "#78e08f")};
  text-decoration: ${props => (props.as === "a" ? "none" : "")};
`;

export default App;

```

이렇게 해도 동작은 한다.
하지만 Button component에 부가적인 스타일이 계속 추가되는 것이 마음에 들지 않는다면 별도로 분리하는 것도 좋은 방법이다.

```js
import React, { Fragment } from "react";
import styled from "styled-components";

function App() {
  return (
    <Fragment>
      <Button>Hello</Button>
      <Button danger>Hello</Button>
      <Button as="a" href="https://angelxtry.github.io">
        My static git page
      </Button>
      <Anchor as="a" href="https://angelxtry.github.io">
        Another styled components anchor
      </Anchor>
    </Fragment>
  );
}

const Button = styled.button`
  border-radius: 50px;
  padding: 5px;
  min-width: 120px;
  font-weight: 600;
  color: white;
  cursor: pointer;
  &:active,
  &:focus {
    outline: none;
  }
  background-color: ${props => (props.danger ? "#eb2f06" : "#78e08f")};
  text-decoration: ${props => (props.as === "a" ? "none" : "")};
`;

const Anchor = styled(Button)`
  text-decoration: none;
`;

export default App;

```

Anchor라는 component를 만들었다.
이 component는 Button componet의 style을 그대로 사용한다.
그리고 `text-decoration` 스타일만을 하나 더 추가했다.
어떤 방식으로 코드를 작성할지는 상황에 따라 선택하면 되겠다.

## animation 추가

Button에 animation을 추가해보자.

```js
import React, { Fragment } from "react";
import styled, { keyframes, css } from "styled-components";

function App() {
  return (
    <Fragment>
      <Button>Hello</Button>
      <Button danger>Hello</Button>
      <Button as="a" href="https://angelxtry.github.io" fadeIn>
        My static git page
      </Button>
      <Anchor as="a" href="https://angelxtry.github.io" fadeIn>
        Another styled components anchor
      </Anchor>
    </Fragment>
  );
}

const Button = styled.button`
  border-radius: 50px;
  padding: 5px;
  min-width: 120px;
  font-weight: 600;
  color: white;
  cursor: pointer;
  &:active,
  &:focus {
    outline: none;
  }
  background-color: ${props => (props.danger ? "#eb2f06" : "#78e08f")};
  text-decoration: ${props => (props.as === "a" ? "none" : "")};
  animation: ${props =>
    props.fadeIn
      ? css`
          ${fadeIn} 2s
        `
      : ""};
`;

const Anchor = styled(Button)`
  text-decoration: none;
`;

const fadeIn = keyframes`
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
`;

export default App;

```

코드를 하나씩 분석해보자.

```js
const fadeIn = keyframes`
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
`;
```

keyframes라는 키워드를 이용하여 animation의 효과를 적었다.
이렇게 만든 fadeIn이라는 component를 Button에 추가한다.

```js
animation: ${props =>
  props.fadeIn
    ? css`
        ${fadeIn} 2s
      `
    : ""};
```

여기서 `css`라는 키워드를 사용했다.
`css`는 템플릿 리터럴 안에서 사용한 함수가 다시 템플릿 리터럴을 리턴할 때 사용한다.

props로 전달되는 param에 fadeIn이 있다면 animation이 적용된다.

```js
<Button as="a" href="https://angelxtry.github.io" fadeIn>...</Button>
<Anchor as="a" href="https://angelxtry.github.io" fadeIn>...</Anchor>
```

Button과 Anchor에 모두 fadeIn을 적용했다.
브라우저에서 확인해보면 fade in 효과가 적용된 것을 확인할 수 있다.

(내일에 이어서...)
