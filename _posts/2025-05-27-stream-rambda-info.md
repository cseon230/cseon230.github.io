---
title: 스트림과 람다에 관하여
description:
author: Choi Seon Ha
date: 2025-05-27 09:30:00 +0800
categories: [Development, Backend]
tags: [java, backend]
pin: true
math: false
mermaid: fasle
---

스트림과 람다식은 함께 자주 쓰이지만, 각기 다른 개념이다.

스트림에 대해서 알기 위해선 먼저 **함수형 스타일**에 대해서 알아야 한다.

# 함수형 스타일

| 특징               | 설명                                        | Java 예시                            |
| ------------------ | ------------------------------------------- | ------------------------------------ |
| 상태 변화 없음     | 기존의 값을 바꾸지 않고 새 값을 만듬        | `.map(x -> x + 1)`                   |
| 명령보다 표현      | “어떻게”가 아니라 “무엇을” 하고 싶은지 표현 | `list.stream().filter(x -> x  > 10)` |
| 고차 함수          | 함수를 인자로 전달하거나 반환               | `filter(x -> x > 10)` (람다 전달)    |
| 선언형(Declartive) | for문처럼 절차를 쓰지 않음                  | `for` 대신 `stream().map().filter()` |

## 예시

**🔶 전통적인 방식 (명령형 프로그래밍)**

```java
List<String> names = List.of("Alice", "Bob", "Charlie");
List<String> result = new ArrayList<>();

for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name);
    }
}
```

- 어떻게 할지를 코드가 설명함 (절차 중심)

**📌 함수형 스타일 (선언형 프로그래밍)**

```java
List<String> result = names.stream()
		.filter(name -> name.startsWith("A"))
		.collect(Collectors.toList());
```

- 무엇을 하고 싶은지를 코드가 표현함 (데이터 흐름 중심)
- 더 간결하고 가독성이 뛰어남
- 내부적으로 병렬 처리도 쉽게 가능

→ **함수형 스타일**이란 데이터를 처리할 때 루프나 상태변화 없이 함수를 조합해서 선언적으로 처리하는 방식입니다. Stream API는 이걸 가능하게 해주는 도구고, 람다식은 그 도구 안에서 동작하는 함수의 모습입니다.

---

Stream은 Java 8에서 도입된 기능으로, **컬렉션(List, Set)의 데이터를 순차적 또는 병렬적으로 처리할 수 있는 데이터 흐름입니다.**

### Stream의 핵심 개념

- **컬렉션**의 데이터를 처리할 수 있음
- 데이터 소스를 변경하지 않음
- 한 번만 사용 가능
- **중간 연산**과 **종결 연산**으로 나뉨
- 게으른 평가(Lazy evaluation) → 종결 연산이 실행되어야 실제로 동작함

### 사용 예시

```java
List<String> names = List.of("Alice", "Bob", "Charlie");

names.stream()                            // 스트림 시작 (중간 연산X)
		.filter(name -> name.length() > 3)    // 람다식 사용: 이름이 3글자 넘는 것만
		.map(String::toUpperCase)             // 람다 or 메서드 참조: 대문자로 변환
		.forEach(System.out::println);        // 종결 연산: 출력
```

---

# 람다식(Lambda Expression)이란?

람다식은 **이름 없는 함수**이다. Java에서는 함수를 값처럼 전달할 수 없었는데 람다식을 도입함으로써 **코드를 함수처럼 전달** 할 수 있게 됐다.

### 기본 문법

```java
(매개 변수) -> { 실행할 코드 }
```

```java
(String name) -> { return name.length() > 3; }
```

한 줄이면 `{}` 생략 가능.

```java
name -> name.length() > 3
```

람다식은 **함수형 인터페이스**를 구현할 때 사용됩니다.

예를 들어, `Predicate<T>`는 `boolean test(T t)` 라는 추상 메서드 하나를 가지므로 람다로 대체 가능합니다.

### `Predicate<T>` 자세히 보기

```java
@FunctionalInterface
public interface Predicate<T> {
	boolean test(T t);
}
```

- `T`는 타입
- `test()` 라는 추상 메서드 하나만 있음
- 인자로 받은 값을 검사해서 true/false 를 반환하는 용도

위의 함수형 인터페이스를 전통적인 방식으로 오버라이딩해서 작성하면..

```java
Predicate<String> startsWithA = new Predicate<String>() {
    @Override
    public boolean test(String s) {
        return s.startsWith("A");
    }
};
```

### 람다로 바꾸어 작성하면..

```java
Predicate<String> startsWithA = s -> s.startsWith("A");
```

어떻게 이게 가능하냐면 `Preicate<T>`는 딱 하나의 추상 메서드 `test`만 있기 때문이다. 이런 인터페이스를 함수형 인터페이스라고 부르고, **자바는 여기에 람다식을 넣을 수 있게 해줍니다.**

즉… Stream API 안에서 사용하는 `.map()` , `.filter()`, `.forEach()` 등은 함수를 인자로 받는 메서드이다. 이때 **함수 역할을 하는 것이 바로** **람다식**입니다.

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

List<Integer> doubled = numbers.stream()
		.filter(n -> n % 2 == 0)       // 짝수만 추출 (람다식: n -> n % 2 == 0)
		.map(n -> n * 2)               // 각 요소를 2배로 (람다식: n -> n * 2)
		.collect(Collectors.toList()); // 결과 수집
```

**Stream은 데이터를 함수형으로 처리하는 도구, 람다식은 Stream 연산자에게 넘기는 행동(함수)이다.**

# 🔥 예시 코드

1. 숫자들의 합 구하기 (`reduce`)

```java
List<Integer> nums = List.of(1, 2, 3, 4, 5);

int sum = nums.stream()
			.reduce(0, (a, b) -> a + b); // 누적 합

System.out.println(sum); // 15
```

1. 객체 리스트에서 조건에 맞는 필드 추출

```java
class Person {
    String name;
    int age;
    public Person(String name, int age) {
        this.name = name; this.age = age;
    }
    public String getName() { return name; }
    public int getAge() { return age; }
}

List<Person> people = List.of(
    new Person("Alice", 22),
    new Person("Bob", 19),
    new Person("Charlie", 25)
);

List<String> adults = people.stream()
			.filter(p -> p.getAge >= 20)   // 나이 필터링
			.map(p -> p.getName())         // 이름만 추출
			.collect(Collectors.toList()); // 리스트로 모음

System.out.println(adults); // [Alice, Charlie]
```
