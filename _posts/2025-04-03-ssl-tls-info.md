---
title: SSL/TLS 이란?
description:
author: Choi Seon Ha
date: 2025-04-03 09:30:00 +0800
categories: [Development, Backend]
tags: [network, cs, backend]
pin: true
math: false
mermaid: fasle
---

# SSL / TLS 란?

🔐 SSL (Secure Socket Layer)

- 과거에 널리 쓰이던 보안 통신 프로토콜
- **현재는 보안 취약점 때문에 사용되지 않고 TLS로 대체됨**

🔐 TLS (Transport Layer Security)

- SSL의 후속 버전이자, **현재 웹의 표준 보안 프로토콜**
- 우리가 SSL 이라고 말해도 **실제로는 거의 다 TLS 를 의미함**

---

# 무슨 역할을 할까?

**SSL / TLS 는 네트워크 통신에서 보안을 보장해주는 기술**입니다. 세 가지 핵심 기능을 제공합니다.

### 1. 암호화 (Encryption)

→ 데이터를 제3자가 볼 수 없도록 함

### 2. 인증 (Authentication)

→ 내가 통신하려는 상대가 진짜 맞는지? 를 검증

### 3. 무결성 (Integrity)

→ 데이터가 중간에 변조되지 않았는지 확인

---

# 동작 흐름

🧑‍💻 클라이언트(브라우저)가 접속함

1. 브라우저가 서버에 접속 (예: `https://example.com`)
2. 서버는 공개키 + 인증서(Certificate)를 브라우저에 전달
3. 브라우저는 인증서를 검증 (신뢰할 수 있는 기관인지 확인)

🔐 대칭키 생성 & 공유

1. 브라우저는 **임시 대칭키**를 만들어서 서버에게 **공개키로 암호화**해서 전송
2. 서버는 **자신의 개인키**로 복호화해서 **대칭키를 얻음**

🔁 보안 통신 시작

1. 이 대칭키를 기반으로 **이후 모든 통신은 암호화 됨**

> **즉, 처음엔 공개키 기반 비대칭 암호화 → 이후엔 빠른 대칭키 암호화로 전환**

---

## 용어 정리

| 용어                | 설명                                                             |
| ------------------- | ---------------------------------------------------------------- |
| 공개키              | 누구나 볼 수 있는 키 (데이터 암호화에 사용)                      |
| 개인키              | 오직 서버만 가진 비밀 키 (데이터 복호화에 사용)                  |
| 대칭키              | 암호화/복호화에 같은 키 사용 → 빠름                              |
| 인증서 (SSL 인증서) | 도메인과 공개키를 묶어주는 서류. 신뢰기관이 발급함 (DigiCert 등) |

---

# 인증서(Certificate)는 어디서 나왔을까?

### ✅ 인증서는 공인 인증기관에서 발급받는다

- CA : Certificate Authority (인증서 발급 기관)

예) Let’s Encrypt, DigiCert, Sectigo, GoDaddy 등

### ✅ 서버는 어떻게 인증서를 받는가?

**[1단계] 서버가 CSR을 만든다**

- CSR (Certificate Signing Request): 인증서를 발급받기 위한 신청서
- 여기에 서버의 도메인 정보 + 공개키가 포함되어 있음

```bash
openssl req -new -newkey rsa:2048 -nodes -keyout mysite.key -out mysite.csr
```

**[2단계] CA에 제출**

- CSR을 **공인 인증기관(CA)**에 제출
- CA는 도메인이 실제로 너의 소유인지 확인 (보통 DNS 인증이나 이메일 인증)

**[3단계] CA가 인증서를 발급**

- 도메인 소유가 확인되면, **CA는 도메인 정보 + 공개키를 바탕으로 인증서에 서명해서 발급해줌**
- 인증서에 들어있는 정보
  - 도메인 이름
  - 공개키
  - 유효 기간
  - 발급한 CA 정보
  - CA의 전자서명 (이게 핵심!)

**[4단계] 서버에 인증서를 설치**

- 받은 인증서를 서버(Web server: Nginx, Apache 등)에 설치
- 이후 사용자가 HTTPS로 접속하면, **브라우저는 이 인증서를 확인해서 신뢰할 수 있는지 판단**

---

### TIP

- 인증서에는 유효기간이 있나? 👉 보통 90일 ~ 1년, 정기 갱신 필요
- Let’s Encrypt는 무료인가? 👉 Yes. 자동 갱신까지 지원돼서 개발자들한테 인기가 많음
- 직접 인증서를 만들 수는 없나? 👉 가능은 하지만 브라우저가 신뢰하지 않음
