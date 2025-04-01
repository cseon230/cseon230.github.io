---
title: nginx와 nginx.config
description:
author: Choi Seon Ha
date: 2025-04-01 09:30:00 +0800
categories: [Development, Backend]
tags: [docker, nginx, backend]
pin: true
math: false
mermaid: fasle
---

## Nginx란?

**Nginx**는 경량화되고 고성능인 **웹 서버**이자 **리버스 프록시 서버**입니다.

원래는 정적 파일을 빠르게 제공하기 위해 만들어졌지만, 지금은 다양한 기능을 갖춘 강력한 서버로 발전했습니다.

---

## Nginx의 주요 역할

| 역할              | 설명                                                                            |
| ----------------- | ------------------------------------------------------------------------------- |
| **웹 서버**       | 정적 파일(html, css, js, 이미지 등)을 클라이언트에게 전달                       |
| **리버스 프록시** | 클라이언트 요청을 백엔드 서버(Spring, Node 등)로 전달하고 응답을 받아 대신 전달 |
| **로드 밸런서**   | 여러 서버로 트래픽을 분산해서 처리                                              |
| **SSL 종단 처리** | HTTPS 설정 가능                                                                 |
| **캐싱**          | 자주 요청되는 데이터를 캐싱해서 처리 속도 향상                                  |

---

## 아파치와 Nginx의 차이

| 구분짐             | Apache                      | Nginx                     |
| ------------------ | --------------------------- | ------------------------- |
| **처리 방식**      | 프로세스 기반 (Thread 방식) | 이벤트 기반 (비동기 방식) |
| **성능**           | 동시접속자 많을 때 느려짐   | 동시접속자 많아도 안정적  |
| **정적 파일 성능** | 보통                        | 매우 빠름                 |
| **설정 복잡도**    | 비교적 쉬움                 | 비교적 어려움             |

---

## 사용 예시

### 1. 정적 웹 사이트 서버

```docker
server {
	listen 80;
	server_name example.com;

	location / {
		root /var/www/html;
		index index.html;
	}
}
```

### 2. 리버스 프록시 (React 프론트 + Spring 백엔드 예시)

```docker
server {
	listen 80;
	server_name yourdomain.com;

	location /api/{
		proxy_pass http://localhost:8080/;
	}

	location / {
		root /var/ww/frontend;
		index index.html;
		try_files $uri $uri/ /index.html;
	}
}
```

---

## nginx.conf 의 역할

`nginx.conf` 는 nginx의 **메인 설정 파일**로, nginx의 전체 동작 방식을 정의하는 설정의 중심입니다. 쉽게 말해, nginx가 어떻게 요청을 처리하고, 어디로 보낼지, 어떤 포트를 열지 등을 이 파일을 통해 모두 조정할 수 있어요.

## nginx.cofg의 핵심 역할

| 기능                    | 설명                                                 |
| ----------------------- | ---------------------------------------------------- |
| **포트 지정**           | 예) 80, 443 등 어떤 포트를 리슨할지 설정             |
| **서버 블록 구성**      | 요청에 따라 어떤 동작을 할지 정의 (`server {}` 블록) |
| **리버스 프록시**       | 요청을 백엔드(Spring, Node 등)로 전달                |
| **정적 파일 위치 지정** | HTML, 이미지 등을 어디서 찾을지 설정                 |
| **로그 파일 설정**      | 접근 로그, 에러 로그 위치 지정                       |
| **gzip, cache 설정**    | 성능 최적화                                          |
| **SSL 인증서 연결**     | HTTPS 설정                                           |
| **로드 밸런싱**         | 여러 서버에 트래픽 분산 설정 가능                    |

---

## 기본 구조 예시

```docker
# /etc/nginx/nginx.conf

user www-data;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    access_log /var/log/nginx/access.log;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen 80;
        server_name example.com;

        location / {
            root /var/www/html;
            index index.html index.htm;
        }

        location /api/ {
            proxy_pass http://localhost:8080/;
        }
    }
}
```

---

## 주로 수정하는 부분들

- listen 80; → 몇 번 포트로 들어올지
- server_name → 어떤 도메인일 때 적용할지
- location / { … } → 어떤 경로 요청에 어떻게 응답할지
- proxy_pass → 백엔드 서버로 프록시할 주소
- root → 정적 파일 위치
- ssl_certificate / ssl_sertificate_key → HTTPS 설정 시

---

## 위치

```bash
/etc/nginx/nginx.config # 메인 설정 파일
/etc/nginx/sties-available/ # 서버별 설정 파일
/etc/nginx/sites-enabled/ # 실제 적용되는 설정 (symlink)
```

---

## 꿀팁 : 설정 변경 후 반드시 테스트 !

설정 수정 후엔 다음의 명령어로 문법 검사하기

```bash
sudo nginx -t
```

그리고 적용하려면 :

```bash
sudo systemctl reload nginx
```

---

## 정리

- `nginx.config`는 nginx의 모든 동작을 정의하는 설정의 핵심
- 서버 블록, 정적 파일 제공, 리버스 프록시, HTTPS 설정 등 모두 여기에서 관리
- 설정 변경 시 `nginx -t`와 `reload` 는 필수
