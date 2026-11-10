[Velog로 가기](https://velog.io/@kimdongwoo0930/무료-SSL로-HTTPS-적용하기-Nginx-Lets-Encrypt)

released at 2026-01-22 19:02:03 KST

updated at 2026-11-11 06:13:48 KST

|[Nginx](https://velog.io/tags/Nginx)|[code-server](https://velog.io/tags/code-server)|[devops](https://velog.io/tags/devops)|[docker](https://velog.io/tags/docker)|[https](https://velog.io/tags/https)|[ssl](https://velog.io/tags/ssl)|
|----|----|----|----|----|----|

![](https://velog.velcdn.com/images/kimdongwoo0930/post/bced896e-0e77-4066-97f0-bd03d0ffd029/image.png)
## 🎯 목표 구조
```java
https://도메인
   ↓ 443
Docker Nginx (SSL)
   ↓
code-server:8443

```

- SSL 처리는 Nginx
- 인증서 발급/갱신은 Certbot (Docker 컨테이너)
- 로컬 nginx / certbot 사용 안 함

---
## 📌 준비물
- 도메인 A 레코드가 서버 IP를 가리키고 있어야 함
- 80 / 443 포트 외부에서 접근 가능
- Docker / Docker Compose 설치 완료

---

## 1️⃣ 폴더 구조
```text
.
├── docker-compose.yml
├── nginx/
│   ├── conf.d/
│   │   └── code-server.conf
│   └── certs/        👈 인증서 저장
└── certbot/
    └── www/          👈 인증용 웹루트


```
> 📌 nginx/certs 와 certbot/www 는 nginx와 certbot이 공유한다.

---

## 2️⃣ docker-compose.yml (SSL 버전)

```yaml
...
nginx:
    image: nginx:latest
    container_name: nginx
    ports:
      - "80:80"
      - "443:443"
    restart: unless-stopped

    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d
      - ./nginx/certs:/etc/letsencrypt
      - ./certbot/www:/var/www/certbot

    depends_on:
      - code-server

  certbot:
    image: certbot/certbot
    container_name: certbot
    volumes:
      - ./nginx/certs:/etc/letsencrypt
      - ./certbot/www:/var/www/certbot


```
### **✅ certbot 컨테이너를 만든 이유**

- 인증서 발급/갱신을 Docker 내부에서만 처리
- nginx와 인증서를 볼륨으로 공유
- 호스트에 certbot 설치 불필요


---
## 3️⃣ Nginx 설정 (1단계: HTTP 인증용)

> ⚠️ 처음엔 인증서가 아직 없기 때문에 HTTPS 설정을 넣으면 안 된다.

```nginx 
# 🔹 HTTP (인증서 발급용)
server {
    listen 80;
    server_name 도메인 www.도메인; 
    
    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 200;
    }
}

```
---
## 4️⃣ Nginx 컨테이너 작동
```bash
docker compose up -d nginx
```
>📌 certbot은 nginx가 실제로 80 포트에서 응답 중일 때만 성공한다.

---


## 5️⃣ 인증서 발급 (중요)
```bash
docker compose run --rm certbot certonly \
  --webroot \
  -w /var/www/certbot \
  -d 도메인 \
  -d www.도메인 \
  --email your@email.com \
  --agree-tos \
  --no-eff-email

```
성공 시 생성됨 👇

```text
nginx/certs/live/도메인/
 ├── fullchain.pem
 └── privkey.pem

```

---
성공했다면 
추가로 Nginx 설정을 해줘야한다.

### 6️⃣ Nginx 설정 (2단계: HTTPS + 프록시)

**이제 HTTPS 설정 추가**

```nginx

server {
    listen 80;
    server_name 도메인 www.도메인;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://$host$request_uri;
    }
}

# 🔐 HTTPS
server {
    listen 443 ssl;
    server_name 도메인 www.도메인;

    ssl_certificate /etc/letsencrypt/live/도메인/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/도메인/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        proxy_pass http://code-server:8443;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

---

## 7️⃣ nginx 재시작
```bash
docker compose restart nginx

```
---
## 8️⃣ 확인

- 🌐 https://도메인 접속
- 🔒 자물쇠 보이면 성공
- 💻 code-server 로그인 화면 뜨면 끝

---
## 🔄 자동 갱신 (필수)

**Let’s Encrypt 인증서는 90일 유효 → 자동 갱신 필수**

```bash
crontab -e
```
아래 한 줄 추가:
```bash
0 3 * * * docker compose run --rm certbot renew && docker compose restart nginx

```
- 매일 새벽 3시
- 만료 30일 전이면 자동 갱신
- nginx 자동 reload

---
## ✅ 최종 정리
- ✅ SSL / HTTPS 완전 자동화
- ✅ 로컬 nginx / certbot 사용 ❌
- ✅ 컨테이너만 삭제해도 환경 정리 가능
- ✅ 서버 이전/백업 쉬움


