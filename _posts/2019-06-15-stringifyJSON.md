---
layout: post
comments: true
title: "stringJSOM"
tags:
  - Node.js
  - JavaScript
  - CodeStates
---

# stringifyJSON 구현

코드 스테이츠 flex에 참여하고 있다.

stringifyJSON을 구현하는 문제다. JSON.stringify()를 재귀를 사용해서 직접 구현해보는 것.

문제를 풀다보니 primitive type을 확인하는 법, template string, destructuring 등을 고민하고, 공부하게 된다.

내가 구현한 코드는 다음과 같다.

```js
const map = (f, iter) => {
  let res = [];
  for (const a of iter) {
    res.push(f(a));
  }
  return res;
}

const stringifyJSON = obj => {
  if (['boolean', 'number', 'undefined'].includes(typeof(obj)) ||
      obj === null) {
    return `${obj}`;
  }
  if (typeof(obj) === 'string') {
    return `"${obj}"`;
  }
  if (Array.isArray(obj)) {
    return `[${map(stringifyJSON, obj).join(',')}]`;
  }
  if (Object.keys(obj).length >= 0) {
    const newObj = {};
    for (const k in obj) {
      if (!(typeof(obj[k]) === 'function' || obj[k] === undefined)) {
        newObj[k] = obj[k];
      }
    }
    const stringifyObj = ([k, v]) => `${stringifyJSON(k)}:${stringifyJSON(v)}`;
    return `{${map(stringifyObj, Object.entries(newObj)).join(',')}}`;
  }
};
```

우선, boolean, number, undefined는 문자열로 출력되고, string은 따옴표가 붙어있는 채로 문자열이 된다.

array는 map과 재귀를 이용했다.

객체의 경우 Object.keys(obj).length로 판단했다. 객체를 판단하기 위한 더 좋은 방법은 뭘까? 궁금하다!

객체의 경우 stringify 처리가 특이했다.

value가 function이나 undefined이면 아예 없는 항목처럼 결과물에는 포함되지 않는다.

함수를 하나 만들어서 destructuring으로 인자를 받고 재귀호출을 진행한 후 key, value 사이에 `:`을 붙여서 처리한다.

코드가 깔끔해보이지 않아 다듬느라고 시간이 오래 걸렸다.

지금도 뭔가 아쉬운 부분이 있지만 처음 작성했을 때 보다는 모양이 이뻐졌다.
