# 전략(Strategy)

전략 패턴은 한 유형의 알고리즘을 보유한 상태에서 런타임에 적절한 알고리즘을 선택하는 기법이다.

전략 패턴은 세 부분으로 구성된다.

- 알고리즘을 나타내는 인터페이스(Strategy Interface)
- 다양한 알고리즘을 나타내는 한 개 이상의 인터페이스 구현(ConcreteStrategyA, ConcreteStrategyB 같은 구체적인 구현 클래스)
- 전략 객체를 사용하는 한 개 이상의 클라이언트

> 즉, `전략 알고리즘 인터페이스 생성` > `구현체 생성` > `전략 객체 사용` 으로 나뉜다.

## Strategy Example

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

## 람다 표현식으로 리팩토링

```java
Validator numericValidator = new Validator((String s) -> s.matches("[a-z]+"));
boolean b1 = numericValidator.validate("aaaa");
Validator lowerCaseValidator = new Validator((String s) -> s.matches("\\d+"));
boolean b2 = lowerCaseValidator.validate("bbbb");
```
