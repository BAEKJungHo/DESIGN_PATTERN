# 데코레이터(Decorator)

- 디자인 원칙
  - 클래스는 확장에 대해서는 열려 있어야 하지만, 코드 변경에 대해서는 닫혀 있어야 한다.
    - ex) 옵저버 패턴에서 옵저버를 새로 추가하면 Subject 자체에 코드를 추가하지 않아도 된다.

> 데코레이터 패턴에서는 객체에 추가적인 요건을 동적으로 첨가한다. 데코레이터는 서브클래스를 만드는 것을 통해서 기능을 유연하게 확장할 수 있는 방법을 제공한다.  
  
데코레이터 패턴은 `객체 작성이라는 형식으로 실행중에 클래스를 꾸미는 방법` 이다. 객체에 추가 요소를 동적으로 더할 수 있고, 서브클래스를 만드는 경우 (상속)에 비해 훨씬 유연하게 확장이 가능하다. 데코레이터 패턴을 사용하는 경우는 `기존 코드는 건드리지 않은 채로 확장을 통해서 새로운 행동을 추가하고 싶은 경우` 에 사용하면 된다. 데코레이터 패턴은 상속을 이용해서 형식을 맞춘다. 행동을 물려받는게 목적이 아님. 만약, 상속을 통해 행동을 물려받게 되면 서브클래스에서 오버라이딩한 메서드만 쓰기 때문에 안좋다. 따라서 구성(인스턴스 변수로 다른 객체를 저장)을 이용해서 유연성을 확보한다.

# Example 1

- 상속을 사용하는 경우
  - 음료 가격과 첨가물(샷, 시럽, 우유, 휘핑 크림 등) 가격을 합한 총 가격을 계산하는 방법
- 데코레이터를 사용하는 경우
  - DarkRoast (cost() 음료 가격을 계산하는 메서드를 가지고 있음) 객체에서 시작
  - 손님이 모카를 주문했으니 Mocha 객체를 만들고 그 객체로 DarkRoast 를 감싼다.
  - 손님이 휘핑 클림을 주문했으니 Whip 객체를 만들고 그 객체로 Mocha 를 감싼다.
  - 가격 계산을 위해 가장 바깥 쪽에 있는 Whip 데코레이터 의 cost() 호출
  - 그러면 Whip 에서는 그 객체가 장식하고 있는 객체한테 가격을 위임한다.
 
 - 데코레이터 패턴 특징
  - 데코레이터의 상위클래스는 자신이 장식하고 있는 객체의 상위클래스와 같다.
  - 한 객체를 여러 개의 데코레이터로 감쌀 수 있다.
  - 데코레이터는 자신이 감싸고 있는 객체와 같은 상위클래스를 가지고 있기 대문에 원래 객체가 들어갈 자리에 데코레이터 객체를 집어넣어도 상관 없다.
  - 데코레이터는 자신이 장식하고 있는 객체에게 어떤 행동을 위임하는 것 외에 원하는 추가적인 작업을 수행할 수 있다.
  - 객체는 언제든지 감쌀 수 있기 때문에 실행중에 필요한 데코레이터를 마음대로 적용할 수 있다.

- Beverage 클래스

```java
public abstract class Beverage {
  String description = "제목없음";
  
  public String getDescription() {
    return description;
  }
  
  public abstract double cost();
} 
```

- 첨가물 데코레이터(추상클래스)

```java
/**
 * Beverage 객체가 들어갈 자리에 들어갈 수 있어야 하므로 Beverage 확장
 */
public abstract class CondimentDecorator extends Beverage {
  /**
   * 모든 첨가물 데코레이터에서 getDescription() 메서드를 새로 구현해야 한다.
   */
  public abstract String getDescription();
}
```

- 음료 코드 구현
  - 음료를 설명하는 문자열을 설정해야 하고 cost() 메서드를 구현해야 한다.

```java
public class Espresso extends Beverage {
  public Espresso() {
    description = "에스프레소";
  }
  
  public double cost() {
    return 1.99;
  }
}

public class HouseBlend extends Beverage {
  public HouseBlend() {
    description = "하우스 블랜드 커피";
  }
  
  public double cost() {
    return .89;
  }
}
```

- 첨가물용 코드 구현(데코레이터 구현체)

```java
public class Mocha extends CondimentDecorator {
  // 감싸고자 하는 음료를 저장하기 위한 변수
  Beverage beverage; 
  
  /**
   * 데코레이터 생성자에 감싸고자 하는 객체로 설정하기 위한 생성자
   * 데코레이터의 생성자에 감싸고자 하는 음료 객체를 전달하는 방식을 사용
   */ 
  public Mocha(Beverage beverage) {
    this.beverage = beverage;
  }
  
  public String getDescription() {
    return beverage.getDescription() + ", 모카";
  }
  
  /**
   * 음료 가격에 Mocha 가격을 
   */
  public double cost() {
    return .20 + beverage.cost();
  }
}
```
