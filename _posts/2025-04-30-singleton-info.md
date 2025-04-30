---
title: 싱글톤 패턴
description:
author: Choi Seon Ha
date: 2025-04-30 09:30:00 +0800
categories: [Development, Backend, Design Pattern]
tags: [Java, Design Pattern, backend]
pin: true
math: false
mermaid: fasle
---

### 싱글톤 패턴이란, 프로그램 안에서 단 하나의 객체만 존재하도록 보장하는 디자인 패턴입니다.

객체를 여러번 생성하지 않고 딱 **한 번만** 생성해서 필요할 때 마다 그 하나를 공유해서 사용합니다.

---

내가 만약 대형 RPG 게임을 만들고 있다면..

- 게임 화면 해상도
- 음량 설정
- 조작키 설정

이런 설정들이 **전역으로 하나만 존재**해야 합니다.

이럴 때 **환경 설정(Setting) 객체**를 싱글톤으로 관리합니다

```java
public class GameSettings {
    private static GameSettings instance;

    private int volume;
    private String resolution;

    // 생성자를 private으로 막아서 외부에서 new로 만들 수 없게 함
    private GameSettings() {
        // 기본값 설정
        this.volume = 50;
        this.resolution = "1920x1080";
    }

    // 전역에서 이 메서드를 통해서만 인스턴스를 가져오게 함
    public static GameSettings getInstance() {
        if (instance == null) {
            instance = new GameSettings();
        }
        return instance;
    }

    // 설정값 getter/setter
    public int getVolume() {
        return volume;
    }
    public void setVolume(int volume) {
        this.volume = volume;
    }
    public String getResolution() {
        return resolution;
    }
    public void setResolution(String resolution) {
        this.resolution = resolution;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        GameSettings settings = GameSettings.getInstance();
        System.out.println(settings.getResolution());  // 1920x1080 출력

        settings.setVolume(80);  // 볼륨을 바꿈

        GameSettings anotherSettings = GameSettings.getInstance();
        System.out.println(anotherSettings.getVolume());  // 80 출력 (같은 인스턴스)
    }
}
```

---

### 위 코드에 대한 나의 질문

```java
GameSettings settings = GameSettings.getInstance();
GameSettings anotherSettings = GameSettings.getInstance();
```

- settings는 getInstance()를 호출해서 만들어졌고 anotherSettings도 getInstance()를 또 호출했다. 그런데 왜 둘이 같은 인스턴스를 참조하는거지?

### 답변

`getInstance()` 메서드를 다시 보자.

```java
public static GameSettings getInstance() {
    if (instance == null) {           // (1)
        instance = new GameSettings(); // (2)
    }
    return instance;                   // (3)
}
```

먼저 `instance == null` 인지 확인한다. 만약 `null`이면, 그때만 `new GameSettings()`로 객체를 딱 한 번 만든다. 만든 instance를 항상 그대로 반환한다.

여기서 중요한 핵심은 `static` 메서드 라는 것이다.

`static` 키워드는 “클래스에 소속된다”는 뜻이다.

객체(instance)마다 따로 존재하는게 아니라, 클래스 하나당 딱 하나만 존재한다. 즉, `new`로 객체를 몇 개를 만들든 `static` 변수나 메서드는 모든 객체, 또는 객체 없이도 같이 **공유**합니다.

즉 ! 싱글톤 패턴은 static을 사용해서 **”전역에서 하나의 인스턴스”**를 유지하는 겁니다.

`static` 메서드 덕분에 객체를 만들지 않고도 접근할 수 있습니다.

---

### 두번째 질문 : `static`이면 다 싱글톤인가?

static과 싱글톤의 핵심 차이점

| 구분        | static                                       | 싱글톤                                                   |
| ----------- | -------------------------------------------- | -------------------------------------------------------- |
| 목적        | 어떤 값이나 기능을 “클래스 단위”로 공유      | “객체(instance)”를 하나만 생성해서 공유                  |
| 개념        | 그냥 값이나 기능을 공유하는 것               | “하나의 객체”를 만들어서 쓰는 것                         |
| 사용 방법   | 클래스를 통해 직접 접근(`클래스명.메서드()`) | 객체를 통해 접근 (`Singleton.getInstance().메서드()`)    |
| 메모리 관리 | 프로그램이 끝날 때 까지 메모리에 계속 남음   | 생성 시점, 소멸 시점을 컨트롤 할 수 있음 (조금 더 유연)  |
| 상태 관리   | 일반적으로 state(상태)를 가지지 않음.        | 객체이기 때문에 state(상태)를 가질 수 있음(ex)설정 저장) |

### static 만 사용한 예시

```java
public class MathUtil {
	public static int add(int a, int b) {
		return a + b;
	}
}
```

```java
int result = MathUtil.add(3, 5);
```

- 객체를 만들 필요 없이 **클래스명.메서드()** 로 바로 사용
- 상태를 가지지 않고 계산만 함
- 여러 번 호출해도 별다른 **저장된 상태는 없음**

