# 전략(Strategy)

- 디자인 원칙
  - 상속 보다는 구성(Composition) 을 사용하라. (A 에는 B 가 있다.)

> 전략 패턴은 한 유형의 알고리즘을 보유한 상태에서 런타임에 적절한 알고리즘을 선택하는 기법이다. 알고리즘군을 정의하고 각각을 캡슐화하여 교환해서
사용할 수 있도록 만든다. 전략을 활용하면 알고리즘을 사용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있다.

전략 패턴은 세 부분으로 구성된다.

- 알고리즘을 나타내는 인터페이스(Strategy Interface)
- 다양한 알고리즘을 나타내는 한 개 이상의 인터페이스 구현(ConcreteStrategyA, ConcreteStrategyB 같은 구체적인 구현 클래스)
- 전략 객체를 사용하는 한 개 이상의 클라이언트

> 즉, `전략 알고리즘 인터페이스 생성` > `구현체 생성` > `전략 객체 사용` 으로 나뉜다.

## Strategy Example 1

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

## Strategy Example 2

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
