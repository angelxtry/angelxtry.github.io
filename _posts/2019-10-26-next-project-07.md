---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - 로그인 폼 추가"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## 로그인 폼 추가

```js
const dummy = {
  nickname: 'angelx',
  myCafes: [{}, {}],
  isLoggedIn: false
};

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
      <Button type="primary" htmlType="submit">Login</Button>
      <Link href="/signup">
        <Button>Signup</Button>
      </Link>
    </Form>
  )}
</Col>

```

사용자 카드와 함께 로그인 폼을 출력했다.

그리고 `isLoggedIn`이라는 더미 데이터를 만들어서 로그인 했을 때는 사용자 카드, 로그아웃일 때는 로그인 폼을 보여주도록 처리한다.

```js
<Button type="primary" htmlType="submit" loading={true}>Login</Button>
```

![loading](https://drive.google.com/uc?export=view&id=1pCVE3w8TmxJW-xEx5TnHBApraREPQz2o)

`antd`의 `Button`에는 `loading`이라는 속성이 있다.

비동기 상태일 때 로딩 중이라는 표시로 버튼에 로딩 아이콘이 표시된다.
