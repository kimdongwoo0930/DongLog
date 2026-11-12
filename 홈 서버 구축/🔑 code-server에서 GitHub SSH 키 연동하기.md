[Velog로 가기](https://velog.io/@kimdongwoo0930/code-server에서-GitHub-SSH-키-연동하기)

released at 2026-01-22 19:21:57 KST

updated at 2026-11-12 23:57:30 KST

|[code-server](https://velog.io/tags/code-server)|[docker](https://velog.io/tags/docker)|[github](https://velog.io/tags/github)|[ssh](https://velog.io/tags/ssh)|
|----|----|----|----|

![](https://velog.velcdn.com/images/kimdongwoo0930/post/047576f9-9f9a-4216-82c8-610e8405e15e/image.png)


code-server는 서버 환경이기 때문에
GitHub를 사용할 때 **SSH** 키 방식이 가장 깔끔하고 안전하다.

이 글에서는 비밀번호·토큰 없이
`git clone / pull / push`를 사용할 수 있도록 설정한다.

---

## 🎯 목표
```text 
code-server (Docker)
   🔑 SSH 개인키
        ↓
GitHub (공개키 등록)

```

- 비밀번호 입력 ❌
- Personal Access Token ❌
- SSH 키로 자동 인증 ✅
---

## 1️⃣ code-server 터미널 열기

브라우저에서 code-server 접속 → **Terminal** 실행

---

## 2️⃣ SSH 키 생성

```bash
ssh-keygen -t ed25519 -C "github@code-server"
```
> github@code-server는 키를 구분하기 위한 **메모(comment)**일 뿐이며,
실제 인증에는 사용되지 않는다.

질문이 나오면 전부 Enter
```text
Enter file in which to save the key: (Enter)
Enter passphrase (empty for no passphrase): (Enter)
Enter same passphrase again: (Enter)

```
👉 생성 결과
```text
~/.ssh/id_ed25519       # 개인키 (절대 외부 공유 ❌)
~/.ssh/id_ed25519.pub   # 공개키 (GitHub에 등록)

```
---
## 3️⃣ 공개키 확인 및 복사
`/.ssh/id_ed25519.pub` 파일을 열어 한 줄 전체를 복사한다.
(`ssh-ed25519 ....`)

---
## 4️⃣ GitHub에 SSH 키 등록

**1. GitHub 접속
2. Settings → SSH and GPG keys
3. New SSH key
4. Title: `code-server`
5. Key: 복사한 공개키 붙여넣기
6. Add SSH key**

---

## 5️⃣ GitHub 연결 테스트
```bash
ssh -T git@github.com
```
처음 실행 시 아래 메시지가 출력된다.
```text
Are you sure you want to continue connecting (yes/no)?
```
`yes`를 입력하면, 성공 시 다음과 같은 메시지가 출력된다.
```text
You've successfully authenticated, but GitHub does not provide shell access.
```

---


## 6️⃣ 이제 이렇게 사용하면 된다

```bash
git clone git@github.com:username/repository.git
git pull
git push

```
- 인증 요청 ❌
- 토큰 입력 ❌
- 자동 인증 ✅

---

## ✅ 정리

code-server와 같은 서버 환경에서는
**SSH 키를 한 번만 설정하면 GitHub 인증 과정을 완전히 생략할 수 있어**
가장 효율적이고 안정적인 방식이다.
