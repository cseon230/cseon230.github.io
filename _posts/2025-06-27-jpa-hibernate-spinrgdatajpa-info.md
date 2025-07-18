---
title: JPA, Hibernate, Spring Data JPA의 차이
description:
author: Choi Seon Ha
date: 2025-06-27 09:30:00 +0800
categories: [Development, Backend]
tags: [spring, JPA, backend]
pin: true
math: false
mermaid: fasle
---

- **JPA는 기술 명세**입니다. 자바 애플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 **인터페이스** 입니다. JPA는 단순한 명세이기 때문에 인터페이스와 규약만 정의하며, 실제 구현체는 제공하지 않습니다.
- **Hibernate는 JPA의 구현체 중 하나입니다**. JPA가 정의한 `javax.persistence.EntityManager`와 같은 인터페이스를 직접 구현한 라이브러리 입니다. JPA의 구현체 중 하나일 뿐이므로, DataNucleus, EclipseLink 등 다양한 JPA 구현체로 대체할 수 있습니다.
- **Spring Data JPA는 JPA를 쉽게 사용할 수 있도록 지원하는 모듈입니다. JPA를 한 단계 추상화시킨 Repository 라는 인터페이스를 제공합니다**. 개발자가 Repository 인터페이스에 정해진 규칙대로 메서드를 만들어주기만 하면, 해당 메서드 이름에 적합한 쿼리를 날리는 구현체를 만들어 자동으로 Bean으로 등록해줍니다.

Spring Data JPA는 JPA를 기반으로 하며, 반복적인 코드 작성을 줄이고 데이터 접근 계층을 단순화 합니다. 이 때 JPA를 추상화 했다는 의미는, Spring Data JPA의 Repository 의 구현에서 JPA를 사용하고 있다는 것 입니다. 예를 들어 Repository 인터페이스의 기본 구현체인 `SimpleJpaRepository`는 내부적으로 `EntityManager` 를 사용합니다.
