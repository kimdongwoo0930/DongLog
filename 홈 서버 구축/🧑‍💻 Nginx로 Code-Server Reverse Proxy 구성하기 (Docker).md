[Velog로 가기](https://velog.io/@kimdongwoo0930/Nginx로-Code-Server-Reverse-Proxy-구성하기-Docker)

released at 2026-01-13 21:07:50 KST

updated at 2026-10-24 11:09:48 KST

|[Nginx](https://velog.io/tags/Nginx)|[code-server](https://velog.io/tags/code-server)|[docker](https://velog.io/tags/docker)|[docker compose](https://velog.io/tags/docker-compose)|
|----|----|----|----|

![](https://velog.velcdn.com/images/kimdongwoo0930/post/2b3858d6-a3f7-496d-b9f4-3c4241ec731a/image.png)


## 왜 Nginx를 이용해야할까?

도메인을 연결하려면
요청을 받아서 내부 서비스로 전달해 줄 입구 역할이 필요하다.

이 역할을 하는 게 바로 **Nginx** 이다.
```text
[Browser]
   ↓
[Nginx]   ← 도메인, HTTPS 처리
   ↓
[Code-Server]
```

- 도메인 연결

- HTTPS 적용

- 포트 숨기기

- 보안 설정

👉 이 모든 작업이 Nginx 단계에서 처리된다.

---
## 🛠 환경
- M2 Mac mini

- Docker Desktop

- Docker Compose 

- code-server 

---

## 📁 프로젝트 구조 (Nginx 추가)
```bash
Code-Server/
├── docker-compose.yml
├── nginx/
│   ├── conf.d/
│   │   └── code-server.conf
│   └── html/
└── workspace/
```
---


## 🧩 전체 구조 
- 외부에서의 접근은 Nginx로 들어오도록만 허용 
- code server는 내부 네트워크에서만 접근 가능하도록
- 포트를 직접적으로 노출하지 않기

---

## 🐳 docker-compose 구조 변경
```yaml
services:
	nginx: 		  # 외부 접속
    code-server:  #내부 서비스로 이용

```

### 포트 변경
- Nginx → **80 / 443**

---

## 🧠 구성 포인트
#### 1️⃣ code-server는 ports 제거

- 더 이상 localhost:8080으로 직접 접근하지 않음

#### 2️⃣ Nginx가 모든 요청을 받아 전달

- Reverse Proxy 방식

#### 3️⃣ WebSocket 설정 필수

- code-server는 WebSocket 기반

---

### 목표
```text
http://IP
   ↓
Nginx
   ↓
Code-Server
```
---
##  docker-compose.yml (Nginx + Code-Server)

```yaml
services:
  nginx:
    image: nginx:latest
    container_name: nginx
    restart: unless-stopped

    ports:
      - "80:80"

    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d

    depends_on:
      - code-server

  code-server:
    image: codercom/code-server:latest
    container_name: code-server
    restart: unless-stopped
    command: code-server --bind-addr 0.0.0.0:8080/workspace

    expose:
      - "8080"

    volumes:
      - ./volume/workspace:/workspace
      - ./volume/config:/config

    environment:
      PASSWORD=비밀번호
      SUDO_PASSWORD=관리자비밀번호
      TZ=Asia/Seoul

    user: "1000:1000"
```


---

## 📄 nginx 설정 파일 
``nginx/conf.d/code-server.conf``
```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://code-server:8080;
        proxy_set_header Host $host;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_http_version 1.1;
    }
}
```
>테스트를 위해 localhost를 사용했으며,
다음 단계에서 실제 도메인으로 변경할 예정이다.

---
## 🚀 마무리
Nginx를 앞단에 두고 외부 요청을 내부 code-server로 전달하는 구조를 구성하면서, 도메인을 적용하기 위한 기반을 마련했다.  
다음 글에서는 이 환경에서 도메인을 연결하는 과정을 해볼것이다.

