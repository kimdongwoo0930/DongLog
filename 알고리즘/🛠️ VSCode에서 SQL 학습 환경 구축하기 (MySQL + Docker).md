[Velog로 가기](https://velog.io/@kimdongwoo0930/VSCode에서-SQL-학습-환경-구축하기-MySQL-Docker)

released at 2026-02-16 18:17:17 KST

updated at 2026-11-13 17:01:05 KST

|[algorithm](https://velog.io/tags/algorithm)|[mysql](https://velog.io/tags/mysql)|[sql](https://velog.io/tags/sql)|[vscode](https://velog.io/tags/vscode)|[알고리즘](https://velog.io/tags/알고리즘)|
|----|----|----|----|----|


SQL을 제대로 공부하려면  
직접 쿼리를 작성하고 실행해보는 게 가장 중요하다.

하지만 막상 시작하려니...

* MySQL 설치는 어떻게 하지?
* 데이터베이스는 어떻게 만들지?
* VSCode에서 SQL 실행이 되나?

**이 모든 걸 한 번에 해결해보자.**

---

## 🤔 왜 이 환경인가?

### Docker + MySQL + VSCode

* **Docker**: MySQL 설치 없이 컨테이너로 간편하게
* **MySQL**: 실무에서 가장 많이 쓰는 DB
* **VSCode**: 익숙한 에디터에서 모든 작업

> 브라우저 왔다갔다 할 필요 없이  
> VSCode 하나로 SQL 작성부터 실행까지!

---

## 📋 사전 준비

### 필요한 것

*  Docker Desktop 설치
*  VSCode 설치
*  인터넷 연결

---

## 1️⃣ Docker로 MySQL 실행하기

### docker-compose.yml 파일 생성

프로젝트 폴더를 만들고 `docker-compose.yml` 파일 생성:

```bash
mkdir sql-practice
cd sql-practice
```

`docker-compose.yml` 파일 작성:

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql-practice
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: 1234
      MYSQL_DATABASE: sql_practice
      MYSQL_CHARACTER_SET_SERVER: utf8mb4
      MYSQL_COLLATION_SERVER: utf8mb4_unicode_ci
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    command:
      - --default-authentication-plugin=mysql_native_password
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci

volumes:
  mysql_data:
    driver: local
```

**설정 설명:**

| 옵션 | 설명 |
|------|------|
| `image: mysql:8.0` | MySQL 8.0 버전 사용 |
| `container_name` | 컨테이너 이름 지정 |
| `restart: unless-stopped` | 재부팅 시 자동 실행 |
| `MYSQL_ROOT_PASSWORD` | root 비밀번호 |
| `MYSQL_DATABASE` | 자동 생성할 DB 이름 |
| `MYSQL_CHARACTER_SET_SERVER` | UTF-8 한글 지원 |
| `ports: 3306:3306` | 호스트와 컨테이너 포트 연결 |
| `volumes` | 데이터 영구 저장 |
| `command` | 인증 방식 및 문자셋 설정 |

### MySQL 실행

```bash
docker-compose up -d
```

### 실행 확인

```bash
# 컨테이너 상태 확인
docker-compose ps

# 로그 확인
docker-compose logs -f mysql
```


MySQL이 정상적으로 시작되면 로그에 `ready for connections`가 표시됨!

---

## 2️⃣ VSCode Extension 설치

### MySQL Extension 설치

1. VSCode Extension 탭 열기
2. "MySQL" 검색
3. **"MySQL"**  설치

![](https://velog.velcdn.com/images/kimdongwoo0930/post/eb2c73e9-3ebb-4403-ad41-640f28cb0488/image.png)


### 데이터베이스 연결

1. 사이드바에 MySQL 아이콘 클릭
2. **"+"** 버튼 클릭
3. 연결 정보 입력:
![](https://velog.velcdn.com/images/kimdongwoo0930/post/4b1d784e-52ab-4d77-837d-8b6f8b9bca94/image.png)


**연결 정보:**
- 호스트: `localhost` (또는 `IP`)
- 포트: `3306`
- 사용자 이름: `root`
- 비밀번호: `1234`

4. **저장** 버튼 클릭

연결 성공하면 `sql_practice` DB가 보임!

---

## 3️⃣ 실습용 테이블 생성

### 새 SQL 파일 만들기

VSCode에서 `setup.sql` 파일 생성

### 테이블 생성 쿼리

```sql
-- sql_practice 데이터베이스 선택
USE sql_practice;

-- 회원 테이블 생성
CREATE TABLE members (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    age INT,
    email VARCHAR(100),
    join_date DATE
);

-- 주문 테이블 생성
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    member_id INT,
    product_name VARCHAR(100),
    price INT,
    order_date DATE,
    FOREIGN KEY (member_id) REFERENCES members(id)
);
```

### 샘플 데이터 INSERT

```sql
-- 회원 데이터
INSERT INTO members (name, age, email, join_date) VALUES
('김동우', 25, 'dongwoo@example.com', '2024-01-15'),
('홍길동', 30, 'hong@example.com', '2024-01-20'),
('이영희', 28, 'lee@example.com', '2024-02-01'),
('박철수', 35, 'park@example.com', '2024-02-05');
...

-- 주문 데이터
INSERT INTO orders (member_id, product_name, price, order_date) VALUES
(1, '노트북', 1500000, '2024-02-10'),
(1, '마우스', 50000, '2024-02-11'),
(2, '키보드', 100000, '2024-02-12'),
(3, '모니터', 300000, '2024-02-13'),
(1, '헤드셋', 80000, '2024-02-14'),
(4, '웹캠', 120000, '2024-02-15');
...
```

### 실행하기

1. 쿼리 전체 선택 (`Ctrl + A`)
2. 실행

실행 결과가 VSCode 하단에 표시됨!

---

## 4️⃣ 첫 SELECT 문 실행

### 테스트 쿼리 작성

`test.sql` 파일을 새로 만들고:

```sql
USE sql_practice;

-- 전체 회원 조회
SELECT * FROM members;

-- 주문 내역 조회
SELECT * FROM orders;

-- 회원별 주문 금액 합계
SELECT 
    m.name,
    COUNT(o.order_id) AS order_count,
    SUM(o.price) AS total_price
FROM members m
LEFT JOIN orders o ON m.id = o.member_id
GROUP BY m.id, m.name
ORDER BY total_price DESC;
```

각 쿼리를 선택하고 `Ctrl + Enter`로 실행!

**실행 결과:**

![](https://velog.velcdn.com/images/kimdongwoo0930/post/3aee3068-cb19-4de1-b288-dfd44c6764ee/image.png)


---

## 5️⃣ 유용한 팁

### 단축키

| 단축키 | 기능 |
|--------|------|
| `Ctrl + Enter` | 선택한 쿼리 실행 |
| `Ctrl + A` | 전체 선택 |
| `Ctrl + /` | 주석 토글 |

### 자동완성 활용

* 테이블명, 컬럼명 자동완성 지원
* `Tab`으로 자동완성 선택

### 쿼리 히스토리

* MySQL Extension에서 실행한 쿼리 이력 확인 가능
* 이전 쿼리 재실행 가능

---

## 💡 다음 단계

이제 환경 구축이 끝났으니  
본격적으로 SQL 학습을 시작해보자!

### 학습 순서

1. **SELECT 기초** (WHERE, ORDER BY, LIMIT)
2. **집계 함수** (COUNT, SUM, AVG, GROUP BY)
3. **JOIN** (INNER, LEFT, RIGHT)
4. **서브쿼리**
5. **실전 문제 풀이** (프로그래머스, LeetCode)

---

## 🔗 참고 자료

* **Docker 공식 문서**: [https://docs.docker.com/](https://docs.docker.com/)
* **MySQL 공식 문서**: [https://dev.mysql.com/doc/](https://dev.mysql.com/doc/)
* **프로그래머스 SQL 고득점 Kit**: [링크](https://school.programmers.co.kr/learn/challenges)

---

## 💬 마무리

처음 환경 구축할 때는 복잡해 보이지만  
**한 번만 세팅하면 계속 편하게 쓸 수 있다.**

Docker 덕분에:
* MySQL 설치/삭제 걱정 없음
* 언제든 초기화 가능
* 다른 프로젝트와 충돌 없음

VSCode 덕분에:
* 익숙한 환경에서 작업
* 브라우저 왔다갔다 안 해도 됨
* 스크린샷 찍기 편함

> **이제 본격적으로 SQL 학습 시작!**

다음 글에서는 SELECT 기초부터 천천히 정복해보자! 🚀
