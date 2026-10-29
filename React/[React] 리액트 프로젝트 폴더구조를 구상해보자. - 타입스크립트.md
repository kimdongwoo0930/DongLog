[Velog로 가기](https://velog.io/@kimdongwoo0930/React-리액트-프로젝트-폴더구조를-구상해보자.-타입스크립트)

released at 2024-06-05 21:28:36 KST

updated at 2026-10-10 15:35:04 KST

|[React](https://velog.io/tags/React)|[typescripts](https://velog.io/tags/typescripts)|[리액트](https://velog.io/tags/리액트)|[타입스크립트](https://velog.io/tags/타입스크립트)|[폴더 구조](https://velog.io/tags/폴더-구조)|
|----|----|----|----|----|

지난 시간에 리액트 프로젝트를 CRA를 통해서 타입스크립트 리액트 프로젝트를 생성했었습니다.

### 프로젝트 구성

```bash
client
  ├── node_modules
  ├── public
  ├── src
  ├── .gitignore
  ├── package.json
  ├── package-lock.json
  ├── tsconfig.json
  └── README.md
```
처음 프로젝트를 생성하면 이러한 폴더구조로 구성되어있습니다.

우리가 개발을 하는 코드들은 모두 src 폴더 안에두게 됩니다. 그렇기 때문에 우리가 필요한 폴더들을 모두 src폴더안에 만들어주겠습니다.

### src 폴더 구조

```bash
src
 ├── api
 ├── assets
 ├── components
 ├── hooks
 ├── layouts
 ├── pages
 ├── styles
 ├── types
 ├── utils
 ├── App.tsx
 └── index.tsx
```
> **주의!!** 이건 오직 제가 사용하는 폴더 구조일뿐 구조는 사람마다 다를 수 있으니 참고만 해주세요.

### 1. api

api 폴더안에는 우리가 서버로부터 가져오기위한 api호출 함수들과 관련된 파일들을 넣었습니다.

### 2. assets

개발 중에 사용하는 이미지같은 파일들을 넣어둡니다.

### 3. components

페이지를 제작할때 재사용되는 컴포넌트들을 넣어둡니다. (Ex. modal, Loading, Header)

### 4. hooks

커스텀 훅을 넣어둡니다.

### 5. layouts

페이지 레이아웃을 위한 파일을 넣어둡니다.

### 6. pages

react Router를 적용할 때 필요한 페이지 컴포넌트들을 넣어둡니다.

### 7. styles

css 파일들을 넣어둡니다. ( 저는 이 프로젝트에서 style-components를 사용하기때문에 많이 사용하지는 않습니다. )

### 8. types

type 인터페이스 파일들을 넣어둡니다.

### 9. utils

개발중 여러번 사용되는 함수, 상수들을 작성해 넣어둡니다.

<br/>

---

## 마무리
> 다음에는 React router를 이용해서 페이지 이동을 해보겠습니다.