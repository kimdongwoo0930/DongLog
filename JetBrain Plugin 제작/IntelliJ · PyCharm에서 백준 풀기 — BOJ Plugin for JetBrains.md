[Velog로 가기](https://velog.io/@kimdongwoo0930/IntelliJ-PyCharm에서-백준-풀기-BOJ-Plugin-for-JetBrains)

released at 2026-03-01 18:44:25 KST

updated at 2026-11-13 04:36:26 KST

|[IntelliJ](https://velog.io/tags/IntelliJ)|[boj](https://velog.io/tags/boj)|[jetbrains](https://velog.io/tags/jetbrains)|[pycharm](https://velog.io/tags/pycharm)|[백준](https://velog.io/tags/백준)|[알고리즘](https://velog.io/tags/알고리즘)|[플러그인](https://velog.io/tags/플러그인)|
|----|----|----|----|----|----|----|

## 😤 IntelliJ 쓰면서 백준 풀 때마다 이 짓 반복하지 않나?

1. 브라우저 열고 백준 접속해서 문제 읽기
2. IDE로 전환해서 패키지 폴더 직접 생성
3. 파일 만들고 템플릿 직접 작성
4. 예제 입력 복붙해서 터미널에서 실행
5. 제출하러 또 브라우저로...

VSCode 쓸 때는 내가 만든 [BOJ Extension for VSCode](https://marketplace.visualstudio.com/items?itemName=Dong.boj-extension-for-vscode) 덕분에 이게 전부 자동화됐는데,  
**IntelliJ랑 PyCharm 쓰는 사람들은 어떡하지?**

> 그래서 만들었다.  
> **JetBrains IDE 안에서 백준 문제 생성부터 테스트, 제출까지 한 번에**

---

## 🤔 왜 만들게 되었나

Java로 알고리즘 공부를 시작하면서 IntelliJ를 주로 쓰게 됐다.  
근데 VSCode 확장은 당연히 IntelliJ에서 못 쓴다.

매번 같은 불편함이 반복됐다.

* **문제 보려고 브라우저 ↔ IDE 계속 왔다갔다**
* **폴더랑 파일 매번 직접 만들어야 함**
* **예제 입력 복붙해서 터미널에 직접 넣어줘야 함**
* **제출도 결국 브라우저로 나가야 함**

**"VSCode 확장이랑 똑같은 걸 JetBrains에서도 쓸 수 없을까?"**

그래서 Kotlin으로 IntelliJ 플러그인을 직접 만들었다.

---

## 🎯 Before / After

### Before: 브라우저와 IDE 사이를 왔다갔다

1. 브라우저에서 문제 읽기
2. IDE로 전환해서 코딩
3. 다시 브라우저로 가서 예제 확인
4. IDE로 돌아와서 터미널에서 직접 실행
5. 백준에 제출해서 결과 확인

**문제점**: 화면 전환 피로 + 테스트 피드백 느림

### After: IDE 안에서 모든 것 해결

![](https://velog.velcdn.com/images/kimdongwoo0930/post/1d64614f-bf60-43ae-8435-b29cfab6dcda/image.png)


1. Tool Window에서 문제 번호 입력
2. 문제 보면서 바로 코딩
3. 채점 버튼 클릭 → 즉시 결과 확인
4. 통과하면 바로 제출

**장점**: 집중력 유지 + 빠른 피드백

---

## ✨ 핵심 기능

### 1️⃣ 문제 번호 입력 → 자동 생성

문제 번호만 입력하면:
- 폴더 자동 생성
- 언어별 템플릿 파일 자동 생성
- 예제 입력 자동 저장

![](https://velog.velcdn.com/images/kimdongwoo0930/post/faa1c0cd-0ee9-4492-98f5-60d8e791da72/image.png)


### 2️⃣ 문제 WebView 표시

백준 문제를 IDE 안에서 바로 확인할 수 있다.  
브라우저로 나갈 필요 없이 Tool Window에서 문제를 보면서 바로 코딩하면 된다.

![](https://velog.velcdn.com/images/kimdongwoo0930/post/e5c20b69-44d7-4871-8a51-f7eeaea9a8ad/image.png)


### 3️⃣ 예제 자동 채점

예제 케이스를 자동으로:
- 컴파일 (Java, C/C++)
- 실행
- 정답 비교
- 결과 표시

![](https://velog.velcdn.com/images/kimdongwoo0930/post/f6e262d0-f270-4296-802f-b26ad532f2fe/image.png)


### 4️⃣ 다중 언어 지원

| 언어 | 지원 여부 |
|------|-----------|
| Java | ✅ |
| Python | ✅ |
| C11 | ✅ |
| C++17 | ✅ |
| JavaScript | ✅ |

### 5️⃣ 백준에 제출

채점 통과 후 제출 버튼을 누르면:
- 코드 자동 복사
- 백준 제출창 자동 열기

![](https://velog.velcdn.com/images/kimdongwoo0930/post/9b119654-4c65-4499-b429-405b11ac7e00/image.png)


---

## 📦 설치 및 사용법

### 설치

#### IntelliJ IDEA / PyCharm 모두 지원! 🎉

| 환경 | 설치 방법 |
|------|-----------|
| IntelliJ IDEA | Plugins 탭에서 "BOJ Plugin" 검색 |
| PyCharm | Plugins 탭에서 "BOJ Plugin" 검색 |

> `File → Settings → Plugins → Marketplace` 에서 **BOJ Plugin for JetBrains** 검색

#### 주요 기능

| 버튼 | 설명 |
|------|------|
| 📝 새 문제 시작하기 | 문제 번호 입력 → 파일 자동 생성 + 문제 표시 |
| 👁️ 문제만 먼저 보기 | 파일 생성 없이 문제만 확인 |
| 🧪 예제로 채점하기 | 예제 케이스 자동 실행 + 결과 비교 |
| 📂 문제 다시 열기 | 현재 문제 다시 표시 |
| 🚀 제출하기 | 코드 복사 + 제출창 열기 |

---

## 🛠 어떻게 만들었나

### 기술 스택

```
Kotlin + IntelliJ Platform SDK
├── JCEF (문제 WebView 렌더링)
├── Tool Window API (사이드바 UI)
├── Action API (버튼 기능)
└── Process Builder (언어별 실행)
```

### 아키텍처

```
사용자 입력 (문제 번호)
    ↓
백준 스크래핑 (문제 정보 파싱)
    ↓
파일 시스템 (폴더/파일 생성)
    ↓
JCEF WebView (문제 표시)
    ↓
Process Builder (테스트 실행)
```


---

## 💬 마무리하며

VSCode 확장을 만들면서 "IntelliJ 쓰는 사람들도 쓸 수 있으면 좋겠다"는 생각으로 시작했다.  
Kotlin도 배우면서 만들다 보니 시간이 걸렸지만, 실제로 동작하는 걸 보니 뿌듯했다.

IntelliJ 플러그인 개발 한국어 자료가 거의 없어서 혼자 삽질을 많이 했다.

> 피드백은 언제나 환영입니다! 🙌

---

## 🔗 링크

- **GitHub**: [BOJ-Plugin-for-JetBrains](https://github.com/kimdongwoo0930/BOJ-Plugin-for-JetBrains)
- **JetBrains Marketplace**: (출시 예정)
- **피드백 & 버그 리포트**: [GitHub Issues](https://github.com/kimdongwoo0930/BOJ-Plugin-for-JetBrains/issues)
- **VSCode 버전**: [BOJ Extension for VSCode](https://marketplace.visualstudio.com/items?itemName=Dong.boj-extension-for-vscode)