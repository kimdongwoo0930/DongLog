[Velog로 가기](https://velog.io/@kimdongwoo0930/React-리액트를-TypeScript로-시작하기)

released at 2024-05-30 19:57:48 KST

updated at 2026-10-10 19:06:26 KST

|[Node](https://velog.io/tags/Node)|[React](https://velog.io/tags/React)|[ts](https://velog.io/tags/ts)|[typescript](https://velog.io/tags/typescript)|[리액트](https://velog.io/tags/리액트)|[타입스크립트](https://velog.io/tags/타입스크립트)|
|----|----|----|----|----|----|

이번에 리액트를 타입스크립트로 개발하는 이유는 사실 자바스크립트로 먼저 개발을 해둔 사이트가 있지만 많은 버그 중에 타입 문제도 많이 발생하였기 때문에 사이트를 다시 만들겸 이번에는 타입스크립트로 작성을 해보려고 한다.

### 프로젝트 생성
우선 타입스크립트를 사용하는 리액트 프로젝트를 생성할때는 원래 리액트 프로젝트를 생성할때 사용하던 명령어 뒤에 `--template typescript`를 붙이면 됩니다.

```bash
$ npx create-react-app 프로젝트명 --template typescript
```

혹시 이미 진행중인 프로젝트에 타입스크립트를 적용하고 싶다면 [여기](https://create-react-app.dev/docs/adding-typescript/)를 확인해서 진행해 주세요.

### 프로젝트 살펴보기
프로젝트를 생성한 후 폴더를 살펴보면 `src`폴더 안에 `App.tsx`라는 파일이 있을겁니다.
이제 타입스크립트로 리액트를 개발하다보면 작성하는 모든 컴포넌트들의 확장자는 ` .tsx`의 이름으로 구성됩니다.
![](https://velog.velcdn.com/images/kimdongwoo0930/post/0aee312a-cd52-4263-a241-87a99af6922f/image.png)

저는 function 형태보다 화살표 함수를 더 선호하기 때문에 수정해주겠습니다.
```typescript
const App = () => {}
```
### 실행하기
우리가 생성했던 프로젝트 폴더로 이동해서 `npm start`를 입력하면 프로젝트가 실행됩니다.
```bash
$ npm start
```

### 마무리
![](https://velog.velcdn.com/images/kimdongwoo0930/post/54a5d916-695b-48fe-b897-f37d10a39629/image.png)


이제 개발을 시작할 준비가 끝났습니다.
앞으로 개발을 하면서 리액트에 대한 정보가 더 있다면 글로 작성해보겠습니다.