### 싱글톤 사용 예시

```java
public class Settings {
	private static Settings instance;
	private int volume;

	private Settings() {} // 생성자 private.
	// 자바에서는 생성자의 이름은 클래스 이름과 반드시 같아야 하며, 대소문자도 일치해야 한다.

	public static Settings getInstance() {
		if (instance == null) {
			instance = new Settings();
		}
		return instance;
	}

	public int getVolum() {
		return volume;
	}
	public void setVolume(int volume) {
		this.volume = volume;
	}
}
```

```java
Settings settings = Settings.getInstance();
settings.setVolume(80);

Settings another = Settings.getInstance();
System.out.println(another.getVolume());
```

### 위 코드에 대한 질문

Settings 클래스 내부에서 자기 자신을 자료형(타입)으로 사용했는데 이건 올바른 문법인가?

→ 자바에서 클래스는 내가 직접 정의하는 **자료형(타입)** 이기 때문에 그 클래스 안에서 자기 자신을 자료형으로 사용할 수 있다.

```java
public class Settings {
	private static Settings instance;
}
```

→ `Settings` 클래스가 아직 완전히 정의되기 전처럼 보일 수 있지만, **자바는 클래스의 정의 중에도 자기 자신의 이름을 타입을 사용할 수 있게 허용합니다.**

### 예시

1. 싱글톤 패턴

```java
public class Logger {
	private static Logger instance;

	private Logger();

	public static Logger getInstance() {
		if (instance == null) {
			instance = new Logger();
		}
		return instance;
	}
```

1. 연결 리스트 같은 재귀적인 자료구조

```java
public class Node {
	int data;
	Node next; // 자기 자신을 자료형으로 사용

	public Node(int data) {
		this.data = data;
		this.next = null;
	}
}
```

---

### 문제

1. 다음 코드가 의미하는 바를 설명해보세요.

```java
public class Example {
    private static Example instance;
}
```

위 코드에서 `private static Example instance;`는 어떤 의미를 가지나요?

- 내가 작성한 답
  : Example 자료형으로 정의된 static 변수 정의. static 변수는 변경되지 않는다.
- 보완된 설명
  : `private static Example instance;` 는 `Example` 클래스 내부에서 전역적으로 하나만 존재하고 공유되는 인스턴스를 저장하기 위한 변수입니다. **static 변수는 클래스 로딩 시 메모리에 올라가며, 프로그램 어디에서든 접근할 수 있지만 값은 변경될 수 있습니다.**

1. 다음 조건에 맞게 클래스를 직접 작성해보세요

- 클래스 이름은 `Printer`
- 생성자는 **외부에서 호출할 수 없도록(private)** 처리
- `static`으로 `Printer` 타입의 `instance` 변수를 선언
- `getInstance()` 메서드를 통해 `instance`를 반환
- `print(String message)` 메서드를 만들어서 전달받은 메시지를 출력

```java
// 내가 작성한 정답
public class Printer {
	private static Printer instance;

	private Printer; // private 생성자. 생성자는 클래스명과 대소문자가 완전히 같아야 한다.

	private Printer getInstance() {
		if (instance == null) {
			Printer instance = new Printer();
		}
		return instance;
	}

	private Printer print(String message) {
		System.out.println(message);
	}
}
```

### 틀린 부분

- `private Printer;` 생성자로 작성하려면 괄호가 있어야 하고, 반환형이 없어야 한다.

⇒ 수정 후

```java
private Printer () {
  // 생성자 내용
}
```

- `private Printer getInstance()` 이건 static 이어야 한다.
- 반환형이 `Printer`인데 `private` 일 필요 없다. 왜냐하면 외부에서도 호출해야 하니까 `public static` 이 맞다.
- 그리고 안쪽의 `Printer instance = new Printer();`는 새로 변수를 만드는게 아니라 기존의 `static` 변수에 대입해야 합니다. 앞에 `Printer`를 붙이면 안됩니다.

```java
public static Printer getInstance () {
	if (instance == null) {
		instance == new Printer();
	}
	return instance;
}
```

- `private Printer print(String message)` 이 메서드는 `Printer`를 반환할 필요가 없고 접근 제헌자도 `private`이 아니라 `public` 이어야 외부에서 호출할 수 있다.

```java
public void print(String message) {
	System.out.println(message);
}
```

### 정답

```java
public class Printer {
	private static Printer instance;

	// private 생성자
	private Printer() {}

	// 인스턴스를 반환하는 public static 메서드
	public static Printer getInstance() {
		if (instace == null) {
			instance = new Printer();
		}
		return instance;
	}

	// 메세지를 출력하는 메서드
	public void print(String message) {
		System.out.println(message);
	}
}
```

### 사용예시

```java
public class Main {
	public static void main(String[] args) {
		Printer printer = Printer.getInstance();
		printer.print("안녕하세요. 싱글톤입니다.");
	}
}
```
