---
layout: post
comments: true
title: "[React] React styled components #2"
tags:
  - react
  - styled components
---

어제에 이어서 styeld components에 대해 계속 정리해본다.

노마드코더의 [Make CSS Great Again! Styled Components!](https://www.youtube.com/watch?v=MqGxMOhPqeI&list=PL7jH19IHhOLNUIOJcGj6egl-dNB-QXjEm) Youtube를 보고 일부 내용을 요약하고, 일부 수정, 내 생각을 추가했다.

## 이전 글

[[React] React styled components #1](https://angelxtry.github.io/React-Styled-Components-01/)

## body 전체의 margin, padding 없애기

body전체에 배경색을 넣으려고 한다.

`Fragment` 대신 `Container`라는 것을 하나 만들어 교체하고 `Container`에 스타일을 적용한다.

```js
import React from "react";
import styled, { keyframes, css } from "styled-components";

function App() {
  return (
    <Container>
      <Button>Hello</Button>
      <Button danger>Hello</Button>
      <Button as="a" href="https://angelxtry.github.io" fadeIn>
        My static git page
      </Button>
      <Anchor as="a" href="https://angelxtry.github.io" fadeIn>
        Another styled components anchor
      </Anchor>
    </Container>
  );
}

const Container = styled.div`
  height: 100vh;
  width: 100%;
  background-color: #ecf0f1;
`;

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

브라우저 화면을 가득 채우는 배경색이 적용되었으나 테두리에 못생긴 공백이 있다.
이것을 없애기 위해 다음과 같이 코드를 추가한다.

```js
import React, { Fragment } from "react";
import styled, { createGlobalStyle, keyframes, css } from "styled-components";

const GlobalStyle = createGlobalStyle`
  body {
    margin: 0;
    padding: 0;
  }
`;

function App() {
  return (
    <Fragment>
      <GlobalStyle />
      <Container>
        <Button>Hello</Button>
        <Button danger>Hello</Button>
        <Button as="a" href="https://angelxtry.github.io" fadeIn>
          My static git page
        </Button>
        <Anchor as="a" href="https://angelxtry.github.io" fadeIn>
          Another styled components anchor
        </Anchor>
      </Container>
    </Fragment>
  );
}

const Container = styled.div`
  height: 100vh;
  width: 100%;
  background-color: #ecf0f1;
`;

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

`createGlobalStyle`이라는 키워드로 `GlobalStyle`을 생성했다.

`createGlobalStyle`도 v4에 추가되었다. 기존에 사용하던 `injectGlobal`은 deprecated되었다.

다시 `Fragment`를 이용하여 `Container`위에 `GlobalStyle`을 추가하고 브라우저를 다시 확인해보자.

## form, input 등의 표현

```js
import React, { Fragment } from "react";
import styled, { createGlobalStyle } from "styled-components";

const GlobalStyle = createGlobalStyle`
  body {
    margin: 0;
    padding: 0;
  }
`;

function App() {
  return (
    <Fragment>
      <GlobalStyle />
      <Container>
        <form>
          <Input type="text" name="user-name" required/>
          <Button type="submit">Submit</Button>
        </form>
      </Container>
    </Fragment>
  );
}

const Container = styled.div`
  height: 100vh;
  width: 100%;
  background-color: #ecf0f1;
`;

const Input = styled.input`
  border-radius: 5px;
`;

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

`form`의 `Input`은 styled component다.
`Input`에서 사용한 type, name, required 같은 attribute들도 styled component에서 표현할 수 있다.

```js
const Input = styled.input.attrs({
  type: "text",
  name: "user-name",
  required: true
})`
  border-radius: 5px;
`;
```

`attrs` 메서드 안에 객체로 attribute를 표현한다.
어떤 것이 더 명시적이냐는 논란이 있을 수 있겠지만 잘 포장해서 사용하면 좋을 것 같기도 하다.

## mixin

mixin은 css 묶음이다. 한 번 설정해두고 계속 재사용할 수 있다.
이전에 사용했던 `css` 키워드를 이용하여 설정한다.

```js
import React, { Fragment } from "react";
import styled, { createGlobalStyle, css } from "styled-components";

const GlobalStyle = createGlobalStyle`
  body {
    margin: 0;
    padding: 0;
  }
`;

function App() {
  return (
    <Fragment>
      <GlobalStyle />
      <Container>
        <form>
          <Input />
          <Button type="submit">Submit</Button>
        </form>
      </Container>
    </Fragment>
  );
}

const awesomeCard = css`
  box-shadow: 0 4px 6px rgba(50, 50, 93, 0.11), 0 1px 3px rgba(0, 0, 0, 0.08);
  background-color: white;
  border-radius: 10px;
  padding: 20px;
`;

const Container = styled.div`
  height: 100vh;
  width: 100%;
  background-color: #f8c291;
`;

const Input = styled.input.attrs({
  type: "text",
  name: "user-name",
  required: true
})`
  ${awesomeCard}
`;

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

`awesomeCard`라는 mixin을 생성하고 `Input` 적용했다.
mixin 이라는 이름보다 그냥 변수에 css를 담아놓고 적용한다는 느낌이다.
해당 값을 공용 파일에 분리해 놓으면 다른 사람들도 활용할 수 있다는 점이 좋아보인다.

## theme

마지막으로 `theme`을 알아보자.
theme은 redux같은 느낌이다. 별도의 파일에 공통으로 쓰일 스타일을 적용해 놓고 사용할 수 있다.

코드를 보자.

먼저 theme를 하나 생성한다. theme.js 파일을 만들고 객체를 하나 선언한다.

```js
const theme = {
  basicColor: "#3c6382",
  successColor: "#38ada9",
  dangerColor: "#eb2f06"
};

export default theme;

```

App.js 파일에서 theme를 import해서 사용한다.

```js
import React, { Fragment } from "react";
import styled, { createGlobalStyle, ThemeProvider } from "styled-components";

import theme from "./theme";

const GlobalStyle = createGlobalStyle`
  body {
    margin: 0;
    padding: 0;
  }
`;

function App() {
  return (
    <ThemeProvider theme={theme}>
      <Fragment>
        <GlobalStyle />
        <Container>
          <Cards />
        </Container>
      </Fragment>
    </ThemeProvider>
  );
}

const Cards = () => {
  return (
    <Fragment>
      <Card backgroundColor={props => props.theme.basicColor}>basic</Card>
      <Card backgroundColor={props => props.theme.successColor}>success</Card>
      <Card backgroundColor={props => props.theme.dangerColor}>danger</Card>
    </Fragment>
  );
};

const Container = styled.div`
  height: 100vh;
  width: 100%;
  background-color: #f8c291;
`;

const Button = styled.button`
  border-radius: 10px;
  padding: 50px 50px;
  font-weight: 600;
  color: white;
  cursor: pointer;
  &:active,
  &:focus {
    outline: none;
  }
`;

const Card = styled(Button)`
  margin: 10px;
  color: white;
  background-color: ${props => props.backgroundColor};
`;

export default App;

```

`ThemeProvider`를 import 하고 redux의 Provider 처럼 다른 component들을 감싸준다.

`Card` component를 하나 만들었다. `Button` component의 스타일을 그대로 사용하면서 일부만 자신의 스타일을 가진다.

`Cards`에서 `Card` component를 사용하면서 param으로 theme에서 정의한 값을 전달한다.

브라우저에서 확인해보면 해당 색이 적용된 것을 알 수 있다.

## fin

간단하게 styled components의 사용법을 확인해봤다.

막상 사용하려고 하니 hooks로 만든 components의 이름과 styled component의 이름이 비슷해서 혼란스러운 점도 있다.

그래도 css 지식이 부족한 나에게는 component 별로 분리해서 스타일을 적용하는 방식이 상당히 편하게 느껴진다.

어떻게 활용해야 할지는 좀 더 공부를 해봐야겠다.
