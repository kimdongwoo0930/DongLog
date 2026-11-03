[Velog로 가기](https://velog.io/@kimdongwoo0930/Git-자주-쓰는-Git-명령어-완전-정복-기본-Branch-Config)

released at 2026-02-22 18:43:28 KST

updated at 2026-11-03 09:50:40 KST

|[branch](https://velog.io/tags/branch)|[git](https://velog.io/tags/git)|[github](https://velog.io/tags/github)|[개발환경](https://velog.io/tags/개발환경)|[버전관리](https://velog.io/tags/버전관리)|
|----|----|----|----|----|



> 개발하면서 매일 쓰는 Git 명령어들을 한 곳에 정리했다.  
> 처음 Git을 접하는 분도, 명령어가 헷갈리는 분도 북마크해두고 참고하세요.

---

## 🤔 Git이란?

**Git**은 소스코드의 변경 이력을 관리하는 **버전 관리 시스템**이다.

**Git을 쓰는 이유:**
- 여러 명이 같은 파일을 동시에 수정해도 충돌 없이 합칠 수 있다
- 언제든 이전 버전으로 되돌아갈 수 있다
- 브랜치로 기능을 독립적으로 개발하고 나중에 합칠 수 있다
- 혼자 개발해도 체계적인 버전 관리가 가능하다

---

## 1️⃣ 기본 명령어

### 저장소 생성 / 복제

```bash
# 현재 폴더를 Git 저장소로 초기화
git init

# 원격 저장소를 로컬로 복제
git clone https://github.com/username/repo.git

# 특정 폴더명으로 복제
git clone https://github.com/username/repo.git my-folder
```

### 상태 확인

```bash
# 현재 변경사항 확인 (수정/추가/삭제된 파일)
git status

# 커밋 히스토리 확인
git log

# 한 줄로 간단하게 확인
git log --oneline

# 브랜치 그래프와 함께 확인
git log --oneline --graph --all
```

### 스테이징 & 커밋

```bash
# 특정 파일 스테이징
git add 파일명

# 전체 파일 스테이징
git add .
git add -A

# 커밋 생성
git commit -m "커밋 메시지"

# 스테이징 + 커밋 한 번에 (새 파일 제외)
git commit -am "커밋 메시지"

# 마지막 커밋 메시지 수정
git commit --amend -m "수정된 메시지"
```

### Push & Pull

```bash
# 원격 저장소에 push
git push
git push origin main

# 원격 저장소에서 pull
git pull
git pull origin main

# 원격 저장소 변경사항만 가져오기 (merge 안 함)
git fetch
```

---

## 2️⃣ Branch 명령어

브랜치는 **독립적인 작업 공간**이다.  
기능 개발, 버그 수정을 메인 코드와 분리해서 진행할 수 있다.

### 브랜치 생성 / 이동

```bash
# 브랜치 목록 확인
git branch

# 원격 브랜치 포함 전체 목록
git branch -a

# 새 브랜치 생성
git branch 브랜치명

# 브랜치 이동
git checkout 브랜치명

# 브랜치 생성 + 이동 한 번에 (자주 씀!)
git checkout -b 브랜치명

# 최신 방식 (git 2.23+)
git switch 브랜치명
git switch -c 브랜치명  # 생성 + 이동
```

### 브랜치 병합 / 삭제

```bash
# 현재 브랜치에 다른 브랜치 병합
git merge 브랜치명

# 브랜치 삭제 (merge 완료 후)
git branch -d 브랜치명

# 브랜치 강제 삭제
git branch -D 브랜치명

# 원격 브랜치 삭제
git push origin --delete 브랜치명
```

### 자주 쓰는 브랜치 전략

```bash
# 1. 기능 개발 시작
git checkout -b feature/로그인기능

# 2. 작업 후 커밋
git add .
git commit -m "feat: 로그인 기능 구현"

# 3. main에 병합
git checkout main
git merge feature/로그인기능

# 4. 병합 후 브랜치 삭제
git branch -d feature/로그인기능
```

---

## 3️⃣ 되돌리기 명령어

### 스테이징 취소

```bash
# 스테이징 취소 (파일 변경사항은 유지)
git restore --staged 파일명
git restore --staged .  # 전체

# 파일 변경사항도 되돌리기 (주의: 복구 불가)
git restore 파일명
```

### 커밋 되돌리기

```bash
# 이전 커밋으로 되돌리기 (히스토리 유지)
git revert HEAD

# 특정 커밋으로 되돌리기 (히스토리 삭제 - 공유 브랜치에서 주의!)
git reset --hard 커밋해시
git reset --soft 커밋해시  # 변경사항은 스테이징에 유지
```

---

## 4️⃣ Config 설정 명령어

Git을 처음 설치하면 **사용자 정보 설정**이 필수다.  
커밋할 때 작성자 정보로 사용된다.

### 기본 설정

```bash
# 전역 사용자 이름 설정
git config --global user.name "홍길동"

# 전역 이메일 설정
git config --global user.email "example@email.com"

# 설정 확인
git config --list
git config user.name  # 특정 항목만 확인
```

### 편집기 설정

```bash
# 기본 편집기를 VSCode로 설정
git config --global core.editor "code --wait"

# vim으로 설정
git config --global core.editor "vim"
```

### 줄바꿈 설정 (Windows ↔ Mac 협업 시 중요!)

```bash
# Windows
git config --global core.autocrlf true

# Mac / Linux
git config --global core.autocrlf input
```

### 설정 파일 직접 열기

```bash
# 전역 설정 파일 열기
git config --global --edit
```

---

## 5️⃣ 유용한 기타 명령어

```bash
# 변경사항 임시 저장 (작업 중간에 브랜치 이동할 때)
git stash

# 임시 저장 목록 확인
git stash list

# 임시 저장 복원
git stash pop

# 특정 파일을 .gitignore에 추가 후 추적 중단
echo "파일명" >> .gitignore
git rm --cached 파일명

# 원격 저장소 확인
git remote -v

# 원격 저장소 추가
git remote add origin https://github.com/username/repo.git
```

---

## 📌 자주 하는 실수

**1. 커밋 전 `git add` 빠뜨리기**
```bash
# ❌ add 없이 commit하면 아무것도 커밋 안 됨
git commit -m "수정"

# ✅ 반드시 add 먼저
git add .
git commit -m "수정"
```

**2. main 브랜치에서 바로 작업**
```bash
# ❌ main에서 직접 작업 (협업 시 위험)
git checkout main
# ... 코딩 ...
git commit -m "기능 추가"

# ✅ 브랜치 분리 후 작업
git checkout -b feature/기능명
# ... 코딩 ...
git commit -m "feat: 기능 추가"
```

**3. `git reset --hard` 후 복구 시도**
> `--hard` 옵션은 변경사항을 완전히 삭제한다.  
> 공유 브랜치(main 등)에서는 절대 사용하지 말 것.

---

## 📌 정리

| 분류 | 명령어 | 설명 |
|------|--------|------|
| 저장소 | `git init` / `git clone` | 생성 / 복제 |
| 상태 | `git status` / `git log` | 확인 |
| 스테이징 | `git add .` | 변경사항 스테이징 |
| 커밋 | `git commit -m ""` | 커밋 생성 |
| 동기화 | `git push` / `git pull` | 원격 저장소 |
| 브랜치 | `git checkout -b` | 생성 + 이동 |
| 병합 | `git merge` | 브랜치 병합 |
| 되돌리기 | `git revert` / `git reset` | 커밋 되돌리기 |
| 임시저장 | `git stash` | 작업 임시 저장 |