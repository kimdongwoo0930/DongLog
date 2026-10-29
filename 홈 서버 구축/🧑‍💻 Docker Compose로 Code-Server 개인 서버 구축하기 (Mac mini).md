[Velog로 가기](https://velog.io/@kimdongwoo0930/Docker-Compose로-Code-Server-개인-서버-구축하기-Mac-mini)

released at 2026-01-13 16:36:36 KST

updated at 2026-10-29 09:33:17 KST

|[code-server](https://velog.io/tags/code-server)|[docker](https://velog.io/tags/docker)|[docker compose](https://velog.io/tags/docker-compose)|[mac](https://velog.io/tags/mac)|[vscode](https://velog.io/tags/vscode)|
|----|----|----|----|----|



![](https://velog.velcdn.com/images/kimdongwoo0930/post/b8f3f5d0-97a7-4d14-945d-e93de5efdd5a/image.png)

군 복무도 끝났으니 다시 개발 공부를 시작하기 위해  
새로운 개발 환경을 준비하려고 한다.

예전에도 사용했던 **M2 Mac mini**를 다시 개인 서버로 활용해  
언제 어디서든 접속 가능한 개발 환경을 만들고자 한다.

가장 먼저 **웹 기반 VS Code 환경인 code-server**를  
**Docker + Docker Compose**로 구성해보았다.

---

## 🛠 개발 환경

- **Mac OS**
- **Docker Desktop 4.56.0**
- **Docker Compose v5.0.0**

---

## 📁 프로젝트 구조

```bash
Code-Server/
├── docker-compose.yml
└── workspace/
    └── project/
```

- docker-compose.yml
→ code-server 컨테이너 설정 파일

- workspace/
→ 실제 개발 코드가 저장될 디렉터리 (컨테이너 삭제 후에도 유지)

---

## 🐳 docker-compose.yml 작성
```yml
services:
  code-server:
    image: codercom/code-server:latest
    container_name: code-server
    restart: unless-stopped

    ports:
      - "8080:8080"

    volumes:
      - ./volume/workspace:/workspace
      - ./volume/config:/config

    environment:
      - PASSWORD=접속_비밀번호
      - SUDO_PASSWORD=관리자_비밀번호
      - TZ=Asia/Seoul

    user: "1000:1000"
   ```
 ### 🔄 Docker 이미지 변경 (업데이트)
 기존에 사용하던 code-server 이미지에서는
VS Code 버전이 오래되어 일부 확장 기능이 정상적으로 동작하지 않았다.

이를 해결하기 위해
**VS Code 최신 버전**을 사용할 수 있는 이미지로 변경했다.

#### ✅ 변경 전
```yaml
image: codercom/code-server
```
#### ✅ 변경 후
```yaml
image: ghcr.io/corder/code-server:latest
```
 
 ---
   

   

## 📌 설정 설명
#### 🔹 version 필드 없음

- 최신 Docker Compose는 Compose Specification을 사용

- **version**: 필드는 더 이상 필수가 아님

- 최신 Docker 환경에서는 없어도 정상 작동

---

#### 🔹 포트
```yml
- ports:
  - "8080:8080"
```

- 로컬 8080 포트를 컨테이너 8080 포트에 연결

- 브라우저에서 http://localhost:8080으로 접속 가능

---

#### 🔹 volumes
```yml
volumes:
  - ./volume/workspace:/workspace
```

- **workspace**
→ 컨테이너 내부 작업 디렉터리와 연결
→ 코드가 로컬에 저장되어 컨테이너 삭제 후에도 유지됨

---

#### 🔹 environment
```yml
environment:
  - PASSWORD=접속_비밀번호
  - SUDO_PASSWORD=관리자_비밀번호
  - TZ=Asia/Seoul
```

- PASSWORD
→ code-server 웹 접속 비밀번호

- TZ
→ 컨테이너 시간대 설정 (로그 시간 오류 방지)

---

#### 🔹 user 설정
```yml
user: "1000:1000"
```

- Mac / Linux 환경에서 파일 권한 꼬임 방지

- 컨테이너에서 생성한 파일이 로컬 사용자 소유로 생성됨

---

### ▶ Docker 컨테이너 실행

docker-compose.yml 파일이 있는 디렉터리로 이동한 뒤 아래 명령어를 실행한다.
```bash
docker compose up -d
```

컨테이너가 정상적으로 실행되면 브라우저에서 다음 주소로 접속한다.

http://localhost:8080


설정한 비밀번호를 입력하면
VS Code 화면(code-server) 이 나타난다.

---
## ✅ 마무리

Docker와 code-server를 이용해 다음과 같은 환경을 구축할 수 있었다.

- Mac mini를 개인 서버처럼 활용

- 언제 어디서든 동일한 개발 환경 접속

- 로컬과 완전히 분리된 개발 환경 유지

- Docker 기반이라 확장 및 관리가 쉬움


다음 단계로는 다음 작업을 진행할 예정이다.

🔐 Nginx + HTTPS (Let’s Encrypt)

🌐 도메인 연결


