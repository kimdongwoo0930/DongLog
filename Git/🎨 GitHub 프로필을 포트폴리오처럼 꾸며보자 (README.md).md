[Velog로 가기](https://velog.io/@kimdongwoo0930/GitHub-프로필을-포트폴리오처럼-꾸며보자-README.md)

released at 2026-02-01 17:17:11 KST

updated at 2026-11-12 03:11:46 KST

|[Markdown](https://velog.io/tags/Markdown)|[github](https://velog.io/tags/github)|[profile](https://velog.io/tags/profile)|[readme](https://velog.io/tags/readme)|
|----|----|----|----|

GitHub은 단순히 코드를저장하는 공간을 넘어서 **개발자 자신을 보여주는 프로필 페이지** 역할도 한다.

> GitHub에는 `username/username`이름의 repo에 `README.md`를 작성하면 해당 내용이 GitHub 메인 페이지에 그래도 노출된다.

그래서 이번글에서는 **README파일을 활용해** 메인페이지를 꾸며볼것이다.

---

## 🎯 목표

### Before
![](https://velog.velcdn.com/images/kimdongwoo0930/post/2b401fbc-cf6b-42f0-b4ab-383b7863b96f/image.png)



### After
![](https://velog.velcdn.com/images/kimdongwoo0930/post/c597e575-4dbf-4a28-8c8f-b642324f7245/image.png)
> 아이콘 몇개 깨지는건 추후 수정할 것이다.

---

##  1️⃣ 프로필 README 레포지토리 만들기

GitHub에서 **내 아이디와 동일한 이름의 레포지토리**를 생성하면
해당 레포지토리의 README.md가 GitHub 메인 페이지 상단에 자동으로 표시된다.
![](https://velog.velcdn.com/images/kimdongwoo0930/post/8d99eeb9-3345-405f-96c1-0aec2c9f74a9/image.png)

#### 📌 생성 시 체크 포인트

- Public 레포지토리

- `Add README` 체크

---

## 2️⃣ README 기본 틀은 직접 만들지 않았다

GitHub 프로필 README는
Markdown만으로도 충분히 꾸밀 수 있지만, **처음부터 전부 직접 작성하는 건 생각보다 손이 많이 간다.**

그래서 이번에는 이미 잘 만들어진 툴을 이용해 기본 레이아웃(뼈대)부터 먼저 만들고,
그 위에 필요한 요소만 추가하기로 했다.

사용한 도구는 아래 사이트다.

👉 [github-profile-readme-editor](https://github-profile-readme-editor.netlify.app/)


--- 
## 3️⃣ FirstStep으로 README 만들기

자신의 이름과 넣고싶은 부분을 체크한 후 Next를 하면 된다.

![](https://velog.velcdn.com/images/kimdongwoo0930/post/9089e076-76cd-484f-9ea7-ea37f83fe3fc/image.png)

> 이후 단계는 디자인 요소 위주이기 때문에
이번 글에서는 따로 다루지 않겠다.

모두 설정이 끝난후 **COPY CODE**를 눌러 아까 생성한 Repo에 있는 README.md 파일에 붙여 넣고 저장하면 된다. 

---
## 4️⃣ GitHub Stats 카드가 안 뜨는 문제
**README**를 거의 완성했을 즈음,
**GitHub Stats** 카드가 보이지 않는 문제가 발생했다.

```text
503: SERVICE_UNAVAILABLE
This Deployment is paused by the owner
```

처음에는
- username 오타
- Markdown 문법 문제

를 의심했지만, **원인은 외부 서비스 장애였다.**

해당 서비스가 정상화되면
추후 다시 수정할 예정이다.

---

## 5️⃣ README를 만들면서 느낀 점
GitHub 프로필 README를 만들면서 느낀 점은 하나다.
> 처음부터 완벽하게 만들 필요는 없다.

- 도구로 기본 틀을 만들고
- 실제로 써보면서 불편한 부분을 고치고
- 필요 없는 요소는 과감히 제거하는 방식이 가장 효율적이었다.

**README는 완성 문서가 아니라 관리 대상에 가깝다.**