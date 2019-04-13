---
layout: post
comments: true
title: "[Solve] Codility:PermCheck"
tags:
  - Codility
  - python
---

# PermCheck

정수 배열에 1부터 N까지의 숫자가 한 개씩 들어 있어야한다.

순서는 랜덤. 같은 숫자가 2번 이상 나타나거나, 중간에 빠진 숫자가 있으면 return 0.

1부터 N까지 숫자가 모두 한 번씩만 포함되어 있다면 return 1.

## 내가 작성한 코드

```py
def perm_check(A):
    length = len(A)
    if not max(A) == length:
        return 0
    count = [0] * length
    for i in range(length):
        index = A[i] - 1
        count[index] += 1
        if count[index] > 1:
            return 0
    return 1 if sum(A) == length else 0
```

max(A)와 A의 길이를 비교하여 갑자기 큰 수가 나오는 경우를 처리했다. 이 경우를 먼저 처리하지 않으면 count에서 out of range error가 발생할 수 있다.

이 코드를 제출하니 O(N) or O(N * log(N))의 시간 복잡도가 나왔다.

## 다른 사람들이 작성한 코드

```py
def perm_check(A):
    if max(A) == len(A) and len(set(A)) == len(A):
        return 1
    return 0
```

정말 단순하고 깔끔하다. 언제쯤 이런 코드가 쑥쑥 나오려나.
