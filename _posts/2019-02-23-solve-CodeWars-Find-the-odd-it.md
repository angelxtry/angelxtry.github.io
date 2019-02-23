---
layout: post
comments: true
title: "[Solve] Find the odd int"
tags:
  - CodeWars
  - python
---

# Find the odd int

정수로 구성된 배열을 인자로 받는다.

배열의 각 요소들의 빈도가 홀수인 요소를 찾는다.

## Example

```txt
find_if([1, 2, 1]) -> 2
find_if([1, 2, 1, 2, 1]) -> 1
```

## Solve

```py
def find_it(sq):
    """ Find the odd int """
    dict = {}
    for num in sq:
        if num in dict.keys():
            dict[num] = dict[num] + 1
        else:
            dict[num] = 1

    for member, count in dict.items():
        if count % 2:
            return member

    return None

```

dictionary를 하나 만들어서 처리했다.

역시 이번도 best solution에 배울만한 것들이 있었다.
list의 count() 함수를 이용하여 깔끔하게 처리했다.
collections의 Counter() 함수를 사용한 것도 참고할만하다.

```py
from collections import Counter


def best_solve1(seq):
    for num in seq:
        if seq.count(num) % 2:
            return num


def best_solve2(seq):
    return [num for num in seq if seq.count(num) % 2][0]


def best_solve3(seq):
    return [k for k, v in Counter(seq).items() if v % 2][0]

```

list에 count() 함수가 있다는 것을 기억해두자.
