---
title: Lazy Loading(지연 로딩) 이란?
description:
author: Choi Seon Ha
date: 2025-04-09 09:30:00 +0800
categories: [Development, Backend]
tags: [backend]
pin: true
math: false
mermaid: fasle
---

백엔드에서의 **Lazy Loading(지연 로딩)** 은 주로 **ORM(Object-Relational Mapping)**, 예를 들어 Java의 JPA(Hibernate)같은 기술에서 중요한 개념입니다.

# 백엔드 관점에서 Lazy Loading 이란?

ORM을 사용할 때, **연관된 엔티티(객체)**를 데이터베이스에서 **필요할 때 조회하도록** 설정하는 전략입니다. 즉, 엔티티를 조회할 때 관련된 모든 데이터를 즉시 불러오지 않고, 해당 필드(예: 리스트, 참조 객체 등)에 접근하는 순간 쿼리를 실행하여 데이터를 불러옵니다.

---

## JPA / Hibernate 기준 예시 상황

```java
@Entity
public class Post {
	@Id
	private Long id;

	private String title;

	@OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
	private List<Comment> comments;
}
```

위 예시에서 `Post` 엔티티는 여러 개의 `Comment` 를 가지고 있고, `fetch = FetchType.LAZY`로 설정되어 있습니다.

### Lazy Loading의 동작 방식

```java
Post post = postRepository.findById(1L).get();
// 이 시점에는 comments 쿼리가 실행되지 않음

List<Comment> comments = post.getComments();
// 여기서 실제로 SELECT 쿼리가 실행됨
```

---

## 왜 Lazy Loading을 사용할까?

### 장점

1. **불필요한 쿼리 방지**: 댓글이 필요 없으면 쿼리를 실행하지 않음 → 성능 향상
2. **메모리 사용 최소화**: 연관된 엔티티가 많을수록 유리함

### 단점

1. **N+1 문제 발생 가능**: 게시글 10개를 불러온 후 각 게시글의 댓글을 지연 로딩하면, 총 1 + 10 개의 쿼리가 발생함
2. **세션 종료 문제(LazyInitalizationException)**: Lazy 로딩된 데이터를 세션이 종료된 후 접근하면 오류 발생 (특히, Controller에서 Lazy 필드를 조회하려다 오류가 날 수 있음)

---

## 실무에서의 팁

- **DTO로 필요한 필드만 뽑아서 사용하는 방식** 권장
  - `fetch join`또는 `JPQL Projection` / `QueryDSL Projection` 사용
- **FetchType.LAZY 설정 + 필요한 경우 fetch join으로 데이터 한번에 가져오기**
  ```java
  @Query("SELECT p FROM Post p JOIN FETCH p.comments WHERE p.id = :id")
  Post findPostWithComments(@Param("id") Long id);
  ```
