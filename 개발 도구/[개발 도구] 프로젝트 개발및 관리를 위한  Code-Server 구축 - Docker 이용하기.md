[Velog로 가기](https://velog.io/@kimdongwoo0930/프로젝트-개발및-관리를-위한-Code-Server-구축-Docker-이용하기)

released at 2024-05-28 22:35:07 KST

updated at 2026-11-08 05:07:21 KST

|[code-server](https://velog.io/tags/code-server)|[docker](https://velog.io/tags/docker)|[mac](https://velog.io/tags/mac)|[vscode](https://velog.io/tags/vscode)|
|----|----|----|----|

![](https://velog.velcdn.com/images/kimdongwoo0930/post/a9963a53-048a-468a-b26e-f4a8924f02cd/image.png)
## 왜?
 먼저 작성자는 Mac을 주로 사용하고 있고 맥미니를 이용해서 서버를 돌릴 수 있는 환경이기 때문에 
개발하고 있는 프로젝트및 코드들을 맥미니에 저장하기 위해서 구축하려고 한다. 

 또한 코드서버를 구축해두면 집에서 개발을 못할 경우 외부에서 vnc를 이용하지 않고 
code-server를 이용해 접속해 코드를 작성및 수정을 할 수 있게 된다.

<br/>

## 구축하기

### 사전 준비
- 24시간 돌릴 수 있는 서버 ( 24시간이 가능해야 언제 어디서 접속이 가능하다. )
- Docker 설치
- docker-compose 설치

### 환경
- Mac OS
- Docker 25.0.3
- docker-compose V2.24.6

<br/>

### docker-compose.yaml 파일 작성하기

```yaml
version: "3.1"
services:
  code-server:
    image: lscr.io/linuxserver/code-server:latest
    container_name: code-server
    restart: always
    ports:
      - "8443:8443"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Seoul
      - PASSWORD= # 접속 비밀번호
      - SUDO_PASSWORD= # 관리자 접근 비밀번호
      - DEFAULT_WORKSPACE=../ # 기본 작업 디렉토리
    volumes:
      - ./volume/config:/config
      - ./volume/source:/config/workspace
  ```
  
  ### 실행
  이제 docker-compose.yml를 작성한 곳에서 실행을 해주면 된다.
  ```bash
  sudo docker-compose up -d
  ```
  
  ### 결과
  ![](https://velog.velcdn.com/images/kimdongwoo0930/post/902e1c0c-ee5e-4f24-9629-885bc4d325fc/image.png)

  위 사진처럼 만약 도커가 정상적으로 됐다면 우리가 설정했던 포트( 저는 8443 )로 접속한다.

![](https://velog.velcdn.com/images/kimdongwoo0930/post/5d14b232-baf9-44b6-84b8-1d9ede1cd49d/image.png)

로그인창이 나오면 우리가 docker-compose.yml에 작성했던 비밀번호로 접속하면 된다.
![](https://velog.velcdn.com/images/kimdongwoo0930/post/ebba9b85-ce02-4cb8-8a73-d1821f44a7b4/image.png)

#### 이렇게 나오면 성공이다 짜자잔!
