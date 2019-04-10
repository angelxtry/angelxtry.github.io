---
layout: post
comments: true
title: "[Solve] Codility:Tape Equilibrium"
tags:
  - Codility
  - python
---

# Tape Equilibrium

Codility 문제. 문제를 제대로 이해 못 해서 완전히 헤맸다.

문제는 다음과 같다.

숫자 배열을 인자로 받는 함수가 있다.

숫자 배열은 N개의 [-1000, ..., 1000] 사이의 integer다.

N개의 배열을 적당히 두 묶음으로 나눴을 때 각 묶음의 합의 차의 최소값을 구하라.

예를 들어 다음과 같은 배열이 있을 때

[-3, -2, -1, 5]

[-3], [-2, -1, 5]  | `| -3 - 2 |  = 5`
[-3, -2], [-1, 5]  | `| -5 - 4 | = 9`
[-3, -2, -1], [5]  | `| -6 - 5 | = 11`

따라서 최소값은 5가 된다.

## 내가 작성한 코드

```py
def tape_equilibrium(A):
    left_sum = 0
    right_sum = sum(A)
    minimum = 0
    for index in range(0, len(A) - 1):
        left_sum += A[index]
        right_sum -= A[index]
        diff = abs(left_sum - right_sum)
        if index == 0:
            minimum = diff
        else:
            minimum = min(minimum, diff)
    return minimum
```

right_sum에 배열의 마지막 element는 남겨둬야 하기 때문에 len(A) - 1까지 순회해야 한다.

배열을 slicing해서 계속 sum을 하는 것이 아니라 sum은 한번만 하는 것이 key point인 듯 하다.

sum()도 결국 배열을 한번 순회하는 것과 동일하다.

위와 같이 처리하면 시간 복잡도는 O(N)이 된다.
