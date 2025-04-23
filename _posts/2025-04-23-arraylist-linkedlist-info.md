---
title: ArrayList VS LinkedList
description:
author: Choi Seon Ha
date: 2025-04-23 09:30:00 +0800
categories: [Development, Backend]
tags: [Java, backend]
pin: true
math: false
mermaid: fasle
---

# ArrayList 특징

- **내부적으로 배열을 사용**
- 초기 용량이 꽉 차면 자동으로 크기를 늘림( `grow()` 메서드, 보통 1.5배)
- 인덱스로 빠르게 접근할 수 있어 조회 성능이 우수
- 단점을 **배열 중간에 삽입/삭제 시 모든 요소를 이동시켜야 함** → **O(n)**

```java
List<String> arrayList = new ArrayList<>();
arrayList.add("apple");
arrayList.get(0); // O(1)
```

---

# LinkedList 특징

- 내부적으로 **이중 연결 리스트**를 사용
- 각 노드는 `data`, `prev`, `next`를 가짐.
- **중간 삽입/삭제에 유리** (노드 연결만 변경하면 됨)
- 하지만 조회 성능이 떨어짐 (앞에서부터 탐색 필요)

```java
List<String> linkedList = new LinkedList<>();
linkedList.add("apple");
linkedList.add(1, "banana"); // 중간 삽입 O(n)
```

---

# 사용 시기 추천

| 상황                                | 추천       |
| ----------------------------------- | ---------- |
| 조회 빈도가 많고 인덱스로 자주 접근 | ArrayList  |
| 데이터의 삽입/삭제가 자주 발생      | LinkedList |
| 메모리 사용이 중요한 경우           | ArrayList  |

| 항목            | ArrayList                            | LinkedList                                      |
| --------------- | ------------------------------------ | ----------------------------------------------- |
| 내부 구조       | 동적 배열 (배열 기반)                | 이중 연결 리스트 (Double Linked List)           |
| 검색 (get) 속도 | **빠름 (O(1))**                      | 느림 (O(n))                                     |
| 삽입 속도       | 끝에 삽입 시 빠름 (O(1) - amortized) | 중간 삽입 시 빠름 (O(1) + 탐색 O(n))            |
| 삭제 속도       | 중간 삭제 시 느림 (O(n))             | 중간 삭제 시 상대적으로 빠름 (O(1) + 탐색 O(n)) |
| 메모리 사용량   | 적음 (배열 하나만 존재)              | 많음 (노드마다 포인터 2개 추가)                 |
| 순차 접근       | 효율적                               | 비효율적                                        |
| 랜덤 접근       | 효율적 (O(1))                        | 비효율적 (O(n))                                 |
