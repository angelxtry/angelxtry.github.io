---
layout: post
comments: true
title: "Next.js를 이용한 간단한 프로젝트 - Home 화면 만들기"

tags:
  - Next.js
  - react
  - antd
---

Next.js를 공부하면서 간단한 프로젝트를 만들어보려고 한다.

제로초님의 인프런 강의를 보고 따라한 것임을 미리 밝힌다.

## Home 화면 만들기

```js
import React from 'react';
import { Form, Input, Button } from 'antd';

const dummy = {
  isLoggedIn: true
};

const Home = () => {
  return (
    <div>
      {dummy.isLoggedIn && (
        <Form encType="multipart/form-data">
          <Input.TextArea maxLength={140} placeholder="기분을 적어주세요." />
          <div>
            <Input type="file" multiple hidden />
            <Button>Image Upload</Button>
            <Button type="primary" style={{ float: 'right' }} htmlType="submit">
              write
            </Button>
          </div>
        </Form>
      )}
    </div>
  );
};

export default Home;

```

로그인이 되었다면 해당 폼을 보여준다.

`encType="multipart/form-data"`로 이미지도 함께 올릴 수 있도록 처리한다.

```js
import React from 'react';
import { Form, Input, Button, Card, Icon, Avatar } from 'antd';

const dummy = {
  isLoggedIn: true,
  mainPosts: [
    {
      postId: 1,
      User: {
        nickname: 'angelx'
      },
      content: 'Study hard!',
      img: ''
    },
    {
      postId: 2,
      User: {
        nickname: 'angelx'
      },
      content: 'Make money!',
      img: ''
    }
  ]
};

const Home = () => {
  return (
    <div>
      {dummy.isLoggedIn && (
        <Form encType="multipart/form-data">
          <Input.TextArea maxLength={140} placeholder="기분을 적어주세요." />
          <div>
            <Input type="file" multiple hidden />
            <Button>Image Upload</Button>
            <Button type="primary" style={{ float: 'right' }} htmlType="submit">
              write
            </Button>
          </div>
        </Form>
      )}
      {dummy.mainPosts.map((post) => {
        return (
          <Card
            key={post.postId}
            corver={post.img && <img alt="example" src={post.img} />}
            actions={[
              <Icon type="retweet" key="retweet" />,
              <Icon type="heart" key="heart" />,
              <Icon type="message" key="message" />,
              <Icon type="ellipsis" key="ellipsis" />
            ]}
            extra={<Button>팔로우</Button>}
          >
            <Card.Meta
              avatar={<Avatar>{post.User.nickname[0]}</Avatar>}
              title={post.User.nickname}
              description={post.content}
            />
          </Card>
        );
      })}
    </div>
  );
};

export default Home;

```

작성된 글을 보여주는 `Card`까지 작성했다.

이 부분도 코드를 분리하자.

`pages/index.js`

```js
import React from 'react';

import PostForm from '../components/PostForm';
import PostCard from '../components/PostCard';

const dummy = {
  isLoggedIn: true,
  mainPosts: [
    {
      postId: 1,
      User: {
        nickname: 'angelx'
      },
      content: 'Study hard!',
      img: ''
    },
    {
      postId: 2,
      User: {
        nickname: 'angelx'
      },
      content: 'Make money!',
      img: ''
    }
  ]
};

const Home = () => {
  return (
    <div>
      {dummy.isLoggedIn && <PostForm />}
      {dummy.mainPosts.map((post) => {
        return <PostCard key={post.postId} post={post} />;
      })}
    </div>
  );
};

export default Home;

```

`components/PostForm.js`

```js
import React from 'react';
import { Form, Input, Button } from 'antd';

const PostForm = () => {
  return (
    <Form encType="multipart/form-data">
      <Input.TextArea maxLength={140} placeholder="기분을 적어주세요." />
      <div>
        <Input type="file" multiple hidden />
        <Button>Image Upload</Button>
        <Button type="primary" style={{ float: 'right' }} htmlType="submit">
          write
        </Button>
      </div>
    </Form>
  );
};

export default PostForm;

```

`components/PostCard.js`

```js
import React from 'react';
import { Card, Icon, Button, Avatar } from 'antd';

const PostCard = ({ post }) => {
  return (
    <Card
      corver={post.img && <img alt="example" src={post.img} />}
      actions={[
        <Icon type="retweet" key="retweet" />,
        <Icon type="heart" key="heart" />,
        <Icon type="message" key="message" />,
        <Icon type="ellipsis" key="ellipsis" />
      ]}
      extra={<Button>팔로우</Button>}
    >
      <Card.Meta
        avatar={<Avatar>{post.User.nickname[0]}</Avatar>}
        title={post.User.nickname}
        description={post.content}
      />
    </Card>
  );
};

export default PostCard;

```

## Profile 만들기

```js
import React from 'react';
import { Form, Input, Button, List, Card, Icon } from 'antd';

const profile = () => {
  return (
    <div>
      <Form
        style={{
          marginBottom: '20px',
          border: '1px solid #d9d9d9',
          padding: '20px'
        }}
      >
        <Input addonBefore="Nickname" />
        <Button type="primary">Modify</Button>
      </Form>
      <List
        style={{ marginBottom: '20px' }}
        grid={{ gutter: 4, xs: 2, md: 3 }}
        size="small"
        header={<div>Following List</div>}
        loadMore={<Button style={{ width: '100%' }}>More</Button>}
        bordered
        dataSource={['aaa', 'bbb', 'ccc']}
        renderItem={(item) => (
          <List.Item style={{ marginTop: '20px' }}>
            <Card actions={[<Icon key="stop" type="stop" />]}>
              <Card.Meta description={item} />
            </Card>
          </List.Item>
        )}
      />
      <List
        style={{ marginBottom: '20px' }}
        grid={{ gutter: 4, xs: 2, md: 3 }}
        size="small"
        header={<div>Follower List</div>}
        loadMore={<Button style={{ width: '100%' }}>More</Button>}
        bordered
        dataSource={['bbb', 'ccc', 'ddd']}
        renderItem={(item) => (
          <List.Item style={{ marginTop: '20px' }}>
            <Card actions={[<Icon key="stop" type="stop" />]}>
              <Card.Meta description={item} />
            </Card>
          </List.Item>
        )}
      />
    </div>
  );
};

export default profile;

```
