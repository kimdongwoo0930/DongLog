[Velog로 가기](https://velog.io/@kimdongwoo0930/VSCode에서-백준-문제를-1초-만에-생성하는-법-BOJ-Extension)

released at 2026-02-16 17:48:12 KST

updated at 2026-11-09 02:00:43 KST

|[algorithm](https://velog.io/tags/algorithm)|[extension](https://velog.io/tags/extension)|[vscode](https://velog.io/tags/vscode)|[백준](https://velog.io/tags/백준)|[알고리즘](https://velog.io/tags/알고리즘)|[코딩테스트](https://velog.io/tags/코딩테스트)|
|----|----|----|----|----|----|


백준 알고리즘 문제를 풀 때마다  똑같은 작업을 반복하고 있지 않나요?


1. 문제 번호로 폴더 생성
2. 파일 생성하고 템플릿 복붙
3. 백준 사이트에서 예제 입력 복사
4. 터미널에서 컴파일 명령어 입력
5. 입력 리다이렉션 설정...

**이 모든 과정을 단 1초로 줄일 수 있다면?**

> 그래서 만들었습니다.  
> **VSCode 내부에서 백준 문제 생성부터 테스트, 정답 제출까지 한 번에**

---

## 🤔 왜 만들게 되었나

백준 문제를 풀면서 느낀 불편함들:

* **문제 보려고 브라우저 ↔ VSCode 계속 왔다갔다**
* **테스트는 직접 제출해봐야 결과를 알 수 있음**
* **예제 입력 복사해서 직접 넣어줘야 함**

특히 짧은 문제 여러 개 풀 때는  
화면 전환하는 것만으로도 집중력이 깨진다.

**"VSCode 안에서 문제 보고, 바로 테스트할 수 없을까?"**

그래서 VSCode Extension을 직접 만들기로 했다.

---

## 🎯 목표

### Before: 브라우저와 VSCode 왔다갔다

1. 브라우저에서 문제 읽기
2. VSCode로 전환해서 코딩
3. 다시 브라우저로 가서 예제 확인
4. VSCode로 돌아와서 수정
5. 백준에 제출해서 결과 확인

**문제점**: 화면 전환 피로 + 테스트 피드백 느림

### After: VSCode 안에서 모든 것 해결 

![](https://velog.velcdn.com/images/kimdongwoo0930/post/705a0aba-5f5f-4030-9d0f-2f1930ab04ed/image.png)

1. 사이드바에서 문제 번호 입력
2. 문제 보면서 바로 코딩
3. 테스트 버튼 클릭 → 즉시 결과 확인
4. 통과하면 백준에 제출

**장점**: 집중력 유지 + 빠른 피드백 

---

## ✨ 핵심 기능

### 1️⃣ 문제 자동 생성

문제 번호만 입력하면:
- 폴더 자동 생성
- 언어별 템플릿 자동 삽입
- 예제 입력 자동 저장
- 문제 설명 WebView로 표시

![](https://velog.velcdn.com/images/kimdongwoo0930/post/714f574f-5d9b-434b-a973-6c8f406b1c4c/image.png)

### 2️⃣ 테스트 자동 실행

예제 케이스를 자동으로:
- 컴파일 (C/C++/Java)
- 실행
- 정답 비교
- 결과 표시

![](https://velog.velcdn.com/images/kimdongwoo0930/post/f5fe027e-6482-48aa-a025-981b6d353eee/image.png)


### 3️⃣ 다중 언어 지원

| 언어 | 컴파일러/인터프리터 | 지원 여부 |
|------|---------------------|-----------|
| C11 | gcc | ✅ |
| C++17 | g++ | ✅ |
| Python | python3 | ✅ |
| Java | javac | ✅ |
| JavaScript | node | ✅ |

---

## 📦 설치 및 사용법

### 설치

#### VSCode / code-server 모두 지원! 🌐

이 Extension은 **로컬 VSCode**는 물론  
**code-server**(브라우저 기반 VSCode)에서도 동일하게 작동합니다.

| 환경 | 마켓플레이스 | 설치 방법 |
|------|-------------|-----------|
| VSCode (로컬) | Microsoft Extension Marketplace | Extension 탭에서 "BOJ Extension" 검색 |
| code-server | Open VSX Registry | Extension 탭에서 "BOJ Extension" 검색 |

> 💡 **Tip**: code-server를 사용하면 iPad나 태블릿에서도  
> 같은 개발 환경으로 백준 문제를 풀 수 있어요!

#### 주요 명령어

| Command | 설명 |
|---------|------|
| 파일 생성 및 문제 보기 | 문제 번호 입력 → 자동 세팅 |
| 파일 생성 없이 문제 보기 | 문제 미리보기 |
| 현재 문제 보기 | 현재 문제 다시 표시 |
| 테스트 | 예제 테스트 실행 |
| 제출하기 | 코드 복사 및 제출창 열기 |


---


## 🛠 어떻게 만들었나

### 기술 스택

```
TypeScript + VSCode Extension API
├── Cheerio (백준 크롤링)
├── Child Process (언어별 실행)
└── WebView (문제 표시)
```

### 아키텍처

```
사용자 입력 (문제 번호)
    ↓
백준 크롤링 (문제 정보 파싱)
    ↓
파일 시스템 (폴더/파일 생성)
    ↓
WebView (문제 표시)
    ↓
Command (테스트 실행)
```

---


## 🚧 앞으로 개선할 점


### 단기 계획
-  언어별 템플릿 커스터마이징
-  버그 수정

### 장기 계획
-  다른 알고리즘 사이트 지원 (프로그래머스 등)
-  다른 언어 지원
-  문제 난이도별 필터링
-  풀이 통계 및 대시보드

---

## 💬 마무리하며

처음으로 VSCode Extension을 만들어봤는데  
**생각보다 훨씬 재미있었다.**

처음에는 "내가 쓸 도구"를 만든다는 생각으로 시작했는데  
Extension API 문서를 읽고  
TypeScript로 구조를 설계하고  
실제로 동작하는 걸 보니 뿌듯했다.

> **완성도보다는 "일단 만들어보자"가 중요했다.**

아직 부족한 부분이 많지만  
계속 개선해나갈 예정이다.

---

## 🔗 링크

- **GitHub Repository**: [https://github.com/kimdongwoo0930/BOJ-Extenstion-for-VSCode](https://github.com/kimdongwoo0930/BOJ-Extenstion-for-VSCode)
- **VSCode Marketplace**: [BOJ Extension for VSCode](https://marketplace.visualstudio.com/items?itemName=Dong.boj-extension-for-vscode)
- **피드백 & 버그 리포트**: [GitHub Issues](https://github.com/kimdongwoo0930/BOJ-Extenstion-for-VSCode/issues)

피드백은 언제나 환영입니다! 🙌