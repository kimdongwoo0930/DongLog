[Velog로 가기](https://velog.io/@kimdongwoo0930/IntelliJ-플러그인-사이드바-UI-만들기-ToolWindow-Swing-입문-EP.02)

released at 2026-02-26 20:12:10 KST

updated at 2026-11-01 00:20:30 KST

> **[IntelliJ 플러그인으로 BOJ 정복하기] 시리즈**
> 1. [EP.01] 왜 IntelliJ 플러그인인가? — 개발 배경 & 환경 구축
> 2. **[EP.02] Tool Window — 사이드바 UI 만들기** 👈 현재 글


---

## 🗂 지난 EP 요약

JetBrains 공식 템플릿으로 레포를 만들고 IntelliJ에서 clone까지 했다.  
`runIde`로 실행하면 샌드박스 IntelliJ가 뜨는 것도 확인했다.

이번엔 본격적으로 **사이드바 UI를 만든다.**  
VSCode 확장에서 사이드바에 버튼들이 있던 것처럼, IntelliJ에서도 똑같이 구성할 거다.

---

## 🤔 Tool Window가 뭔가

IntelliJ에서 화면 좌측이나 하단에 붙어있는 패널들을 **Tool Window**라고 부른다.  
프로젝트 탐색기, 터미널, Git 패널 같은 것들이 전부 Tool Window다.

우리가 만들 BOJ 플러그인도 여기에 들어간다.

| VSCode | IntelliJ |
|--------|----------|
| Activity Bar (사이드바 패널) | **Tool Window** |
| `WebviewViewProvider` | **`ToolWindowFactory`** |
| HTML + CSS로 UI 구성 | **Swing 컴포넌트로 구성** |

VSCode는 HTML로 UI를 자유롭게 만들 수 있는데,  
IntelliJ는 **Swing** 이라는 Java GUI 프레임워크를 쓴다.  
처음엔 낯설지만 구조는 단순하다.

---

## 1️⃣ 템플릿 기본 코드 살펴보기

템플릿을 clone하면 `MyToolWindowFactory.kt`가 이미 있다.  
랜덤 숫자를 보여주는 샘플 코드인데, 구조를 먼저 파악하고 갈아엎을 거다.

```kotlin
// 템플릿 기본 코드 (갈아엎기 전)
class MyToolWindowFactory : ToolWindowFactory {
    override fun createToolWindowContent(project: Project, toolWindow: ToolWindow) {
        val myToolWindow = MyToolWindow(toolWindow)
        val content = ContentFactory.getInstance()
            .createContent(myToolWindow.getContent(), null, false)
        toolWindow.contentManager.addContent(content)
    }
}
```

구조는 간단하다:
- `ToolWindowFactory` — Tool Window를 만드는 공장 역할
- `createToolWindowContent()` — 실제 UI를 생성해서 Tool Window에 붙이는 함수
- `ContentFactory` — UI 컴포넌트를 Tool Window에 넣을 수 있는 형태로 포장해주는 역할

---

## 2️⃣ 핵심 API 이해하기

코드를 짜기 전에 쓸 API들을 먼저 알아두자.

### ToolWindowFactory

```kotlin
class MyToolWindowFactory : ToolWindowFactory {
    override fun createToolWindowContent(project: Project, toolWindow: ToolWindow) {
        // 여기서 UI를 만들어서 toolWindow에 붙인다
    }

    override fun shouldBeAvailable(project: Project) = true
    // true = 항상 표시 / false = 특정 조건일 때만 표시
}
```

`ToolWindowFactory`를 구현(implement)하면 IntelliJ가 알아서 이 클래스를 불러와서 Tool Window를 만든다.  
`plugin.xml`에 등록만 해두면 된다. (아래에서 설명)

---

### JBPanel

```kotlin
val panel = JBPanel<JBPanel<*>>()
```

`JBPanel`은 IntelliJ가 제공하는 패널 컴포넌트다.  
Java 표준 라이브러리의 `JPanel`과 거의 같은데, IntelliJ 테마(다크모드 등)를 자동으로 따라간다.

💡 IntelliJ 플러그인 개발할 때는 `JPanel` 대신 `JBPanel`을 쓰는 게 좋다.  
테마가 바뀌어도 UI가 자연스럽게 따라가기 때문이다.

---

### GridLayout

```kotlin
layout = GridLayout(5, 1, 0, 8)
//                  행  열  가로간격  세로간격
```

버튼 5개를 세로로 쭉 나열하는 레이아웃이다.  
`GridLayout(5, 1)`은 5행 1열, 즉 버튼을 세로로 5개 배치한다는 뜻이다.

---

### JButton + addActionListener
```kotlin
add(JButton("📝 새 문제 시작하기").apply {
    cursor = java.awt.Cursor(java.awt.Cursor.HAND_CURSOR) // 마우스 올리면 포인터 변경
    addActionListener {
        // 버튼 클릭 시 실행할 코드
    }
})
```
`addActionListener`로 버튼 클릭 이벤트를 연결한다.
VSCode의 `button.addEventListener('click', ...)` 와 같은 역할이다.
`cursor = java.awt.Cursor(java.awt.Cursor.HAND_CURSOR)`는 버튼 위에 마우스를 올렸을 때 커서를 손가락 모양으로 바꿔준다.
기본값은 화살표 커서라 버튼인지 모를 수 있는데, 이걸 추가하면 훨씬 자연스럽다.

---

### EmptyBorder (여백)

```kotlin
border = EmptyBorder(16, 16, 16, 16)
//                   상  좌  하  우  (px)
```

패널 안쪽에 여백을 준다.  
없으면 버튼이 화면 끝에 딱 붙어버려서 답답해 보인다.

---

## 3️⃣ BOJToolWindow 코드 교체하기

이제 실제로 BOJ용 UI로 갈아엎는다.  
`MyToolWindowFactory.kt` 파일을 아래 코드로 교체하자.

```kotlin
package com.github.kimdongwoo0930.bojpluginforjetbrains.toolWindow

import com.intellij.openapi.project.Project
import com.intellij.openapi.wm.ToolWindow
import com.intellij.openapi.wm.ToolWindowFactory
import com.intellij.ui.components.JBPanel
import com.intellij.ui.content.ContentFactory
import java.awt.GridLayout
import javax.swing.JButton
import javax.swing.border.EmptyBorder

class MyToolWindowFactory : ToolWindowFactory {

    override fun createToolWindowContent(project: Project, toolWindow: ToolWindow) {
        val panel = BOJToolWindow(project).getContent()
        val content = ContentFactory.getInstance().createContent(panel, null, false)
        toolWindow.contentManager.addContent(content)
    }

    override fun shouldBeAvailable(project: Project) = true
}

class BOJToolWindow(private val project: Project) {

    fun getContent() = JBPanel<JBPanel<*>>().apply {
        layout = GridLayout(5, 1, 0, 8)
        border = EmptyBorder(16, 16, 16, 16)

        add(JButton("📝 새 문제 시작하기").apply {
        	cursor = java.awt.Cursor(java.awt.Cursor.HAND_CURSOR)
            addActionListener { /* TODO: 문제 번호 입력 */ }
        })
        add(JButton("👁️ 문제만 먼저 보기").apply {
        	cursor = java.awt.Cursor(java.awt.Cursor.HAND_CURSOR)
            addActionListener { /* TODO: 파일 없이 문제 보기 */ }
        })
        add(JButton("🧪 예제로 채점하기").apply {
        	cursor = java.awt.Cursor(java.awt.Cursor.HAND_CURSOR)	
            addActionListener { /* TODO: 테스트 케이스 실행 */ }
        })
        add(JButton("📂 문제 다시 열기").apply {
        	cursor = java.awt.Cursor(java.awt.Cursor.HAND_CURSOR)
            addActionListener { /* TODO: 문제 다시 보기 */ }
        })
        add(JButton("🚀 제출하기").apply {
        	cursor = java.awt.Cursor(java.awt.Cursor.HAND_CURSOR)
            addActionListener { /* TODO: 제출 */ }
        })
    }
}
```

---

## 4️⃣ plugin.xml 확인하기

Tool Window를 IntelliJ가 인식하려면 `plugin.xml`에 등록돼 있어야 한다.  
JetBrains 템플릿을 쓰면 이미 등록돼 있다.

```xml
<extensions defaultExtensionNs="com.intellij">
    <toolWindow
        factoryClass="...toolWindow.MyToolWindowFactory"
        id="BOJ"
        displayName="BOJ"
        icon="/icons/icon.svg"/>
</extensions>
```

각 속성이 하는 역할:

| 속성 | 역할 |
|------|------|
| `factoryClass` | 어떤 클래스로 Tool Window를 만들지 |
| `id` | Tool Window 고유 식별자 |
| `displayName` | 사이드바에 표시될 이름 |
| `icon` | 사이드바 아이콘 |

💡 `id`와 `displayName`을 `"BOJ"`로 바꿔두면 사이드바에 **"BOJ"** 탭으로 표시된다.

---

## 5️⃣ runIde로 실제 확인하기

코드 교체가 끝났으면 `runIde`로 실행해보자.

```bash
./gradlew runIde
```

샌드박스 IntelliJ가 뜨면 좌측 사이드바에서 **"BOJ"** 탭을 클릭하면 된다.

![](https://velog.velcdn.com/images/kimdongwoo0930/post/0fe800d4-4af9-4866-a581-5bcb4bcbb92b/image.png)


버튼 5개가 세로로 쭉 나열된 패널이 뜨면 성공이다.  
아직 버튼을 눌러도 아무 반응이 없는데, `TODO` 주석 자리를 채워나가는 게 다음 단계다.

---

## ✅ 자주 하는 실수

```
❌ JPanel을 씀
   → IntelliJ 테마(다크모드)를 못 따라감 → UI가 어색하게 보일 수 있음

✅ JBPanel을 쓸 것
   → IntelliJ가 제공하는 컴포넌트는 앞에 JB가 붙은 버전을 쓰는 게 좋다
```

```
❌ plugin.xml에 toolWindow 등록을 빠뜨림
   → runIde 해도 사이드바에 아무것도 안 뜸
   → 에러도 안 나서 왜 안 되는지 모름

✅ plugin.xml의 <extensions> 안에 <toolWindow> 등록 확인
```

---

## 📌 정리

| API | 역할 |
|-----|------|
| `ToolWindowFactory` | Tool Window 생성 진입점 |
| `JBPanel` | IntelliJ 테마를 따르는 패널 컴포넌트 |
| `GridLayout(n, 1)` | n개 버튼을 세로로 배치 |
| `JButton` + `addActionListener` | 버튼 + 클릭 이벤트 |
| `EmptyBorder` | 패널 안쪽 여백 |

> 💡 **핵심 요약**  
> IntelliJ Tool Window는 `ToolWindowFactory`를 구현하고 `plugin.xml`에 등록하면 된다.  
> UI는 Swing 컴포넌트로 구성하고, IntelliJ 전용 컴포넌트(`JBPanel` 등)를 쓰면 테마도 자동으로 따라간다.

---

다음 글에서는 **"📝 새 문제 시작하기"** 버튼에 기능을 붙인다.  
문제 번호를 입력받는 다이얼로그를 띄우고, 폴더와 코드 파일을 자동으로 생성하는 걸 구현할 거다.

🔗 **링크**
- [BOJ-Plugin-for-JetBrains (GitHub)](https://github.com/kimdongwoo0930/BOJ-Plugin-for-JetBrains)
- [IntelliJ Platform SDK — Tool Windows](https://plugins.jetbrains.com/docs/intellij/tool-windows.html)
- [IntelliJ Platform SDK — Swing Components](https://plugins.jetbrains.com/docs/intellij/user-interface-components.html)