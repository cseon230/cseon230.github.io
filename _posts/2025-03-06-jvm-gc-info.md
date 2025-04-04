---
title: JVM에서 GC 대상 객체를 판단하는 기준
description:
author: Choi Seon Ha
date: 2025-03-06 09:30:00 +0800
categories: [Development, Backend]
tags: [jvm, backend]
pin: true
math: false
mermaid: fasle
---

## GC란?

GC(Garbage Collection)는 자바의 메모리 관리 방법의 하나이며, JVM의 힙 영역에서 동적으로 할당했던 메모리 중에서 필요 없어진 객체를 주기적으로 제거하는 것을 의미합니다. GC는 특정 객체가 사용 중인지 아닌지 판단하기 위해서 도달 가능성(Reachability) 라는 개념을 사용하는데요. 특정 객체에 대한 참조가 존재하면 도달할 수 있으며, 참조가 존재하지 않는 경우에 도달할 수 없는 상태로 간주합니다. 이때, 도달할 수 없다는 결론을 내린다면 해당 객체를 GC의 대상이 됩니다.

## 도달 가능성은 어떻게 판단하나요?

힙 영역에 있는 객체에 대한 참조는 4가지 케이스가 존재하는데요. 힙 내부 객체 간의 참조, 스택 영역의 변수에 의한 참조, JNI에 의해 생성된 객체에 대한 참조(네이티브 스택 영역), 메서드 영역의 정적 변수에 의한 참조가 이에 해당됩니다. 이때, 힙 내부 객체 간의 참조를 제외한 나머지를 `Root Set` 이라고 합니다. `Root set`으로부터 시작한 참조 사슬에 속한 객체들은 도달할 수 있는 객체이며, 이 참조 사슬과 무관한 객체들은 도달하기 어렵기 때문에 GC에 대상이 됩니다.

## 개발자가 GC 대상 판단에 관여할 수는 없나요?

```java
Origin o = new Origin();
WeakReference<Origin> wo = new WeakReference<>(o);
```

자바에서는 `java.lang.ref`패키지의 `SoftReference`, `WeakReference` 클래스를 통해 개발자가 GC 대상 판단에 일정 부분 관여할 수 있습니다. 해당 클래스들의 객체(`reference object`)는 원본 객체(`referent`)를 감싸서 생성하는데요. 이렇게 생성된 객체는 GC가 특별하게 취급합니다. `SoftReference` 객체에 감싸인 객체는 `Root Set`으로부터 참조가 없는 경우에, 남아있는 힙 메모리의 크기에 따라서 GC 여부가 결정됩니다. 반면, `WeakReference` 객체에 감싸인 객체는 `Root Set`으로부터 참조가 없는 경우, 바로 GC 대상이 됩니다.
