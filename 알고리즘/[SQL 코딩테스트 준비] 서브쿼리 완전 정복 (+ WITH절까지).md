[Velog로 가기](https://velog.io/@kimdongwoo0930/SQL-코딩테스트-준비-서브쿼리-완전-정복-WITH절까지)

released at 2026-02-22 18:31:50 KST

updated at 2026-10-16 07:20:04 KST

|[CTE](https://velog.io/tags/CTE)|[mysql](https://velog.io/tags/mysql)|[sql](https://velog.io/tags/sql)|[with](https://velog.io/tags/with)|[데이터베이스](https://velog.io/tags/데이터베이스)|[서브쿼리](https://velog.io/tags/서브쿼리)|[코딩테스트](https://velog.io/tags/코딩테스트)|
|----|----|----|----|----|----|----|


> **시리즈 목차**
> 01편 - 환경 세팅 | 02편 - SELECT | 03편 - 집계 함수 | 04편 - GROUP BY | 05편 - JOIN | **06편 - 서브쿼리**

---

JOIN을 배웠으니 이제 한 단계 더 나아가보자.

코테에서 자주 마주치는 패턴이 있다.

> "가격이 **제일 비싼** 상품의 전체 정보를 가져와라"  
> "**평균보다 많이** 주문한 회원만 골라라"

이런 문제들은 `MAX()`, `AVG()` 같은 집계 결과를 **조건으로 다시 사용**해야 한다.  
이게 바로 **서브쿼리(Subquery)**다.

---

## 실습 테이블 구조

이번 편도 `members`, `orders` 테이블을 그대로 사용한다.

**members 테이블**

| id | name   | age | gender |
|----|--------|-----|--------|
| 1  | 김동우 | 25  | M      |
| 2  | 이지은 | 30  | F      |
| 3  | 박민준 | 22  | M      |
| 4  | 최수아 | 27  | F      |
| 5  | 정하늘 | 35  | M      |

**orders 테이블**

| id | member_id | product | amount  | order_date |
|----|-----------|---------|---------|------------|
| 1  | 1         | 노트북  | 1200000 | 2024-01-05 |
| 2  | 1         | 마우스  | 35000   | 2024-01-10 |
| 3  | 2         | 키보드  | 89000   | 2024-02-03 |
| 4  | 3         | 모니터  | 450000  | 2024-02-15 |
| 5  | 1         | 헤드셋  | 150000  | 2024-03-01 |
| 6  | 4         | 이어폰  | 50000   | 2024-03-10 |

---

## 1️⃣ 서브쿼리란?

**쿼리 안에 또 다른 쿼리**를 넣는 것이다.  
안쪽 쿼리(서브쿼리)가 먼저 실행되고, 그 결과를 바깥 쿼리가 사용한다.

```sql
SELECT *
FROM orders
WHERE amount = (SELECT MAX(amount) FROM orders);
--             ↑ 이 부분이 서브쿼리
```

**실행 순서:**
1. `SELECT MAX(amount) FROM orders` → `1200000` 반환
2. `WHERE amount = 1200000` 조건으로 바깥 쿼리 실행

---

## 2️⃣ WHERE 절 서브쿼리

가장 많이 쓰이는 패턴이다.

### 가장 비싼 주문 정보 가져오기

```sql
SELECT *
FROM orders
WHERE amount = (SELECT MAX(amount) FROM orders);
```

**결과**

| id | member_id | product | amount  | order_date |
|----|-----------|---------|---------|------------|
| 1  | 1         | 노트북  | 1200000 | 2024-01-05 |

### 평균보다 많이 주문한 내역만

```sql
SELECT *
FROM orders
WHERE amount > (SELECT AVG(amount) FROM orders);
```

> 📌 `WHERE`절에는 `AVG()`, `MAX()` 같은 집계함수를 직접 못 쓴다.  
> 서브쿼리로 감싸야 한다.

```sql
-- ❌ 이렇게 쓰면 에러
WHERE amount > AVG(amount)

-- ✅ 서브쿼리로 감싸야 함
WHERE amount > (SELECT AVG(amount) FROM orders)
```

---

## 3️⃣ IN 서브쿼리

서브쿼리 결과가 **여러 행**일 때는 `IN`을 사용한다.

### 주문한 적 있는 회원 정보만

```sql
SELECT *
FROM members
WHERE id IN (SELECT DISTINCT member_id FROM orders);
```

**결과**

| id | name   | age | gender |
|----|--------|-----|--------|
| 1  | 김동우 | 25  | M      |
| 2  | 이지은 | 30  | F      |
| 3  | 박민준 | 22  | M      |
| 4  | 최수아 | 27  | F      |

### 주문한 적 없는 회원만 (NOT IN)

```sql
SELECT *
FROM members
WHERE id NOT IN (SELECT DISTINCT member_id FROM orders);
```

**결과**

| id | name   | age | gender |
|----|--------|-----|--------|
| 5  | 정하늘 | 35  | M      |

---

## 4️⃣ FROM 절 서브쿼리 (인라인 뷰)

서브쿼리를 **테이블처럼** 사용하는 패턴이다.  
집계 결과를 다시 조건으로 사용할 때 유용하다.

### 회원별 총 주문금액 중 100만원 이상만

```sql
SELECT *
FROM (
    SELECT member_id, SUM(amount) AS total
    FROM orders
    GROUP BY member_id
) AS summary
WHERE total >= 1000000;
```

**결과**

| member_id | total   |
|-----------|---------|
| 1         | 1385000 |

> 📌 FROM 절 서브쿼리에는 반드시 **별칭(AS)**을 붙여야 한다.

---

## 5️⃣ WITH절 (CTE) — 서브쿼리를 깔끔하게

FROM 절 서브쿼리가 길어지면 가독성이 떨어진다.  
**WITH절(CTE, Common Table Expression)**을 쓰면 훨씬 깔끔해진다.

```sql
-- ❌ FROM 절 서브쿼리 (복잡해 보임)
SELECT m.name, s.total
FROM members m
JOIN (
    SELECT member_id, SUM(amount) AS total
    FROM orders
    GROUP BY member_id
) AS s ON m.id = s.member_id
WHERE s.total >= 100000;

-- ✅ WITH절로 분리 (훨씬 읽기 쉬움)
WITH summary AS (
    SELECT member_id, SUM(amount) AS total
    FROM orders
    GROUP BY member_id
)
SELECT m.name, s.total
FROM members m
JOIN summary s ON m.id = s.member_id
WHERE s.total >= 100000
ORDER BY s.total DESC;
```

**결과**

| name   | total   |
|--------|---------|
| 김동우 | 1385000 |
| 박민준 | 450000  |
| 이지은 | 89000   |
| 최수아 | 50000   |

---

## 6️⃣ 실전 연습 문제

> 앞편에서 본 **연도별 대장균 크기의 편차** 패턴이다.  
> 서브쿼리 + JOIN의 대표 코테 문제 유형.

**문제**: `orders` 테이블에서  
각 주문의 **금액 편차** (해당 월 최대 주문금액 - 각 주문금액)를 구하세요.  
결과는 order_date 기준 오름차순으로 정렬하세요.

**힌트**: `MONTH(order_date)`로 월을 추출하고, WITH절로 월별 MAX를 먼저 구하세요.

<details>
<summary>정답 보기</summary>

```sql
WITH monthly_max AS (
    SELECT
        MONTH(order_date) AS month,
        MAX(amount)       AS max_amount
    FROM orders
    GROUP BY MONTH(order_date)
)
SELECT
    o.id,
    o.product,
    o.amount,
    (m.max_amount - o.amount) AS diff
FROM orders o
JOIN monthly_max m ON MONTH(o.order_date) = m.month
ORDER BY o.order_date ASC;
```

</details>

---

## 📌 자주 하는 실수

**1. WHERE절에 집계함수 직접 사용**

```sql
-- ❌ 에러 발생
SELECT * FROM orders WHERE amount > AVG(amount);

-- ✅ 서브쿼리로 감싸기
SELECT * FROM orders WHERE amount > (SELECT AVG(amount) FROM orders);
```

**2. FROM 절 서브쿼리에 별칭 누락**

```sql
-- ❌ 별칭 없으면 에러
SELECT * FROM (SELECT member_id, SUM(amount) AS total FROM orders GROUP BY member_id);

-- ✅ 반드시 AS 별칭 붙이기
SELECT * FROM (SELECT member_id, SUM(amount) AS total FROM orders GROUP BY member_id) AS summary;
```

**3. IN vs = 혼동**

```sql
-- ❌ 서브쿼리가 여러 행 반환하면 = 사용 불가
WHERE id = (SELECT member_id FROM orders)  -- 여러 행이면 에러

-- ✅ 여러 행이면 IN 사용
WHERE id IN (SELECT member_id FROM orders)
```

---

## 📌 정리

| 패턴 | 사용 위치 | 예시 |
|------|-----------|------|
| 단일 값 비교 | WHERE | `WHERE amount = (SELECT MAX(amount) ...)` |
| 다중 값 비교 | WHERE IN | `WHERE id IN (SELECT member_id ...)` |
| 임시 테이블 | FROM | `FROM (SELECT ...) AS alias` |
| 가독성 향상 | WITH | `WITH alias AS (SELECT ...)` |

- `WHERE`절에 집계함수 직접 사용 불가 → 서브쿼리로 감싸기
- FROM 절 서브쿼리는 반드시 별칭(AS) 필요
- 서브쿼리가 복잡해지면 `WITH절(CTE)`로 분리

> 서브쿼리와 JOIN을 함께 쓰면 **연도별 편차, 그룹별 최대/최소** 같은 코테 단골 패턴을 모두 풀 수 있다.