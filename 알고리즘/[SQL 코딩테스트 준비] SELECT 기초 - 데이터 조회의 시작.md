[Velog로 가기](https://velog.io/@kimdongwoo0930/SELECT-기초-데이터-조회의-시작)

released at 2026-02-16 18:53:31 KST

updated at 2026-11-09 02:00:43 KST

|[mysql](https://velog.io/tags/mysql)|[select](https://velog.io/tags/select)|[sql](https://velog.io/tags/sql)|[데이터베이스](https://velog.io/tags/데이터베이스)|[백엔드](https://velog.io/tags/백엔드)|[코딩테스트](https://velog.io/tags/코딩테스트)|
|----|----|----|----|----|----|



SQL의 가장 기본이자 가장 많이 쓰는 명령어,  
**SELECT**를 완벽하게 정복해보자.

---

## 🎯 오늘의 학습 목표

* SELECT, FROM으로 데이터 조회하기
* WHERE로 조건 필터링
* ORDER BY로 정렬
* LIMIT으로 개수 제한
* DISTINCT로 중복 제거

---

## 📋 사전 준비

[이전 글](https://velog.io/@kimdongwoo0930/VSCode%EC%97%90%EC%84%9C-SQL-%ED%95%99%EC%8A%B5-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0-MySQL-Docker)에서 만든 `members`와 `orders` 테이블을 사용합니다.

### 데이터 확인

```sql
USE sql_practice;

-- 회원 데이터 확인
SELECT * FROM members;

-- 주문 데이터 확인  
SELECT * FROM orders;
```

---

## 1️⃣ SELECT - 기본 조회

### 전체 조회

```sql
-- 모든 컬럼 조회
SELECT * FROM members;
```

**결과:**

| id | name | age | email | join_date |
|----|------|-----|-------|-----------|
| 1 | 김동우 | 25 | dongwoo@... | 2024-01-15 |
| 2 | 홍길동 | 30 | hong@... | 2024-01-20 |
| ... | ... | ... | ... | ... |

---

### 특정 컬럼만 조회

```sql
-- 이름과 나이만
SELECT name, age FROM members;
```

**결과:**

| name | age |
|------|-----|
| 김동우 | 25 |
| 홍길동 | 30 |
| ... | ... |

> **💡 Tip:** 필요한 컬럼만 선택하면 성능이 좋아진다!

---

### 컬럼에 별칭 붙이기

```sql
-- 한글 별칭
SELECT 
    name AS 이름,
    age AS 나이,
    email AS 이메일
FROM members;
```

**결과:**

| 이름 | 나이 | 이메일 |
|------|-----|--------|
| 김동우 | 25 | dongwoo@... |
| ... | ... | ... |

---

## 2️⃣ WHERE - 조건 필터링

### 기본 조건

```sql
-- 나이가 30세 이상인 회원
SELECT name, age 
FROM members 
WHERE age >= 30;
```

**결과:**

| name | age |
|------|-----|
| 홍길동 | 30 |
| 박철수 | 35 |
| 정수진 | 32 |
| ... | ... |

---

### 여러 조건 (AND, OR)

```sql
-- 나이가 25세 이상 30세 이하
SELECT name, age 
FROM members 
WHERE age >= 25 AND age <= 30;

-- 또는 BETWEEN 사용
SELECT name, age 
FROM members 
WHERE age BETWEEN 25 AND 30;
```

```sql
-- 나이가 25세 또는 30세
SELECT name, age 
FROM members 
WHERE age = 25 OR age = 30;

-- 또는 IN 사용
SELECT name, age 
FROM members 
WHERE age IN (25, 30);
```

---

### 문자열 검색 (LIKE)

```sql
-- 이름이 '김'으로 시작하는 회원
SELECT name, email 
FROM members 
WHERE name LIKE '김%';
```

**와일드카드:**
* `%` : 0개 이상의 문자
* `_` : 정확히 1개의 문자

```sql
-- 이메일에 'example'이 포함된 회원
SELECT name, email 
FROM members 
WHERE email LIKE '%example%';

-- 이름이 3글자인 회원
SELECT name 
FROM members 
WHERE name LIKE '___';
```

---

### NULL 처리

```sql
-- 이메일이 없는 회원 (NULL인 경우)
SELECT name 
FROM members 
WHERE email IS NULL;

-- 이메일이 있는 회원 (NULL이 아닌 경우)
SELECT name 
FROM members 
WHERE email IS NOT NULL;
```

> **⚠️ 주의:** `= NULL`이 아니라 `IS NULL`을 사용!

---

## 3️⃣ ORDER BY - 정렬

### 기본 정렬

```sql
-- 나이 오름차순 (작은 것부터)
SELECT name, age 
FROM members 
ORDER BY age ASC;

-- 나이 내림차순 (큰 것부터) - ASC는 생략 가능
SELECT name, age 
FROM members 
ORDER BY age DESC;
```

---

### 여러 컬럼 정렬

```sql
-- 나이 내림차순, 같은 나이면 이름 오름차순
SELECT name, age 
FROM members 
ORDER BY age DESC, name ASC;
```

---

### 계산 결과로 정렬

```sql
-- 가격이 높은 주문부터
SELECT product_name, price 
FROM orders 
ORDER BY price DESC;
```

**결과:**

| product_name | price |
|--------------|-------|
| RTX 4090 | 2500000 |
| MacBook Pro | 2500000 |
| LG 그램 | 1800000 |
| ... | ... |

---

## 4️⃣ LIMIT - 개수 제한

### 상위 N개 조회

```sql
-- 가장 비싼 상품 TOP 5
SELECT product_name, price 
FROM orders 
ORDER BY price DESC 
LIMIT 5;
```

---

### OFFSET - 건너뛰기

```sql
-- 6번째부터 5개 (페이징 구현)
SELECT product_name, price 
FROM orders 
ORDER BY price DESC 
LIMIT 5 OFFSET 5;

-- 또는 축약형
SELECT product_name, price 
FROM orders 
ORDER BY price DESC 
LIMIT 5, 5;  -- LIMIT offset, count
```

---

## 5️⃣ DISTINCT - 중복 제거

### 중복 제거

```sql
-- 주문한 적 있는 회원 ID (중복 제거)
SELECT DISTINCT member_id 
FROM orders;
```

**DISTINCT 없이:**

| member_id |
|-----------|
| 1 |
| 1 |
| 1 |
| 2 |
| 2 |
| ... |

**DISTINCT 사용:**

| member_id |
|-----------|
| 1 |
| 2 |
| 3 |
| ... |

---

### 여러 컬럼 조합으로 중복 제거

```sql
-- 회원별 구매한 날짜 (중복 제거)
SELECT DISTINCT member_id, order_date 
FROM orders 
ORDER BY member_id, order_date;
```

---

## 🔥 실전 연습 문제

### 문제 1
**30세 이상 회원의 이름과 이메일을 나이 내림차순으로 조회하세요.**

<details>
<summary>정답 보기</summary>

```sql
SELECT name, email, age
FROM members
WHERE age >= 30
ORDER BY age DESC;
```

</details>

---

### 문제 2
**가격이 100,000원 이상인 상품 중 상위 10개를 가격 내림차순으로 조회하세요.**

<details>
<summary>정답 보기</summary>

```sql
SELECT product_name, price
FROM orders
WHERE price >= 100000
ORDER BY price DESC
LIMIT 10;
```

</details>

---

### 문제 3
**2024년 2월에 가입한 회원의 이름과 가입일을 조회하세요.**

<details>
<summary>정답 보기</summary>

```sql
SELECT name, join_date
FROM members
WHERE join_date >= '2024-02-01' 
  AND join_date < '2024-03-01';

-- 또는
SELECT name, join_date
FROM members
WHERE join_date BETWEEN '2024-02-01' AND '2024-02-29';

-- 또는 (가장 권장)
SELECT name, join_date
FROM members
WHERE YEAR(join_date) = 2024 
  AND MONTH(join_date) = 2;
```

</details>

---

### 문제 4
**이름에 '우'가 들어가는 회원을 모두 조회하세요.**

<details>
<summary>정답 보기</summary>

```sql
SELECT name, email
FROM members
WHERE name LIKE '%우%';
```

</details>

---

## 💡 자주 하는 실수

### ❌ 실수 1: WHERE와 ORDER BY 순서

```sql
-- 틀림
SELECT * FROM members 
ORDER BY age 
WHERE age >= 30;  -- ❌ 에러!

-- 맞음
SELECT * FROM members 
WHERE age >= 30 
ORDER BY age;  -- ✅
```

**올바른 순서:** SELECT → FROM → WHERE → ORDER BY → LIMIT

---

### ❌ 실수 2: NULL 비교

```sql
-- 틀림
WHERE email = NULL  -- ❌ 항상 false

-- 맞음
WHERE email IS NULL  -- ✅
```

---

### ❌ 실수 3: 문자열 비교

```sql
-- 틀림
WHERE name = 김동우  -- ❌ 에러!

-- 맞음
WHERE name = '김동우'  -- ✅ 따옴표 필수!
```

---

### ❌ 실수 4: LIMIT 없이 전체 조회

```sql
-- 비효율적
SELECT * FROM orders;  -- 수만 건 조회

-- 효율적
SELECT * FROM orders LIMIT 10;  -- 필요한 만큼만
```

---

## 📊 SQL 실행 순서 이해하기

SQL이 실제로 실행되는 순서:

```
1. FROM    - 어느 테이블에서?
2. WHERE   - 어떤 조건으로?
3. SELECT  - 어떤 컬럼을?
4. ORDER BY - 어떤 순서로?
5. LIMIT   - 몇 개만?
```

**예시:**
```sql
SELECT name, age          -- 3. 이름과 나이를 선택
FROM members              -- 1. members 테이블에서
WHERE age >= 30           -- 2. 30세 이상만
ORDER BY age DESC         -- 4. 나이 내림차순으로
LIMIT 5;                  -- 5. 5명만
```

---

## 🎯 오늘의 정리

### 핵심 키워드
* **SELECT**: 조회할 컬럼 지정
* **WHERE**: 조건 필터링
* **ORDER BY**: 정렬 (ASC/DESC)
* **LIMIT**: 개수 제한
* **DISTINCT**: 중복 제거

### 기억할 것
1. SQL 실행 순서: FROM → WHERE → SELECT → ORDER BY → LIMIT
2. NULL은 `IS NULL` / `IS NOT NULL`로 비교
3. 문자열은 작은따옴표(`'`)로 감싸기
4. 와일드카드: `%` (여러 문자), `_` (한 문자)

---

## 🚀 다음 학습

다음 글에서는 **집계 함수**를 배워볼 예정입니다!

* COUNT, SUM, AVG, MAX, MIN
* GROUP BY로 그룹화
* HAVING으로 그룹 필터링

SELECT로 단순 조회만 했다면,  
집계 함수로 **데이터를 요약하고 분석**하는 법을 배워봅시다! 💪
