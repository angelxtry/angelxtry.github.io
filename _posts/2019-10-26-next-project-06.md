---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - Card, Avatar를 활용한 사용자 카드"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## Card, Avatar를 활용한 사용자 카드

`antd`의 `Card`와 `Avatar`를 사용한다.

```js
const dummy = {
  nickname: 'angelx',
  myCafes: [{}, {}]
};

<Card
  actions={[<div key="my-cafe">My Cafe<br />{dummy.myCafes.length}</div>
  ]}
>
  <Card.Meta
    avatar={<Avatar>{dummy.nickname[0]}</Avatar>}
    title={dummy.nickname}
  />
</Card>
```

![avatar](https://drive.google.com/uc?export=view&id=1mpMINkLsW58VNygdBhbDeg4eorn4K4Ch)

`Card.Meta`가 상단부, `Card actions`가 하단부를 담당한다.
