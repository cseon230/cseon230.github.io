---
title: N + 1 문제란?
description:
author: Choi Seon Ha
date: 2025-04-09 09:30:00 +0800
categories: [Development, Backend]
tags: [backend]
pin: true
math: false
mermaid: fasle
---

# N+1 문제란?

어떤 데이터를 조회할 때 1개의 쿼리로 **기본 데이터(N개)** 를 조회한 뒤, **N개의 추가 쿼리가** 각각의 연관 데이터를 조회하기 위해 실행되는 상황을 말합니다.

## (예시) 게시글(Post)과 댓글(Comment)

```java
@Entity
public class post {
	@Id
	private Long id;

	private String title;

	@OneToMany(mappedBy = "post", fetch = FetchType.LAZY)
	private List<Comment> comments;
}
```

```java
@Entity
public class Comment {
	@Id private Long id;

	private String content;

	@ManyToOne(fetch = FetchType.LAZY)
	private Post post;
}
```

---

## 어떤 코드에서 문제가 발생할까?

```java
List<Post> posts = postRepository.findAll(); // 1번 쿼리

for (Post post : posts) {
	List<Comment> comments = post.getComments(); // N번 쿼리
}
```

### 실행되는 쿼리 수

- `postRepository.findAll()` → 게시글 N개 가져옴 → 1번 쿼리
- 각 게시글마다 `post.getComments()`시 마다 쿼리 실행 → N 번 쿼리
- **→ 총 N+1번의 쿼리 발생**

---

## 문제가 되는 이유

1. 데이터 양이 많아지면 DB에 엄청난 부하 발생
2. 성능 저하, 속도 느려짐
3. 개발자는 한 줄의 코드만 작성했는데 백그라운드에서 수십, 수백 쿼리 실행

---

## 해결 방법

### 1. Fetch Join 사용

```java
@Query("SELECT p FROM Post p JOIN FETCH p.comments")
List<Post> findAllWithComments();
```

- 한 번의 쿼리로 게시글과 댓글을 모두 JOIN 해서 가져옵니다.
- → N + 1 문제가 발생하지 않음

### 2. EntityGraph 사용 (Spring Data JPA)

```java
@EntityGraph(attribytePaths = "comments")
List<Post> findAll();
```

- 내부적으로 fetch join이 적용됨

### 3. DTO로 필요한 데이터만 Projection

```java
@Query("SELECT new com.example.PostDto(p.title, c.comtent) FROM Post p JOIN p.comments c"
List<PostDto> findAllWithCommentsInfo();
```
