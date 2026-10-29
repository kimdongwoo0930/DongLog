[Velog로 가기](https://velog.io/@kimdongwoo0930/Spring-Boot-게시판-REST-API-만들기-JPA-MySQL-계층형-아키텍처)

released at 2026-03-11 22:55:26 KST

updated at 2026-10-28 22:51:45 KST


> 🙋 이 글에서 다루는 것들
> - Spring Boot + JPA + MySQL 기반 게시판 REST API 전체 구조
> - 실무에서 쓰는 계층형 아키텍처 (Controller → Service → Repository)
> - Soft Delete, 전역 예외처리, 공통 응답 포맷, 페이징 처리

---

## 🤔 왜 이 프로젝트를 만들었나

Spring Boot를 공부하다 보면 항상 이런 생각이 든다.

> "튜토리얼은 따라했는데... 실제로 API 하나 처음부터 짜려니 막막하다."

Controller에 비즈니스 로직을 다 때려박거나, Entity를 그대로 Response로 쓰거나, 예외 처리를 try-catch로 구석구석 흩뿌리는 코드를 짜본 사람이라면 공감할 것이다.

이 프로젝트는 **실무에서 자주 사용하는 패턴**을 기반으로 게시판 REST API를 처음부터 끝까지 만들어보는 것을 목표로 했다.

---

## 🎯 프로젝트 스펙

| 분류 | 기술 |
|------|------|
| Language | Java 25 |
| Framework | Spring Boot 4.0.3 |
| ORM | Spring Data JPA, Hibernate |
| Database | MySQL 8.0 |
| Build Tool | Gradle |
| Etc | Lombok, Swagger |

---

## ✨ 핵심 기능

### 1️⃣ 회원 / 게시글 / 댓글 CRUD

도메인을 분리해서 각각 Controller → Service → Repository 계층으로 구성했다.

```
src/main/java/com/kdw/boardapi/
├── domain/
│   ├── member/       # 회원 도메인
│   ├── post/         # 게시글 도메인
│   └── comment/      # 댓글 도메인
└── global/
    ├── exception/    # 전역 예외처리
    ├── response/     # 공통 응답 포맷
    └── config/       # 설정
```

각 도메인 안에는 `controller`, `service`, `repository`, `entity`, `dto` 패키지가 있다.

### 2️⃣ Soft Delete (논리적 삭제)

DB에서 실제로 데이터를 삭제하지 않고, `deleted = true`로 표시만 하는 방식이다.

```java
// ❌ Hard Delete — DB에서 완전히 사라짐 (복구 불가)
memberRepository.delete(member);

// ✅ Soft Delete — deleted 플래그만 변경 (복구 가능)
member.delete(); // deleted = true
```

**장점**
- 데이터 복구 가능
- 삭제 이력 추적 가능
- 연관 데이터 정합성 유지

### 3️⃣ 전역 예외처리 + 공통 응답 포맷

모든 API가 동일한 구조로 응답하고, 예외도 한 곳에서 처리한다.

```json
// 성공
{
  "status": 200,
  "message": "성공",
  "data": { ... }
}

// 실패
{
  "status": 404,
  "message": "존재하지 않는 회원입니다.",
  "data": null
}
```

```java
// 성공 응답
return ApiResponse.success(data);

// 예외 발생 → GlobalExceptionHandler가 잡아서 처리
throw new CustomException(ErrorCode.MEMBER_NOT_FOUND);
```

### 4️⃣ 페이징 처리

게시글 목록 조회는 `Pageable`을 활용해 페이지네이션을 적용했다.

```java
// GET /api/posts?page=0&size=10&sort=createdAt,desc
// page=0 → 1페이지 (0부터 시작)
// size=10 → 10개씩
// sort=createdAt,desc → 최신순
```

| | Page | Slice |
|---|---|---|
| 전체 개수 반환 | ✅ | ❌ |
| 용도 | 페이지네이션 | 무한 스크롤 |

---

## 📦 API 명세

### 회원 API

| Method | URL | 설명 |
|--------|-----|------|
| POST | /api/members | 회원가입 |
| GET | /api/members/{id} | 회원 단건 조회 |
| PATCH | /api/members/{id} | 회원 정보 수정 |
| DELETE | /api/members/{id} | 회원 탈퇴 |

### 게시글 API

| Method | URL | 설명 |
|--------|-----|------|
| POST | /api/posts | 게시글 작성 |
| GET | /api/posts | 게시글 목록 조회 (페이징) |
| GET | /api/posts/{id} | 게시글 단건 조회 |
| PATCH | /api/posts/{id} | 게시글 수정 |
| DELETE | /api/posts/{id} | 게시글 삭제 |

### 댓글 API

| Method | URL | 설명 |
|--------|-----|------|
| POST | /api/posts/{postId}/comments | 댓글 작성 |
| GET | /api/posts/{postId}/comments | 댓글 목록 조회 |
| PATCH | /api/posts/{postId}/comments/{id} | 댓글 수정 |
| DELETE | /api/posts/{postId}/comments/{id} | 댓글 삭제 |

---

## 🛠 주요 개념 정리

### 계층 구조 (Layered Architecture)

```
Controller → Service → Repository → DB
```

| 계층 | 역할 | 주의 |
|------|------|------|
| Controller | 요청/응답 처리 | 비즈니스 로직 절대 X |
| Service | 비즈니스 로직 | DB 직접 접근 X |
| Repository | DB 접근 | 로직 X |
| Entity | DB 테이블 매핑 | 비즈니스 로직 X |
| DTO | 요청/응답 데이터 전달 | Entity 직접 노출 X |

💡 계층 분리의 핵심은 **각 계층이 자기 역할만 한다**는 것이다. Controller에서 `memberRepository.findById()`를 직접 부르는 순간 설계가 무너진다.

---

### DTO를 왜 따로 써야 하나?

Entity를 그대로 Response로 내보내면 어떤 문제가 생기는가?

```java
// ❌ Entity 직접 반환 — password 같은 민감 정보가 노출될 수 있음
@GetMapping("/{id}")
public Member getMember(@PathVariable Long id) {
    return memberRepository.findById(id).orElseThrow();
}

// ✅ DTO로 필요한 필드만 선택해서 반환
@GetMapping("/{id}")
public ApiResponse<MemberResponse> getMember(@PathVariable Long id) {
    return ApiResponse.success(memberService.getMember(id));
}
```

| | Request DTO | Response DTO |
|---|---|---|
| 역할 | 클라이언트 → 서버 | 서버 → 클라이언트 |
| 주요 어노테이션 | `@NoArgsConstructor` | `@Builder`, `@AllArgsConstructor` |
| 특징 | JSON 자동 변환 | `from()` 메서드로 Entity 변환 |

---

### FetchType.LAZY vs EAGER

```java
// ✅ 실무 표준 — 필요할 때만 DB 조회
@ManyToOne(fetch = FetchType.LAZY)
private Member member;

// ❌ 즉시 로딩 — N+1 문제 발생 가능
@ManyToOne(fetch = FetchType.EAGER)
private Member member;
```

> ⚠️ N+1 문제: 게시글 10개를 조회했을 때, 각 게시글의 작성자 정보를 가져오기 위해 SQL이 10번 추가로 실행되는 현상

---

### Optional로 NPE 방지

```java
// ❌ null 반환 → NullPointerException 위험
Member member = memberRepository.findByEmail(email);
member.getNickname(); // 💀

// ✅ Optional → 없으면 예외 발생
Optional<Member> member = memberRepository.findByEmail(email);
member.orElseThrow(() -> new CustomException(ErrorCode.MEMBER_NOT_FOUND));
```

---

### 자주 쓰는 어노테이션 정리

#### Entity 어노테이션

| 어노테이션 | 설명 |
|-----------|------|
| `@Entity` | JPA 엔티티 선언 |
| `@Id` | PK 선언 |
| `@GeneratedValue(strategy = GenerationType.IDENTITY)` | Auto Increment |
| `@Column(nullable = false)` | NOT NULL 제약 |
| `@ManyToOne(fetch = FetchType.LAZY)` | 다대일, 지연 로딩 |
| `@CreationTimestamp` | 생성 시 자동 시간 입력 |
| `@UpdateTimestamp` | 수정 시 자동 시간 업데이트 |

#### Lombok 어노테이션

| 어노테이션 | 설명 |
|-----------|------|
| `@Getter` | 모든 필드 Getter 자동 생성 |
| `@NoArgsConstructor` | 기본 생성자 |
| `@AllArgsConstructor` | 모든 필드 생성자 |
| `@Builder` | Builder 패턴 |
| `@RequiredArgsConstructor` | final 필드 생성자 주입 |

#### Validation 어노테이션

| 어노테이션 | 설명 |
|-----------|------|
| `@NotBlank` | null, 빈문자열, 공백 모두 불가 |
| `@NotNull` | null만 불가 |
| `@Email` | 이메일 형식 검증 |
| `@Size(min=8)` | 문자열 길이 제한 |


---

## 💬 마무리

처음 Spring Boot를 공부할 때 "왜 이렇게 구조가 복잡하지?"라는 생각이 들었다. 근데 직접 짜보니 이해가 됐다.

계층을 분리하면 **수정할 때 어디만 고치면 되는지 명확하게** 보인다. 예외처리를 한 곳에 모아두면 **에러가 나도 일관된 응답**을 보장할 수 있다.

---

## 🔗 링크

- GitHub: https://github.com/kimdongwoo0930/spring-boot-board-api