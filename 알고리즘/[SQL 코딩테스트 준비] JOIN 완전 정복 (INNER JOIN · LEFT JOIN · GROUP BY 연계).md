[Velog로 가기](https://velog.io/@kimdongwoo0930/SQL-코딩테스트-준비-JOIN-완전-정복-INNER-JOIN-LEFT-JOIN-GROUP-BY-연계)

released at 2026-02-21 16:33:21 KST

updated at 2026-11-01 09:07:18 KST

|[InnerJoin](https://velog.io/tags/InnerJoin)|[JOIN](https://velog.io/tags/JOIN)|[leftjoin](https://velog.io/tags/leftjoin)|[mysql](https://velog.io/tags/mysql)|[sql](https://velog.io/tags/sql)|[데이터베이스](https://velog.io/tags/데이터베이스)|[코딩테스트](https://velog.io/tags/코딩테스트)|
|----|----|----|----|----|----|----|



> **시리즈 목차**
> 01편 - 환경 세팅 | 02편 - SELECT | 03편 - 집계 함수 | 04편 - GROUP BY | **05편 - JOIN** | 06편 - 서브쿼리

-----

지금까지는 테이블 하나에서만 데이터를 꺼냈다.

하지만 실제 데이터는 여러 테이블에 나뉘어 저장되어 있다.

> 회원 이름은 `members` 테이블에,  
> 주문 정보는 `orders` 테이블에.

이 두 테이블을 연결해서 **“누가 얼마나 주문했는지”** 한 번에 보려면 **JOIN**이 필요하다.

-----

## 실습 테이블 구조

이번 편도 지금까지 써온 `members`, `orders` 테이블을 그대로 사용한다.

**members 테이블**

|id|name|age|gender|
|--|----|---|------|
|1 |김동우 |25 |M     |
|2 |이지은 |30 |F     |
|3 |박민준 |22 |M     |
|4 |최수아 |27 |F     |
|5 |정하늘 |35 |M     |

**orders 테이블**

|id|member_id|product|amount |order_date|
|--|---------|-------|-------|----------|
|1 |1        |노트북    |1200000|2024-01-05|
|2 |1        |마우스    |35000  |2024-01-10|
|3 |2        |키보드    |89000  |2024-02-03|
|4 |3        |모니터    |450000 |2024-02-15|
|5 |1        |헤드셋    |150000 |2024-03-01|
|6 |99       |이어폰    |50000  |2024-03-10|


> 📌 6번 주문의 `member_id = 99`는 `members`에 없는 회원이다.  
> INNER JOIN vs LEFT JOIN 차이를 확인할 때 중요하다.

-----

## 1️⃣ INNER JOIN — 양쪽 모두 있는 것만

**두 테이블에 모두 존재하는 행만** 가져온다.

```sql
SELECT
    m.name,
    o.product,
    o.amount
FROM orders o
INNER JOIN members m ON o.member_id = m.id;
```

**결과**

|name|product|amount |
|----|-------|-------|
|김동우 |노트북    |1200000|
|김동우 |마우스    |35000  |
|이지은 |키보드    |89000  |
|박민준 |모니터    |450000 |
|김동우 |헤드셋    |150000 |


> `member_id = 99`인 이어폰 주문은 `members`에 해당 회원이 없어서 **제외**된다.

-----

## 2️⃣ LEFT JOIN — 왼쪽 테이블은 무조건 다 나온다

`FROM` 뒤에 오는 테이블(왼쪽)의 **모든 행을 유지**하고,  
오른쪽 테이블에 매칭되는 게 없으면 **NULL**로 채운다.

```sql
SELECT
    o.id,
    m.name,
    o.product,
    o.amount
FROM orders o
LEFT JOIN members m ON o.member_id = m.id;
```

**결과**

|id|name|product|amount |
|--|----|-------|-------|
|1 |김동우 |노트북    |1200000|
|2 |김동우 |마우스    |35000  |
|3 |이지은 |키보드    |89000  |
|4 |박민준 |모니터    |450000 |
|5 |김동우 |헤드셋    |150000 |
|6 |NULL|이어폰    |50000  |


> `member_id = 99`인 이어폰은 매칭되는 회원이 없어서 name이 **NULL**로 표시된다.

-----

## 3️⃣ INNER vs LEFT 한눈에 비교

|구분        |설명                |용도              |
|----------|------------------|----------------|
|INNER JOIN|양쪽 모두 일치하는 행만     |확실히 연결된 데이터만 볼 때|
|LEFT JOIN |왼쪽 전체 + 오른쪽 일치하는 것|누락 데이터도 포함해서 볼 때|

```sql
-- INNER JOIN: 주문한 회원만
SELECT m.name, o.product
FROM orders o
INNER JOIN members m ON o.member_id = m.id;

-- LEFT JOIN: 미등록 회원 주문도 포함
SELECT m.name, o.product
FROM orders o
LEFT JOIN members m ON o.member_id = m.id;
```

-----

## 4️⃣ JOIN + GROUP BY 연계

JOIN의 진짜 힘은 **GROUP BY와 함께 쓸 때** 나온다.

### 회원별 총 주문 금액 구하기

```sql
SELECT
    m.name,
    COUNT(o.id)        AS order_count,
    SUM(o.amount)      AS total_amount
FROM orders o
INNER JOIN members m ON o.member_id = m.id
GROUP BY m.id, m.name
ORDER BY total_amount DESC;
```

**결과**

|name|order_count|total_amount|
|----|-----------|------------|
|김동우 |3          |1385000     |
|박민준 |1          |450000      |
|이지은 |1          |89000       |

### 총 주문 금액이 100만원 이상인 회원만

```sql
SELECT
    m.name,
    SUM(o.amount) AS total_amount
FROM orders o
INNER JOIN members m ON o.member_id = m.id
GROUP BY m.id, m.name
HAVING total_amount >= 1000000
ORDER BY total_amount DESC;
```

-----

## 5️⃣ 실전 연습 문제

> 다음 쿼리를 작성해보세요.

**문제**: `orders` 테이블과 `members` 테이블을 JOIN해서,  
**성별(gender)별 총 주문 금액**과 **평균 주문 금액**을 구하세요.  
결과는 총 주문 금액 기준 내림차순으로 정렬하세요.

**힌트**: `GROUP BY m.gender`

<details>
<summary>정답 보기</summary>

```sql
SELECT
    m.gender,
    COUNT(o.id)            AS order_count,
    SUM(o.amount)          AS total_amount,
    ROUND(AVG(o.amount), 0) AS avg_amount
FROM orders o
INNER JOIN members m ON o.member_id = m.id
GROUP BY m.gender
ORDER BY total_amount DESC;
```

**결과**

|gender|order_count|total_amount|avg_amount|
|------|-----------|------------|----------|
|M     |4          |1835000     |458750    |
|F     |1          |89000       |89000     |

</details>

-----

## 📌 자주 하는 실수

**1. ON 조건 빠뜨리기**

```sql
-- ❌ ON 없으면 카테시안 곱 (모든 조합) 발생
SELECT * FROM orders INNER JOIN members;

-- ✅ 반드시 ON으로 연결 조건 명시
SELECT * FROM orders INNER JOIN members ON orders.member_id = members.id;
```

**2. 컬럼명 중복으로 에러**

```sql
-- ❌ 두 테이블 모두 id 컬럼이 있으면 에러
SELECT id FROM orders INNER JOIN members ON orders.member_id = members.id;

-- ✅ 테이블 별칭(alias)으로 구분
SELECT o.id, m.name FROM orders o INNER JOIN members m ON o.member_id = m.id;
```

**3. GROUP BY에 SELECT 컬럼 누락**

```sql
-- ❌ SELECT에 name이 있으면 GROUP BY에도 있어야 함
SELECT m.name, SUM(o.amount)
FROM orders o INNER JOIN members m ON o.member_id = m.id
GROUP BY m.id;  -- name 빠짐

-- ✅
GROUP BY m.id, m.name;
```

-----

## 📌 정리

- `INNER JOIN` — 양쪽 모두 일치하는 행만 반환
- `LEFT JOIN` — 왼쪽 테이블 전체 + 오른쪽 일치하는 것 (없으면 NULL)
- `ON` 조건은 필수, 테이블 별칭(alias)으로 가독성 높이기
- `JOIN + GROUP BY + HAVING` 조합이 코테 단골 패턴

> 다음 편에서는 **서브쿼리**를 배우고,  
> `WHERE PRICE = (SELECT MAX(PRICE) FROM ...)` 같은 패턴을 익혀봅니다.