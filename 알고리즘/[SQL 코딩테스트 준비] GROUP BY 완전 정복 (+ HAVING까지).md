[Velog로 가기](https://velog.io/@kimdongwoo0930/SQL-코딩테스트-준비-GROUP-BY-완전-정복-HAVING까지)

released at 2026-02-20 19:27:51 KST

updated at 2026-10-23 19:04:12 KST

|[groupby](https://velog.io/tags/groupby)|[mysql](https://velog.io/tags/mysql)|[sql](https://velog.io/tags/sql)|[데이터베이스](https://velog.io/tags/데이터베이스)|[백엔드](https://velog.io/tags/백엔드)|[코딩테스트](https://velog.io/tags/코딩테스트)|
|----|----|----|----|----|----|

> 지난 글에서 COUNT, SUM, AVG, MAX, MIN으로 **전체 데이터를 요약**하는 법을 배웠습니다.
>
> 이번엔 한 단계 더 나아가서 **"회원별", "월별", "상품별"** 처럼 그룹을 나눠서 집계하는
> `GROUP BY`를 배워봅니다. 코딩테스트에서 정말 자주 나오는 핵심 문법입니다.

---

## 🗂️ 실습 환경 확인

```sql
USE sql_practice;
```

- **members 테이블** — 30명의 회원 정보 (id, name, age, email, join_date)
- **orders 테이블** — 80건의 주문 정보 (order_id, member_id, product_name, price, order_date)

---

## 📌 GROUP BY란?

`GROUP BY`는 **특정 컬럼의 값이 같은 행들을 하나의 그룹으로 묶어서** 집계할 수 있게 해줍니다.

예를 들어, 이런 orders 데이터가 있다고 하면:

| order_id | member_id | price |
|----------|-----------|-------|
| 1 | 1 | 1,500,000 |
| 2 | 1 | 50,000 |
| 3 | 2 | 100,000 |
| 4 | 2 | 80,000 |

`GROUP BY member_id`를 하면 member_id가 같은 행끼리 묶여서 집계됩니다.

| member_id | SUM(price) |
|-----------|------------|
| 1 | 1,550,000 |
| 2 | 180,000 |

---

## 1️⃣ 기본 문법

```sql
SELECT 그룹기준_컬럼, 집계함수(컬럼)
FROM 테이블
GROUP BY 그룹기준_컬럼;
```

**회원별 주문 횟수**를 구해봅시다.

```sql
SELECT member_id, COUNT(*) AS 주문횟수
FROM orders
GROUP BY member_id;
```

```
+-----------+-----------+
| member_id | 주문횟수   |
+-----------+-----------+
|         1 |         5 |
|         2 |         4 |
|         3 |         3 |
|         4 |         3 |
|         5 |         4 |
...
```

member_id 별로 각자 몇 번 주문했는지 한 번에 알 수 있습니다.

> ⚠️ **중요한 규칙**: `SELECT`에 쓰는 컬럼은 반드시 `GROUP BY`에 포함되어 있거나, 집계 함수로 감싸져 있어야 합니다.
>
> ```sql
> -- ❌ 잘못된 예: product_name은 GROUP BY에도 없고 집계함수도 아님
> SELECT member_id, product_name, COUNT(*)
> FROM orders
> GROUP BY member_id;
>
> -- ✅ 올바른 예
> SELECT member_id, COUNT(*)
> FROM orders
> GROUP BY member_id;
> ```

---

## 2️⃣ GROUP BY + SUM — 회원별 총 주문 금액

```sql
SELECT member_id, SUM(price) AS 총_주문금액
FROM orders
GROUP BY member_id
ORDER BY 총_주문금액 DESC;
```

```
+-----------+--------------+
| member_id | 총_주문금액   |
+-----------+--------------+
|         6 |      2770000 |
|        24 |      3300000 |
|         5 |      1765000 |
...
```

`ORDER BY`를 함께 써서 금액 높은 순으로 정렬했습니다.

---

## 3️⃣ GROUP BY + MAX/MIN — 회원별 최고/최저 주문 금액

```sql
SELECT
    member_id,
    MAX(price) AS 최고_주문금액,
    MIN(price) AS 최저_주문금액
FROM orders
GROUP BY member_id;
```

회원별로 가장 비싸게 산 상품과 가장 싸게 산 상품의 금액을 동시에 볼 수 있습니다.

---

## 4️⃣ GROUP BY + 여러 집계함수

한 번에 다양한 통계를 뽑아봅시다.

```sql
SELECT
    member_id,
    COUNT(*)                   AS 주문횟수,
    SUM(price)                 AS 총_주문금액,
    ROUND(AVG(price), 0)       AS 평균_주문금액,
    MAX(price)                 AS 최고_주문금액,
    MIN(price)                 AS 최저_주문금액
FROM orders
GROUP BY member_id
ORDER BY 총_주문금액 DESC;
```

이 한 쿼리로 **회원별 구매 분석 리포트**가 완성됩니다.

---

## 5️⃣ GROUP BY + WHERE — 조건 후 그룹 집계

`WHERE`는 그룹을 만들기 **전에** 먼저 행을 필터링합니다.

```sql
-- 2024년 3월 이후 주문만 골라서, 회원별 합계 계산
SELECT member_id, SUM(price) AS 3월이후_주문금액
FROM orders
WHERE order_date >= '2024-03-01'
GROUP BY member_id
ORDER BY 3월이후_주문금액 DESC;
```

실행 순서를 기억하세요:
1. `FROM orders` — 테이블 가져오기
2. `WHERE` — 조건으로 행 필터링
3. `GROUP BY` — 그룹으로 묶기
4. `SELECT` — 집계 결과 출력
5. `ORDER BY` — 정렬

---

## 6️⃣ HAVING — 그룹 결과에 조건 걸기

집계 결과에 조건을 걸고 싶을 때 `HAVING`을 사용합니다.

**총 주문 금액이 200,000원 이상인 회원만** 보고 싶다면?

```sql
-- ❌ 이렇게 하면 에러!
SELECT member_id, SUM(price) AS 총_주문금액
FROM orders
WHERE SUM(price) >= 200000   -- WHERE에는 집계함수 못 씀
GROUP BY member_id;

-- ✅ HAVING을 사용해야 함
SELECT member_id, SUM(price) AS 총_주문금액
FROM orders
GROUP BY member_id
HAVING SUM(price) >= 200000
ORDER BY 총_주문금액 DESC;
```

```
+-----------+--------------+
| member_id | 총_주문금액   |
+-----------+--------------+
|        24 |      3300000 |
|         6 |      2770000 |
|         5 |      1765000 |
...
```

**WHERE vs HAVING 차이점 정리:**

| 구분 | WHERE | HAVING |
|------|-------|--------|
| 적용 시점 | 그룹 만들기 **전** | 그룹 만든 **후** |
| 사용 가능 조건 | 일반 컬럼 | 집계 함수 결과 |
| 예시 | `WHERE age > 25` | `HAVING COUNT(*) > 3` |

> 💡 **코테 TIP**: WHERE와 HAVING을 함께 쓸 수도 있습니다!
>
> ```sql
> -- 3월 이후 주문 중, 총 금액 200,000원 이상인 회원
> SELECT member_id, SUM(price) AS 총_주문금액
> FROM orders
> WHERE order_date >= '2024-03-01'
> GROUP BY member_id
> HAVING SUM(price) >= 200000;
> ```

---

## 7️⃣ 날짜 기준으로 GROUP BY — 월별 매출

`YEAR()`, `MONTH()` 함수를 활용하면 날짜 기준 집계도 가능합니다.

```sql
-- 월별 주문 건수와 총 매출
SELECT
    YEAR(order_date)   AS 연도,
    MONTH(order_date)  AS 월,
    COUNT(*)           AS 주문건수,
    SUM(price)         AS 월_매출
FROM orders
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY 연도, 월;
```

```
+-------+----+-----------+-----------+
| 연도   | 월  | 주문건수  |  월_매출   |
+-------+----+-----------+-----------+
|  2024 |  2 |        25 |   9870000 |
|  2024 |  3 |        50 |  12480000 |
|  2024 |  4 |         5 |    410000 |
+-------+----+-----------+-----------+
```

---

## 8️⃣ members 테이블과 JOIN해서 이름 보기 (미리보기)

지금까지는 member_id(숫자)로만 봤는데, 이름을 함께 보려면 JOIN이 필요합니다.
JOIN은 다음 편에서 자세히 다루고, 여기선 맛보기만 보여드릴게요.

```sql
SELECT
    m.name         AS 회원명,
    COUNT(*)       AS 주문횟수,
    SUM(o.price)   AS 총_주문금액
FROM orders o
JOIN members m ON o.member_id = m.id
GROUP BY m.id, m.name
ORDER BY 총_주문금액 DESC
LIMIT 5;
```

```
+--------+-----------+--------------+
| 회원명  | 주문횟수   | 총_주문금액   |
+--------+-----------+--------------+
| 박준영  |         2 |      3300000 |
| 정수진  |         3 |      2770000 |
| 최민수  |         4 |      1765000 |
| 윤서연  |         2 |      1250000 |
| 강지훈  |         4 |      1230000 |
+--------+-----------+--------------+
```

---

## 📝 핵심 정리

```sql
SELECT 컬럼, 집계함수(컬럼)
FROM 테이블
WHERE 행_필터_조건          -- 그룹 만들기 전 필터 (집계함수 사용 불가)
GROUP BY 그룹기준_컬럼
HAVING 그룹_필터_조건        -- 그룹 만든 후 필터 (집계함수 사용 가능)
ORDER BY 정렬기준
LIMIT 개수;
```

| 개념 | 기억할 것 |
|------|----------|
| `GROUP BY` | 같은 값끼리 묶어서 집계 |
| `HAVING` | 집계 결과에 조건 걸기 |
| `WHERE` vs `HAVING` | WHERE는 집계 전, HAVING은 집계 후 |
| SELECT 규칙 | GROUP BY 컬럼 또는 집계함수만 SELECT에 사용 가능 |