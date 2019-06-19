---
layout: post
comments: true
title: "[Solve] Codility:FrogRiverOne"
tags:
  - Codility
  - python
---

# FrogRiverOne

개구리가 강을 건넌다는 식의 설명을 하고 있지만 결국 정수 X와 배열 A가 주어졌을 때

1부터 X까지 모든 정수가 몇 번째 배열에서 채워지는가를 확인하는 문제다.

X가 5이고 [1, 2, 3, 4, 3, 4, 5] 배열이 있을 때 index: 7을 리턴한다.

[1, 2, 3, 4], [1, 3, 4, 5] 같이 1부터 5까지의 정수가 다 채워지지 않는 경우 -1을 리턴한다.

## 내가 작성한 코드

```py
def frog_river_one(X, A):
    river = set()
    for i in range(0, len(A)):
        if A[i] > X:
            continue
        river.add(A[i])
        if len(river) == X:
            return i
    return -1
```

O(N)의 시간복잡도가 나왔다.

나름 쉬웠던 문제.

range를 쓰는 것보다 enumerate를 쓰는 것이 코드가 더 깔끔하다.

```py
def frog_river_one(X, A):
    river = set()
    for index, value in enumerate(A):
        if value > X:
            continue
        river.add(value)
        if len(river) == X:
            return index
    return -1
```

가정에 A의 element는 [1, ..., X]라고 했지만 혹시나 싶어 조건을 하나 더 넣었다.
