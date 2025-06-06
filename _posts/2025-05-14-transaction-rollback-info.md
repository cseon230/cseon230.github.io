---
title: 어떤 예외가 발생하면 트랜잭션을 롤백하나요?
description:
author: Choi Seon Ha
date: 2025-05-14 09:30:00 +0800
categories: [Development, Backend]
tags: [spring, backend]
pin: true
math: false
mermaid: fasle
---

예외 종류에 따른 트랜잭션 롤백은 개발 환경에 따라 다르게 동작합니다.

# Spring 트랜잭션 예외 처리 동작

- Checked Exception : **컴파일 시점에 예외 처리를 강제하는 예외로,** 개발자가 예외 발생 가능성을 예상하고 이를 적절히 처리할 수 있는 정상적인 예외 상황이라고 가정하기 때문에 기본적으로 Checked Exception이 발생하더라도 트랜잭션을 롤백하지 않습니다.
- Unchecked Exception(RuntimeException, Error): Spring은 기본적으로 Unchecked Exception 또는 Error가 발생하면 트랜잭션을 롤백합니다. 이는 Unchecked Exception이 보통 프로그래머의 실수나 시스템적인 문제로 인한 회복하기 어려운 상황(`NullPointerException`, `IllegalArgumentException` 등) 이라고 가정하기 때문입니다. Spring은 JDBC, JPA, Hibername등 하위 데이터 액세스 계층에서 발생하는 다양한 예외를 모두 공통의 Unchecked Exception인 `DataAccessException` 계층으로 변환하여 처리합니다. 이를 통해 일관된 예외 처리 전략을 수립 할 수 있습니다.

기본 동작과 별개로 **@Transactional** 의 `rollbackFor` 나 `noRollbackFor` 속성을 사용하여 특정 Checked Exception 에 대해서도 롤백을 유도하거나, 반대로 Unchecked Exception 에 대해 롤백하지 않도록 설정할 수 있습니다.

반면, 자바(EE) 환경에서는 컨테이너가 관리하는 트랜잭션(CMT)과 개발자가 직접 관리하는 프로그래밍 방식의 트랜잭션 제어 모두 존재하며, 이들 환경에서는 트랜잭션 롤백 동작이 Spring과는 다르게 동작할 수 있습니다.

- **컨테이너 관리 트랜잭션(CMT) 환경**: 기본적으로 Unchecked Exception이 발생하면 컨테이너가 자동으로 트랜잭션을 롤백합니다. Checked Exception은 기본적으로 롤백을 트리거하지 않으며, 필요하면 `@ApplicationException(rollback=true)` 등의 어노테이션으로 롤백을 강제할 수 있습니다.
- **프로그램 방식의 트랜잭션 제어**: 개발자가 트랜잭션의 시작, 커밋, 롤백 등을 직접 관리해야 합니다. 이 경우, 예외가 발생하면 예외 종류와 관계없이 개발자가 상황에 맞게 명시적으로 `rollback()` 을 호출하는 방식으로 트랜잭션을 종료합니다.
