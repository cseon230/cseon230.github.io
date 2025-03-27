---
title: Thread 란?
description:
author: Choi Seon Ha
date: 2025-03-27 09:30:00 +0800
categories: [Development, Backend]
tags: [java, backend]
pin: true
math: false
mermaid: fasle
---

## 쓰레드(Thread)란?

쓰레드는 하나의 프로세스 안에서 실행되는 작은 단위의 작업 흐름입니다. 쉽게 말해, 프로그램 안에서 **동시에 여러 일을 처리할 수 있게 해주는 실행 단위** 라고 보면 됩니다.

---

## 프로세스(Process) vs 쓰레드(Thread)

| 구분     | 설명                                                              |
| -------- | ----------------------------------------------------------------- |
| 프로세스 | 실행 중인 프로그램 (ex. IntelliJ, chrome, 메모장 등)              |
| 쓰레드   | 프로세스 내부의 작업 단위. 한 프로세스 안에 여러 쓰레드 존재 가능 |

> 하나의 프로세스는 기본적으로 **하나의 메인 쓰레드(Main Thread)** 를 가지고 있고, 추가적으로 다른 쓰레드를 만들 수 있어요.

---

## 예시로 이해하기

📌 유튜브 앱 (하나의 프로세스)

- 영상 재생 쓰레드
- 자막 표시 쓰레드
- 댓글 다운로드 쓰레드
- 광고 표시 쓰레드

이 모든 작업이 동시에 이루어지는 이유는 여러 개의 쓰레드가 동시에 실행되고 있기 때문입니다.

---

## 자바에서의 쓰레드

### 1. 기본 쓰레드 : `main`메서드 안에서 돌아가는 메인 쓰레드

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello"); // 메인 쓰레드에서 실행
    }
}
```

### 2. 새로운 쓰레드 생성

방법 1 : `Thread` 클래스 상속

```java
class MyThread extends Thread {
	public void run() {
		System.out.println("새로운 쓰레드 실행!");
	}
}

public class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start(); // run()을 직접 호출하지 않고 start()로 실행해야 새로운 쓰레드가 생성됨
    }
}
```

방법 2 : `Runnable` 인터페이스 사용

```java
public class Main {
	public static void main(String[] args) {
		Runnable task = () -> {
			System.out.println("Runnable 방식의 쓰레드 실행");
		};

		Thread thread = new Thread(task);
		thread.start();
	}
}
```

---

## `start()` vs `run()` 차이

| 메서드    | 설명                                                            |
| --------- | --------------------------------------------------------------- |
| `start()` | 새로운 쓰레드를 생성해서 `run()` 을 **비동기**로 실행           |
| `run()`   | 그냥 메서드 호출이므로 **동기적으로 실행**됨 (쓰레드 생성 안됨) |

---

## ⚠️ 쓰레드를 사용할 때 주의할 점

- **동기화 문제** : 여러 쓰레드가 동시에 공유 자원에 접근하면 충돌이 발생할 수 있음 → `synchronized` 키워드 필요
- **디버깅 어려움** : 실행 순서가 항상 같지 않기 때문에 예상치 못한 문제가 발생할 수 있음
- **자원 소모** : 쓰레드를 너무 많이 만들면 성능 저하

---

## 🔶 쓰레드 개념 요약

- 하나의 프로그램(프로세스) 안에 여러 개의 쓰레드가 있을 수 있음
- 쓰레드는 동시에 여러 작업을 가능하게 해줌 (병렬 처리)
- 자바에서는 `Thread` 또는 `Runnable` 을 통해 만들 수 있음
