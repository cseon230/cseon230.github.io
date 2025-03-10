---
title: 의존성 주입의 개념
description:
author: Choi Seon Ha
date: 2025-03-11 09:30:00 +0800
categories: [Development, Backend]
tags: [spring, backend]
pin: true
math: false
mermaid: fasle
---

의존성 주입은 객체 간의 의존 관계를 스프링 컨테이너가 관리하고 주입해주는 기법입니다. 즉, 객체가 직접 다른 객체를 생성하는 것이 아니라, 외부에서 필요한 객체를 주입받도록 설계하는 원리입니다.

## 왜 DI가 필요한가?

DI가 없는 코드에서는 클래스 내부에서 직접 객체를 생성하거나 의존성을 명시적으로 설정해야 합니다. 하지만 이런 방식은 결합도를 높여 유지보수와 테스트를 어렵게 만듭니다.

```java
public class Car {
	private Engine engine;

	public Car() {
		this.engine = new Engine(); // 직접 객체 생성 (강한 결합)
	}
}
```

위와 같이 `Car`클래스가 `Engine` 객체를 직접 생성하면, `Car` 는 `Engine`의 구체적인 구현체에 의존하게 되어 유지보수성과 확장성이 떨어지게 됩니다. (`Engine`객체가 변경되면 `Car`클래스에 영향을 줌)

DI를 적용하면 `Car`클래스는 `Engine`객체를 직접 생성하지 않고 외부에서 주입받을 수 있습니다. 이렇게 하면 `Car` 가 `Engine`의 구체적인 구현체에 의존하지 않으므로 결합도를 낮출 수 있습니다.

## 의존성 주입을 적용한 좋은 코드

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;

// 엔진 인터페이스 생성 (추상화)
interface Engine {
    void run();
}

// 엔진 구현체 1: 기본 엔진
@Component
class GasolineEngine implements Engine {
    @Override
    public void run() {
        System.out.println("Gasoline Engine is running...");
    }
}

// 엔진 구현체 2: 전기 엔진
@Component
class ElectricEngine implements Engine {
    @Override
    public void run() {
        System.out.println("Electric Engine is running...");
    }
}

// 자동차 서비스 (의존성 주입 활용)
@Service
public class Car {
    private final Engine engine;

    @Autowired // 생성자 주입
    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        System.out.println("Car is starting...");
        engine.run();
    }
}
```

### 이 코드가 좋은 이유

1. 느슨한 결합
   - `Car` 클래스는 `Engine`의 구현체가 아니라 인터페이스(Engine)에 의존하기 때문에 결합도가 낮아짐
   - `Engine`의 구현체 (`GasolineEngine`, `ElectricEngine`)가 변경되어도 `Car` 클래스는 수정할 필요 없음
2. 유지보수성 증가
   - 새로운 엔진 타입을 추가하려면 `Engine` 인터페이스를 구현한 새로운 클래스를 만들기만 하면 됨
   - 기존 `Car` 코드는 수정할 필요가 없음 → **`OCP (Open-Closed Principle, 개방-폐쇄 원칙)` 준수**
3. 테스트 용이성 증가
   - `MockEngine`과 같은 가짜 엔진을 만들어 테스트 가능
   - `Car`가 `Engine`인터페이스에 의존하므로, 필요에 따라 테스트용 `Engine` 구현체를 쉽게 주입할 수 있음

| 비교 항목     | 의존성 주입 미적용 코드 (Bad Code) | 의존성 주입 적용 코드 (Good Code)         |
| ------------- | ---------------------------------- | ----------------------------------------- |
| 결합도        | 강한 결합 (Tight Coupling)         | 느슨한 결합 (Loose Coupling)              |
| 유지보수      | 코드 수정 시 기존 코드 변경 필요   | 새로운 기능 추가 시 기존 코드 변경 불필요 |
| 테스트 용이성 | 단위 테스트 어려움                 | Mock 객체를 활용한 테스트 가능            |
| 확장성        | 새로운 기능 추가 어려움            | 인터페이스 확장을 통해 기능 추가 용이     |
