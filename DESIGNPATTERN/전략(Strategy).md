# 전략(Strategy)

- 디자인 원칙
  - 상속 보다는 구성(Composition) 을 사용하라. (A 에는 B 가 있다.)

> 전략 패턴은 한 유형의 알고리즘을 보유한 상태에서 런타임에 적절한 알고리즘을 선택하는 기법이다. 알고리즘군을 정의하고 각각을 캡슐화하여 교환해서
사용할 수 있도록 만든다. 전략을 활용하면 알고리즘을 사용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있다.

전략 패턴은 세 부분으로 구성된다.

- 알고리즘을 나타내는 인터페이스(Strategy Interface)
  - 인터페이스 내부에는 하나 이상의 추상 메서드를 가질 수 있다.
- 다양한 알고리즘을 나타내는 한 개 이상의 인터페이스 구현
  - Ex) ConcreteStrategyA, ConcreteStrategyB 같은 구체적인 구현 클래스
- 전략 객체를 사용하는 한 개 이상의 클라이언트

> 즉, `전략 알고리즘 인터페이스 생성` > `구현체 생성` > `전략 객체 사용` 으로 나뉜다.

## Example 1

예를 들어 오직 소문자 또는 숫자로 이루어져야 하는 텍스트 입력이 다양한 조건에 맞게 포맷 되었는지 검증하는 예제

- 전략 알고리즘 인터페이스와 구현체 생성

```java
// 알고리즘 인터페이스 : String 문자열 검증 인터페이스
public interface ValidationStrategy {
  boolean execute(String s); 
}

// 인터페이스 구현
public class IsAllLowerCase implements ValidationStrategy {
  public boolean execute(String s) {
    return s.matches("[a-z]+");
  }
}

public class IsNumeric implements ValidationStrategy {
  public boolean execute(String s) {
    return s.matches("\\d+");
  }
}
```

- 전략 객체 사용

```java
public class Validator {
  private final ValidationStrategy strategy;
  public Validator(ValidationStrategy v) {
    this.strategy = v;
  }
  public boolean validate(String s) {
    return strategy.execute(s);
  }
}

Validator numericValidator = new Validator(new IsNumeric());
boolean b1 = numericValidator.validate("aaa"); // false
Validator lowerCaseValidator = new Validator(new IsAllLowerCase());
boolean b2 = lowerCaseValidator.validate("bbbb"); // true
```

### 람다 표현식으로 리팩토링

```java
Validator numericValidator = new Validator((String s) -> s.matches("[a-z]+"));
boolean b1 = numericValidator.validate("aaaa");
Validator lowerCaseValidator = new Validator((String s) -> s.matches("\\d+"));
boolean b2 = lowerCaseValidator.validate("bbbb");
```

## Example 2

- 슈퍼 클래스

```java
public abstract class Duck {
  FlyBehavior flyBehavior;
  QuackBehavior quackBehavior;
  
  public Duck() {}
  
  public abstract void display();
  
  public void perfromFly() {
    flyBehavior.fly();
  }
  
  public void performQuack() {
    quackBehavior.quack();
  }
  
  public void swim() {
    System.out.println("모든 오리는 물에 뜬다. 가짜 오리도 뜬다.");
  }
  
  // 오리의 행동을 동적으로 지정하기 위한 set 메서드
  public void setFlyBehavior(FlyBehavior flyBehavior) {
    this.flyBehavior = flyBehavior;
  }
  
  public void setQuackBehavior(QuackBehavior quackBehavior) {
    this.quackBehavior = quackBehavior;
  }
}
```

- FlyBehavior 인터페이스와 

```java
public interface FlyBehavior {
  public void fly();
}

public class FlyWithWings implements FlyBehavior {
  public void fly() {
    System.out.println("날고 있어요!!");
  }
}

public class FlyNoWay implements FlyBehavior {
  public void fly() {
    System.out.println("저는 못 날아요");
  }
}
```

- QuackBehavior 인터페이스와 구현체

```java
public interface QuackBehavior {
  public void quack();
}

public class Quack implements QuackBehavior {
  public void quack() {
    System.out.println("꽥");  
  }
}

public class MuteQuack implements QuackBehavior {
  public void quack() {
    System.out.println("조용");
  }
}
```

- 서브 클래스

```java
public class ModelDuck extends Duck {
  public ModelDuck() {
    flyBehavior = new FlyNoWay();
    quackBehavior = new Quack();
  }
  
  public void display() {
    System.out.println("저는 모형 오리입니다.");
  }
}
```

- 테스트 클래스

```java
public class MiniDuckSimulator {
  public static void main(String[] args) {
    Duck mallard = new MallardDuck();
    mallard.performQuack();
    mallard.performFly();
    
    /*
     * 실행 중에 오리의 행동을 바꾸고 싶으면 원하는 행동에 해당하는
     * Duck 의 Setter 메서드를 호출하면 된다.
     */
    Duck model = new ModelDuck();
    model.performFly();
    model.setFlyBehavior(new FlyRocketPowered());
    model.perfomFly();
  }
}
```

## Example 3

- 동작 파라미터화 예제

사과의 어떤 속성에 기초해서 불리언 값을 반환(예를 들어 사과가 녹색인가? 150그램 이상인가?). 참 또는 거짓을 반환하는 함수를 `프레디케이트` 라고 한다.

`선택 조건을 결정하는 인터페이스`를 정하자.

```java
public interface ApplePredicate {
  boolean test (Apple apple);
}
```

다음 예제 처럼 다양한 선택 조건을 대표하는 여러 버전의 ApplePredicate를 정의할 수 있다.

```java
public class AppleHeavyWeightPredicate implements ApplePredicate {
  public boolean test(Apple apple) {
    return apple.getWeight() > 150;
  }
}

public class AppleGreenColorPredicate implements ApplePredicate {
  public boolean test(Apple apple) {
    return GREEN.equals(apple.getColor());
  }
}
```

위 조건에 따라 filter 메서드가 다르게 동작할 것이라고 예상할 수 있다. 이를 `전략 디자인패턴(strategy design patter)`이라고 부른다.

전략 디자인 패턴은 각 알고리즘(전략이라 부르는)을 캡슐화 하는 알고리즘 패밀리를 정의해둔 다음에 런타임에 알고리즘을 선택하는 기법이다.

여기서는 ApplePredicate가 `알고리즘 패밀리` 이며, 이를 구현한 클래스들이 `전략` 이다.
