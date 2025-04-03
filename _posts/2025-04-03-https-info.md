---
title: HTTP와 HTTPS
description:
author: Choi Seon Ha
date: 2025-04-03 09:30:00 +0800
categories: [Development, Backend]
tags: [network, cs, backend]
pin: true
math: false
mermaid: fasle
---

# HTTP란?

### **HyperText Transfer Protocol**

: 웹에서 클라이언트(브라우저)와 서버가 **데이터를 주고받기 위한 통신 규약(프로토콜)**

- 비연결성 (Stateless) : 요청-응답 이후 연결을 끊음. 따라서 서버는 이전 요청의 상태를 기억하지 않음.
- 클라이언트-서버 모델 : 브라우저(클라이언트)가 요청 -> 서버가 응답
- 기본 포트 : 80

# HTTPS란?

### **HTTP Secure**

: HTTP에 **보안(Secure)**을 추가한 버전입니다.

- 암호화 (SSL/TLS) : 중간에서 누가 훔쳐보더라도 내용을 알 수 없게 암호화해서 전송.
- 인증서 사용 : 사이트의 신뢰성을 보장하기 위해 **인증서(SSL 인증서)**를 사용.
- 기본 포트 : 443

---

# HTTP / HTTPS 구조 흐름

### 요청 (Request)

클라이언트 -> 서버

```vbnet
GET /hello.html HTTP/1.1
Host: wwww.example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

요청 방식 (메서드):

- `GET`: 데이터 조회
- `POST`: 데이터 생성/전송
- `PUT`: 데이터 수정
- `DELETE`: 데이터 삭제

### 응답 (Response)

서버 -> 클라이언트

```bash
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 305

<html> ... </html>
```

상태 코드 (Status Code)

- `200 OK`: 정상
- `301 Moved Permanently`: 리다이렉트
- `400 Bad Request`: 잘못된 요청
- `401 Unauthorized`: 인증 필요
- `403 Forbidden`: 접근 금지
- `404 Not Found`: 없는 페이지
- `500 Internal Server Error`: 서버 오류

---

# HTTPS는 어떻게 안전한가?

### 1. 클라이언트가 서버에 접속 시도

### 2. 서버가 인증서(공개키 포함)를 보냄

### 3. 클라이언트가 인증서를 검증하고 대칭키를 암호화해서 서버에 보냄

### 4. 서버는 비밀키로 대칭키를 복호화

### 5. 서버-클라이언트 간 대칭키 기반 암호화 통신 시작

## -> 이런 구조 덕분에 중간자 공격(Man-in-the-Middle) 방지 가능

---

# HTTPS가 HTTP보다 나은 점 6가지

## 1. 데이터 암호화 (Encryption)

- HTTP는 평문(그대로의 텍스트)으로 데이터를 주고받습니다. 중간에서 누가 가로채면 내용이 그대로 노출됩니다.
- HTTPS는 데이터를 암호화해서 보내기 때문에, 중간에서 가로채더라도 내용을 알 수 없습니다.

### 예) 아이디/비밀번호, 카드번호 등 중요한 정보가 안전하게 전송됨

## 2. 신뢰성 있는 인증 (Authentication)

- HTTPS는 SSL/TLS 인증서를 사용해서 웹사이트의 신원을 보장합니다.
- 사용자는 '이 사이트가 진짜 맞는지' 확인할 수 있습니다

### 예) 피싱 사이트, 가짜 은행 사이트에 속을 가능성 낮아짐

## 3. 중간자 공격(Man-in-the-Middle) 방지

- HTTPS는 중간에 누가 통신을 가로채서 내용을 바꾸거나 훔쳐보는 걸 막아줍니다.
- 이는 TLS의 암호화 + 인증 + 무결성 체크 덕분입니다.

## 4. SEO(검색 엔진 최적화) 가산점

- 구글은 HTTPS를 사용하는 사이트에 **랭킹 우선순위(SEO 점수)**를 부여합니다.

### 예) 같은 내용의 두 사이트가 있다면 HTTPS를 사용하는 쪽이 검색 결과에서 더 위에 나옴

## 5. 데이터 무결성 (Integrity)

- 중간에서 누군가 데이터를 조작하더라도, 클라이언트와 서버는 이를 감지할 수 있습니다.
- 덕분에 "받은 데이터가 원래 보낸 그대로인지" 신뢰할 수 있습니다.

## 6. 브라우저의 보안 경고 방지

- 요즘 크롬이나 사파리, 엣지 브라우저는 HTTP 사이트에 경고 메세지를 띄웁니다.

### 예) "이 사이트는 안전하지 않습니다"

- HTTPS 사이트는 이런 경고 없이 `자물쇠 아이콘`🔒 으로 안전함을 보여줍니다.
