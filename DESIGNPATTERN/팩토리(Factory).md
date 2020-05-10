# 팩토리(Factory)

- 디자인 원칙
  - 의존성 뒤집기 원칙(Dependency Inversion Principle) : 추상화된 것에 의존하도록 만들어라. 구상 클래스에 의존하도록 만들지 않도록 한다.

> 팩토리 메서드 패턴에서는 객체를 생성하기 위한 인터페이스를 정의하는데, 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정하게 만든다. 팩토리 메서드 패턴을 이용하면
클래스의 인스턴스를 만드는 일을 서브클래스에게 맡기게 된다.

## Example

- 잘못 된 예제

피자가게를 운영하고 있고 피자 종류가 추가될 때마다 코드가 수정되어야 하는 예제

```java
Pizza orderPizza(String type) {
  Pizza pizza;
  
  if(type.equals("cheese")) {
    pizza = new CheesePizza();
  } else if(type.equals("greek")) {
  
  } else if ...
  
  pizza.prepare();
  pizza.bake();
  pizza.cut();
  pizza.box();
  return pizza;
}
```

위 코드에서 바뀌는 부분은 `조건 분기문` 이다. 바뀌는 부분을 캡슐화 해야 한다.

- 간단한 피자 팩토리

```java
public class SimplePizzaFactory {
  public pizza createPizza(String type) {
    if(type.equals("cheese")) {
    pizza = new CheesePizza();
  } else if(type.equals("greek")) {
  
  } else if ...
  }
}
```

여기저기 고칠 필요 없고, 팩토리 클래스만 고치면 된다.

- PizzaStore 클래스 (간단한 팩토리 적용)

```java
public class PizzaStore {
  SimplePizzaFactory factory;
  
  public PizzaStore(SimplePizzaFactory factory) {
    this.factory = factory;
  }
  
  public Pizza orderPizza(String type) {
    Pizza pizza;
    
    pizza = factory.createPizza(type);
    
    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    return pizza;
  }
}
```

간단한 팩토리(Simple Factory)는 디자인 패턴이라고 할 수는 없다. 프로그래밍을 하는데 있어서 자주 쓰이는 관용구에 가깝다.

정적 팩토리 메서드(Static Factory Method)를 사용하는 경우도 있는데 객체를 생성하기 위해서 객체의 인스턴스를 만들지 않아도 되지만, 객체 생성 메서드의 
행동을 변경시킬 수 는 없다.

