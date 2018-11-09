---
layout: post
comments: true
title: "[SQL] 데이터에 포함된 엔터, 줄바꿈 제거"
tags:
  - sql
  - oracle
---

## 요건

데이터에 의도하지 않은 엔터 또는 줄바꿈이 포함되어 있다.

sql로 데이터를 뽑을 때 제거해야 한다.

## query

줄바꿈이 포함된 데이터

```sql
SELECT T.DATA
FROM (
SELECT 'ABC
' AS DATA
FROM DUAL
) T
;
```

줄바꿈을 제거

```sql
SELECT REPLACE(REPLACE(T.DATA, chr(13), ''), chr(10), '') AS DATA
FROM (
SELECT 'ABC
' AS DATA
FROM DUAL
) T
;
```

많이 지저분하다.

데이터가 정돈되어 저장되는 것이 맞지만 어쩔 수 없는 경우가 있다.
