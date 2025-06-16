---
title: ResponseBody(or ResponseEntity<T>)가 있을때와 없을떄의 차이점
description:
author: Choi Seon Ha
date: 2025-06-13 09:30:00 +0800
categories: [Development, Backend]
tags: [spring, backend]
pin: true
math: false
mermaid: fasle
---

# 기본 개념

Spring MVC에서 컨트롤러 메서드의 반환값은 두 가지 방식 중 하나로 처리됩니다.

## 1. 뷰(View)를 반환하는 방식 (전통적인 방식)

- 반환값은 뷰 이름(String)으로 간주됩니다
- 스프링은 해당 이름의 뷰 파일(JSP, Thymeleaf등)을 찾아 렌더링 합니다.
- 이 방식은 템플릿 기반 웹 페이지에 주로 사용됩니다.

## 2. HTTP 본문을 직접 반환하는 방식(@ResponseBody 또는 ResponseEntity<T>

- 반환값을 뷰 이름으로 보지 않고 **그 자체를 HTTP 응답 본문에 직접 기록합니다.**
- 일반적으로 **JSON 또는 XMl 형태로 직렬화(Serialize) 되어 반환됩니다**
- **REST API에서 주로 사용**됩니다.

---

# @ResponseBody가 없는 경우

```java
@GetMapping("/hello")
public String hello() {
	return "hello";
}
```

1. 위와 같이 `@ResponseBody`가 없으면 `"hello"` 라는 뷰 이름으로 해석됩니다.
2. Spring은 `ViewResolver`를 통해 `"hello"` 라는 템플릿 파일(예: hello.html)을 찾습니다.
3. `Model`에 추가된 값이 있다면 뷰 템플릿으로 전달됩니다.
4. 주로 JSP, Thymeleaf와 같은 서버 사이드 렌더링에 사용됩니다.

---

# @ResponseBody가 있는 경우

```java
@GetMapping("/hello")
@ResponseBody
public String hello() {
	return "hello";
}
```

**@ResponseBody 덕에 이제 `"hello"`는 뷰 이름이 아니라 HTTP 응답 본문 내용으로 처리됩니다.**

결과적으로 브라우저에 “hello” 라는 텍스트가 직접 출력됩니다. 객체를 반환할 경우, JSON 형식으로 자동 변환됩니다. (`HttpMessageConverter`가 처리)

---

# 📌 ResponseEntity<T>를 사용하는 경우

```java
@GetMapping("/user")
public ResponseEntity<User> getUser() {
	User user = new User("철수", 30);
	return ResponseEntity.ok(user);
}
```

**`ResponseEntity<T>`는 HTTP 상태 코드, 헤더, 바디를 모두 조작할 수 있는 강력한 방법입니다.**

위 코드는 `200 OK` 상태 코드와 함께 `user` 객체를 JSON으로 반환합니다. 이 경우에도 @ResponseBody 처럼 HTTP 본문에 직접 데이터를 씁니다.

---

## `@ResponseBody` 와 `ResponseEntity<T>` 비교 요약

| 항목               | `@ResponseBody`                                     | `ResponseEntity<T>`                             |
| ------------------ | --------------------------------------------------- | ----------------------------------------------- |
| **공통점**         | HTTP 응답 본문에 **직접 리턴값을 작성** (뷰 사용 X) |                                                 |
| **주요 용도**      | 단순 데이터(JSON, 문자열 등) 응답                   | 상태 코드, 헤더, 바디를 **모두 제어**해야 할 때 |
| **상태 코드 설정** | 불가 (항상 200 OK)                                  | 가능 (`.status(HttpStatus.BAD_REQUEST)` 등)     |
| **헤더 설정**      | 불가                                                | 가능 (`.header("Location", "/path")` 등)        |
| **예외 처리 활용** | 제한적                                              | `ResponseEntity`를 예외 처리에 자주 활용        |
| **대표 사용 예**   | 간단한 REST 응답                                    | 복잡한 REST API 응답 (성공/실패 구분 등)        |
