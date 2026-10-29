[Velog로 가기](https://velog.io/@kimdongwoo0930/BOJ-Plugin-for-JetBrains-개발기-EP.01-프로젝트-시작하기)

released at 2026-02-26 19:31:16 KST

updated at 2026-10-30 01:53:48 KST

|[IntelliJ](https://velog.io/tags/IntelliJ)|[IntelliJ Plugin](https://velog.io/tags/IntelliJ-Plugin)|[boj](https://velog.io/tags/boj)|[gradle](https://velog.io/tags/gradle)|[jetbrains](https://velog.io/tags/jetbrains)|[kotlin](https://velog.io/tags/kotlin)|[백준](https://velog.io/tags/백준)|
|----|----|----|----|----|----|----|

> **[IntelliJ 플러그인으로 BOJ 정복하기] 시리즈**
> 1. **[EP.01] 개발 배경 & 프로젝트 시작하기** 

---

## 🛠 먼저, VSCode 확장 얘기부터

예전에 백준 문제 풀 때 불편해서 **BOJ Extension for VSCode**를 직접 만들었다.

기능은 이렇다:
- 문제 번호 입력 → 폴더 + 코드 파일 자동 생성
- VSCode 안에서 백준 문제 바로 보기 (WebView)
- 예제 입력 자동 실행 + 결과 비교 (C, C++, Python, Java, JS 지원)
- 백준에 직접 제출

VSCode를 쓰는 사람한테는 꽤 유용한 도구인데…  
**IntelliJ 쓰는 사람들은 어떡하지?**

알고리즘 공부할 때 Java 쓰는 사람은 대부분 IntelliJ를 쓴다.  
근데 VSCode 확장은 IntelliJ에서 못 쓴다.

> "IntelliJ 버전도 만들면 되겠다."

그래서 시작했다.

---

## 🤔 그런데 IntelliJ 플러그인, VSCode랑 얼마나 다를까?

막상 시작하려니 모르는 게 많았다.  
VSCode 확장은 TypeScript로 짰는데, IntelliJ 플러그인은 Java다.  
구조도 다르고, 설정 방식도 다르다.

| 항목 | VSCode Extension | IntelliJ Plugin |
|------|-----------------|-----------------|
| 개발 언어 | TypeScript / JavaScript | **Java 또는 Kotlin** |
| 빌드 도구 | npm | **Gradle** |
| UI 구성 | HTML + WebView | **Swing / JCEF** |
| 기능 등록 방식 | `package.json`에 명시 | **`plugin.xml`에 등록** |
| 국내 자료 | 꽤 있음 | **거의 없음** 😅 |

마지막 줄이 제일 문제였다.  
IntelliJ 플러그인 개발 한국어 자료가 정말 없다. 검색해도 나오는 글이 손에 꼽힌다.

그래서 이 시리즈를 쓰기로 했다.  
같은 이유로 만들려는 사람한테 조금이라도 도움이 됐으면 해서.

---

## 1️⃣ 개발 환경 준비

### 필요한 것

- **IntelliJ IDEA** (Community 버전도 OK)
- **JDK 17 이상**
- **Git**

💡 Gradle은 따로 설치 안 해도 된다. 프로젝트 안에 `gradlew`가 포함돼 있어서 자동으로 처리된다.

---

## 2️⃣ JetBrains 공식 템플릿으로 시작하기

IntelliJ 플러그인을 처음부터 직접 세팅하면 설정할 게 꽤 많다.  
다행히 JetBrains에서 **공식 템플릿 레포**를 제공한다.

GitHub Actions CI/CD, 버전 관리, 기본 구조까지 전부 미리 잡혀있다.  
처음 시작할 때 이걸 안 쓸 이유가 없다.

### 레포 생성

**[github.com/JetBrains/intellij-platform-plugin-template](https://github.com/JetBrains/intellij-platform-plugin-template)** 접속

초록색 **"Use this template"** → **"Create a new repository"** 클릭

![](https://velog.velcdn.com/images/kimdongwoo0930/post/2e541a2f-3bfa-4fb8-bfed-105bee493197/image.png)


아래처럼 입력하고 **"Create repository"** 클릭:

| 항목 | 입력값 |
|------|--------|
| Repository name | `BOJ-Plugin-for-JetBrains` |
| Description | `백준 온라인 저지(BOJ)를 JetBrains IDE에서 사용할 수 있는 플러그인` |
| 공개 여부 | Public |



---

## 3️⃣ IntelliJ에서 Clone하기

레포가 만들어지면 IntelliJ로 불러온다.

IntelliJ 시작 화면 → **"저장소 복제 (Get from VCS)"** 클릭

![](https://velog.velcdn.com/images/kimdongwoo0930/post/a747eb2e-18bc-45fd-9de6-7d8c23d88490/image.png)


레포 URL 입력 후 Clone.

```
https://github.com/[내_깃헙_아이디]/BOJ-Plugin-for-JetBrains.git
```

처음 열면 Gradle이 의존성 다운로드를 시작한다. 시간이 좀 걸리니 기다리면 된다.

---

## 4️⃣ plugin.xml 살펴보기

`src/main/resources/META-INF/plugin.xml`을 열어보자.

VSCode의 `package.json`처럼, **플러그인의 모든 정보가 여기서 시작한다.**  
이름, 버전, 어떤 기능을 쓸 수 있는지 전부 여기에 등록한다.

```xml
<idea-plugin>
    <id>org.example.myintellijplugin</id>   <!-- 플러그인 고유 ID -->
    <name>My IntelliJ Platform Plugin</name>
    <vendor>Plugin Vendor</vendor>

    <!-- 이 플러그인이 의존하는 IntelliJ 모듈 -->
    <depends>com.intellij.modules.platform</depends>

    <extensions defaultExtensionNs="com.intellij">
        <!-- Tool Window, 설정 패널 등 확장 기능 등록 -->
    </extensions>

    <actions>
        <!-- 메뉴/단축키로 실행할 Action 등록 -->
    </actions>
</idea-plugin>
```

지금은 비어있지만 다음 EP부터 하나씩 채워나간다.

⚠️ **`<id>` 지금 바꿔두는 게 좋다.**  
기본값인 `org.example.myintellijplugin` 그대로 두면 나중에 마켓플레이스 등록할 때 문제가 생긴다.  
역순 도메인 형식으로 바꿔두자.

```xml
<!-- 변경 전 -->
<id>org.example.myintellijplugin</id>

<!-- 변경 후 -->
<id>com.kimdongwoo.boj-plugin-for-jetbrains</id>
```

---

## 5️⃣ runIde로 첫 실행

세팅이 끝났으면 실제로 동작하는지 확인하자.

오른쪽 **Gradle 패널** → Tasks → intellij → **`runIde`** 더블클릭

```bash
# 터미널에서 실행할 경우
./gradlew runIde
```

![](https://velog.velcdn.com/images/kimdongwoo0930/post/2b9a93f1-76aa-4b63-8e8f-51b548dcc4a2/image.png)


실행하면 **새 IntelliJ 인스턴스가 하나 더 뜬다.**  
이게 플러그인이 설치된 샌드박스 환경이다. 처음 보면 당황스럽지만 정상이다.

![](https://velog.velcdn.com/images/kimdongwoo0930/post/bcddfd92-6c23-4692-ad81-8cf67a3a6004/image.png)


아직 아무 기능도 없어서 평범한 IntelliJ처럼 보인다.  
다음 EP에서 Action을 추가하면 여기서 바로 확인할 수 있다.

---

## ✅ 자주 하는 실수

```
❌ plugin.xml의 <id>를 기본값 그대로 둠
   → "org.example.myintellijplugin" 채로 배포하면 마켓플레이스 등록 불가

✅ 처음부터 com.{이름}.{플러그인명} 형식으로 바꿔두기
```

```
❌ JDK 11로 세팅함
   → 최신 IntelliJ Plugin SDK는 JDK 17 이상 요구 → 빌드 에러

✅ JDK 17 이상으로 설정
   → gradle.properties에서 javaVersion 항목 확인
```

---

## 📌 정리

| 항목 | 내용 |
|------|------|
| 개발 언어 | Java 17+ (또는 Kotlin) |
| 빌드 도구 | Gradle |
| 시작 방법 | JetBrains 공식 템플릿 레포 사용 |
| 핵심 파일 | `plugin.xml`, `gradle.properties` |
| 실행 확인 | `runIde` Gradle 태스크 |

> 💡 **핵심 요약**  
> IntelliJ 플러그인 개발, 어렵게 느껴지지만 JetBrains 공식 템플릿으로 시작하면 초기 설정 없이 바로 시작할 수 있다.  
> `plugin.xml`만 이해하면 절반은 된 거다.

---

다음 글에서는 **Action API**로 문제 번호 입력창을 만들고,  
폴더와 코드 파일을 자동 생성하는 기능을 구현한다.

🔗 **링크**
- [BOJ Extension for VSCode (GitHub)](https://github.com/kimdongwoo0930/BOJ-Extenstion-for-VSCode)
- [BOJ Extension for VSCode (Marketplace)](https://marketplace.visualstudio.com/items?itemName=Dong.boj-extension-for-vscode)
- [BOJ-Plugin-for-JetBrains (GitHub)](https://github.com/kimdongwoo0930/BOJ-Plugin-for-JetBrains)
- [IntelliJ Platform SDK 공식 문서](https://plugins.jetbrains.com/docs/intellij/welcome.html)
- [JetBrains intellij-platform-plugin-template](https://github.com/JetBrains/intellij-platform-plugin-template)