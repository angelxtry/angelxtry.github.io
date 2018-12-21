---
layout: post
comments: true
title: "[SQL] REGEXP 관련 함수들을 이용하여 문자열을 구분자로 분리하기"
tags:
  - sql
  - oracle
---

```sql
WITH DATA_TABLE AS (
    SELECT 'A_B_C_D_E_F' AS DATA FROM DUAL
)
SELECT
REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 1) AS DATA_A
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 2) AS DATA_B
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 3) AS DATA_C
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 4) AS DATA_D
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 5) AS DATA_E
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 6) AS DATA_F
FROM DATA_TABLE T
;
```

| DATA_A | DATA_B | DATA_C | DATA_D | DATA_E | DATA_F |
| ------ | ------ | ------ | ------ | ------ | ------ |
| A      | B      | C      | D      | E      | F      |

ORACLE의 SUBSTR 함수는 문자열을 자를때 사용한다.

SUBSTR의 확장 개념으로 REGEXP_SUBSTR은 정규식을 이용하여 문자열을 자를 수 있다.

signature는 다음과 같이 생겼다.

```sql
REGEXP_SUBSTR(srcstr, pattern, [,position[,occurrence[,match_option]]])
```

srcstr: 입력 문자열

pattern: 정규식

position: 시작 위치. 1부터 시작한다.

occurrence: pattern 발견 횟수. 1부터 시작한다.

```sql
REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 1)
```

위 sql의 의미는 다음과 같다.

`[]` 대괄호 사이에 존재하는 문자들 중 하나에 일치하는 것을 찾는다.

`^_` `_`가 아닌 문자.

`+` 반드시 1개 또는 1개 이상

`첫 번째 1` 문자열의 시작 부터

`두 번째 1` 정규식에 일치하는 첫 번째 문자열

`+`가 있기 때문에 여러 개의 문자가 붙어 있어도 처리가능하다.

```sql
WITH DATA_TABLE AS (
    SELECT 'A123_B123_C123_D123_E123_F123' AS DATA FROM DUAL
)
SELECT
REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 1) AS DATA_A
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 2) AS DATA_B
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 3) AS DATA_C
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 4) AS DATA_D
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 5) AS DATA_E
,REGEXP_SUBSTR(T.DATA, '[^_]+', 1, 6) AS DATA_F
FROM DATA_TABLE T
;
```

| DATA_A | DATA_B | DATA_C | DATA_D | DATA_E | DATA_F |
| ------ | ------ | ------ | ------ | ------ | ------ |
| A123   | B123   | C123   | D123   | E123   | F123   |

보통은 입력받은 문자열에 문자열과 `_`가 몇 번 반복될지 알 수 없는 상황이 대부분이다. 이런 상황을 한 번에 처리하기 위해 다음과 같은 방법은 이용한다.

```sql
WITH DATA_TABLE AS (
    SELECT 'A123_B123_C123_D123_E123_F123' AS DATA FROM DUAL
)
SELECT LEVEL, TRIM(REGEXP_SUBSTR(T.DATA, '[^_]+', 1, LEVEL)) AS SPLIT_DATA
FROM DATA_TABLE T
CONNECT BY LEVEL <= REGEXP_COUNT(T.DATA, '_') + 1
;
```

| LEVEL | SPLIT_DATA |
| ----- | ---------- |
| 1     | A123       |
| 2     | B123       |
| 3     | C123       |
| 4     | D123       |
| 5     | E123       |
| 6     | F123       |

REGEXP_COUNT와 CONNECT BY LEVEL을 이용했다.

CONNECT BY LEVEL은 여러 용도로 사용되는 듯 하나 여기서는 연속된 숫자를 얻기 위한 방법으로만 사용한다.

```sql
SELECT LEVEL
FROM DUAL
CONNECT BY LEVEL <= 3
;
```

| LEVEL |
| ----- |
| 1     |
| 2     |
| 3     |

반드시 LEVEL까지 붙여서 사용해야 한다.

REGEXP_COUNT는 해당 문자열에서 정규식이 몇 번 출현하는지 센다. 위 문자열에서는 `_`가 5번 출현한다. 그래서 `+1`이 필요하다. `_`의 갯수가 늘어날 수록 row가 추가되고 DATA는 null이 된다.
