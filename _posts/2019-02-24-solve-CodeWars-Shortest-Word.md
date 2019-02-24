---
layout: post
comments: true
title: "[Solve] Shortest Word"
tags:
  - CodeWars
  - python
---

# Shortest Word

인자로 문자열을 받는다.

문자열을 구성하고 있는 단어 중 길이가 가장 짧은 단어의 길이를 return한다.

## Example

```py
assert solve.find_short('finds shortest word') == 4
assert solve.find_short(
        'at least two spaces before inline comment') == 2
assert solve.find_short(
        "bitcoin take over the world maybe who knows perhaps") == 3
assert solve.find_short(
        "turns out random test cases are "
        "easier than writing out basic ones") == 3
assert solve.find_short(
        "lets talk about javascript the best language") == 3
assert solve.find_short(
        "i want to travel the world writing code one day") == 1
assert solve.find_short(
        "Lets all go on holiday somewhere very cold") == 2
```

## Solve

```py
def find_short(s):
    return min([len(word) for word in s.split(' ')])
```

문자열로 받은 s를 split으로 나누고, 단어의 길이를 구해 list를 만들었다.

그리고 list를 min() 함수를 이용하여 가장 짧은 단어의 길이를 구했다.

이번은 best solution도 내 풀이와 비슷했다.

```py
def best_solve1(s):
    return min(len(word) for word in s.split())
```

내 풀이와 차이가 있다면 split() 함수의 default 인자가 공백이기 때문에 굳이 명시하지 않은 것.

그리고 comprehension으로 list를 만들지 않아도 min() 함수에 적용한 것이다.

```py
def best_solve2(s):
    return min(map(len, s.split()))
```

이건 map을 사용했다. 성능은 모르겠지만 훨씬 더 직관적이라고 생각된다.

```py
def best_solve3(s):
    return len(min(s.split(), key=len))
```

min() 함수에 key param을 이용하여 가장 짧은 단어를 구하고 다시 len() 함수를 사용하여 길이를 구했다.

map()을 사용한 풀이가 가장 맘에 든다.
