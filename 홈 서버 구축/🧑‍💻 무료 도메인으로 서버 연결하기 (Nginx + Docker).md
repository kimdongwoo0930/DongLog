[Velog로 가기](https://velog.io/@kimdongwoo0930/도메인으로-Nginx-연결하기)

released at 2026-01-22 17:09:02 KST

updated at 2026-11-10 07:13:31 KST

|[Infra](https://velog.io/tags/Infra)|[Nginx](https://velog.io/tags/Nginx)|[code-server](https://velog.io/tags/code-server)|[docker](https://velog.io/tags/docker)|[domain](https://velog.io/tags/domain)|
|----|----|----|----|----|

![](https://velog.velcdn.com/images/kimdongwoo0930/post/f8dbb043-cc8d-453b-a968-75477dfe5f16/image.png)


## 🌐 무료 도메인으로 서버 연결하기 (HTTP)
저는 비용 부담 없이 도메인을 사용하기 위해  
무료 도메인 제공 사이트인 **[내도메인](https://xn--220b31d95hq8o.xn--3e0b707e/)** 을 이용해 도메인을 생성했습니다.

![](https://velog.velcdn.com/images/kimdongwoo0930/post/08309ce0-c40c-4660-b6e3-2c884fc4c739/image.png)

원하는 도메인 이름을 검색하면  
✔ 사용 가능 여부를 바로 확인할 수 있고,  
✔ 사용 가능한 경우 즉시 도메인을 생성할 수 있습니다.

---
### 📌 도메인 DNS설정
도메인을 생성한 후,  
**도메인 관리** 메뉴로 이동해 **수정** 버튼을 클릭합니다.

![](https://velog.velcdn.com/images/kimdongwoo0930/post/ad397dbe-57bb-4dce-b63e-540af383ed1e/image.png)

도메인 설정 화면에서는  
DNS 레코드를 직접 수정할 수 있습니다.

---
### 🌍 서버와 도메인 연결 (A 레코드)
이제 도메인을 서버와 연결하기 위해  
**IP 연결(A 레코드)** 설정을 진행합니다.
![](https://velog.velcdn.com/images/kimdongwoo0930/post/f8cd5198-a087-45b0-b650-c8a112e8a5f7/image.png)


A 레코드에는  
Nginx가 실행 중인 서버의 **공인 IP 주소**를 입력해주면 됩니다.

이 설정을 통해  `도메인 → 서버 IP → Nginx`  흐름으로 요청이 전달되게 됩니다.

---

## 2️⃣ Nginx server_name 실제 도메인으로 변경
```nginx
server {
    listen 80;
    server_name 내도메인 www.내도메인;

    location / {
        proxy_pass http://code-server:8080;
        
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

```
이렇게 설정하면
```text
브라우저 → 도메인 → Nginx → Code-Server
```
형태로 요청이 정상적으로 전달된다.

---

## 🐳 docker-compose.yml 수정 (ports → expose)

Nginx를 통해 외부 요청을 처리하도록 구성하면서
code-server 컨테이너가 직접 포트를 열 필요가 없어졌다.

그래서 `docker-compose.yml`에서
`ports` 설정을 제거하고
`expose`로 내부 포트만 열도록 수정했다.

> **전 코드서버 포트를 8443으로 사용하고 있었습니다.**

### ❌ 변경 전 docker-compose.yml
```yaml
services:
  code-server:
    image: codercom/code-server:latest
    ports:
      - "8443:8443"
```

### ✅ 변경 후 docker-compose.yml
```yaml
services:
  code-server:
    image: codercom/code-server:latest
    expose:
      - "8443"
```
### 📌 ports 와 expose 차이

#### ports

- 호스트 ↔ 컨테이너 포트 직접 연결

- 외부에서 IP:PORT로 접근 가능

#### expose

- 같은 Docker 네트워크 내부에서만 포트 공개

- 외부 접근 ❌

- Nginx 같은 프록시 컨테이너에서만 접근 가능

---
> **⚠️ 포트 포워딩 관련 설정은 이번 글에서는 따로 다루지 않는다.**

---

### 📐 최종 구조
```text
브라우저
  ↓
도메인
  ↓
Nginx (80)
  ↓
Docker 네트워크
  ↓
Code-Server (8443)

```

---

## ✅ 결과

- 무료 도메인을 발급받아

- DNS A 레코드를 서버 IP로 연결하고

- Nginx server_name에 실제 도메인을 적용함으로써

👉 포트 번호 없이 도메인으로 서비스에 접근할 수 있는 환경을 구성했다.

이제 더 이상 http://IP:포트번호 형태로 접속하지 않아도 된다.

---

### 🔜 다음 편 예고: HTTPS 적용하기

다음 글에서는

- 🔒 Let’s Encrypt

- 🔐 Certbot

- 🌐 Nginx HTTPS 설정

을 이용해서 **무료 SSL 인증서**를 통해 **HTTPS**를 적용하는 과정을 정리할 예정입니다.