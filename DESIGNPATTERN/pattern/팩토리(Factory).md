# 팩토리(Factory)

- 디자인 원칙
  - 의존성 뒤집기 원칙(Dependency Inversion Principle) : 추상화된 것에 의존하도록 만들어라. 구상 클래스에 의존하도록 만들지 않도록 한다.

> 팩토리 메서드 패턴에서는 객체를 생성하기 위한 인터페이스를 정의하는데, 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정하게 만든다. 팩토리 메서드 패턴을 이용하면 클래스의 인스턴스를 만드는 일을 서브클래스에게 맡기게 된다.

`팩토리 메서드 패턴에서는 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정한다`

- 구현 방법
  - 생산자(Creator) 클래스
    - 추상 생산자 클래스
      - 제품을 생산하는 `추상 팩토리 메서드` 를 가지고 있다.
    - 구상 생산자(concrete creator) 클래스 
  - 제품(Product) 클래스
    - 추상 팩토리 클래스
    - 구상 클래스

- 의존성 뒤집기 원칙을 지키기 위한 가이드라인 (항상 지키라는 것은 아님)
  - 어떤 변수에도 구상 클래스에 대한 래퍼런스를 저장하면 안된다.
  - 구상 클래스에서 유도된 클래스를 만들지 않는다.
    - 이렇게 되면 특정 구상 클래스에 의존하게 된다.
  - 베이스 클래스에 이미 구현되어 있던 메서드를 오버라이드하지 않는다.
    - 베이스 클래스에서 메서드를 정의할 때는 모든 서브클래스에서 공유할 메서드만 생성해야 한다.
    
## Example 1

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

간단한 팩토리에서는 객체 생성을 캡슐화 하는 방법을 사용하긴 하지만 팩토리 메서드 패턴처럼 강력한 유연성을 제공하진 못한다. 생성하는 제품을 마음대로 변경할 수 없기 때문이다.

정적 팩토리 메서드(Static Factory Method)를 사용하는 경우도 있는데 객체를 생성하기 위해서 객체의 인스턴스를 만들지 않아도 되지만, 객체 생성 메서드의 행동을 변경시킬 수 는 없다.

## Example 2

피자를 만드는 활동 자체는 전부 PizzaStore 클래스에 국한시키면서 분점마다 고유의 스타일을 살릴 수 있도록 하는 방법

```java
public abstract class PizzaStore {
  public Pizza orderPizza(String type) {
    Pizza pizza;
    
    /*
     * 팩토리 객체가 아닌 PizzaStore 에 있는 createPizza 호출
     */
    pizza = createPizza(type);
    
    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    
    return pizza;
  }

  /*
   * 팩토리 메서드를 추상 메서드로 선언
   * 피자 객체의 인스턴스를 만드는 일은 PizzaStore 의 서브클래스에 있는 createPizza() 메서드에서 처리
   * 매개변수 팩토리 메서드를 사용할 때 단점은, 형식 안정성(type-safety)에 지장이 있을 수 있다.
   * 형식 안정성을 조금 더 잘 보장해 줄 수있는 기법은 매개변수 형식을 나타내기 위한 객체를 만들거나
   * enum, 이나 정적 상수등을 사용할 수 있다.
   */
  protected abstract PIZZA createPizza(String type);
}
```

- `팩토리 메서드의 특징`
  - 팩토리 메서드는 추상 메서드로 선언하여 서브클래스에서 객체 생성을 책임지도록 한다.
  - 팩토리 메서드에서는 특정 제품(객체)를 리턴하며, 그 객체는 보통 수퍼클래스에서 정의한 메서드 내에서 쓰이게 된다.
  - 팩토리 메서드는 클라이언트에서 실제로 생성되는 구상 객체가 무엇인지 알 수 없게 만드는 역할도 한다.
  - 팩토리 메서드를 만들 때 매개변수를 써서 만들어낼 객체 종류를 선택할 수 도 있다.
  - 팩토리 메서드 패턴은 객체의 생성 코드를 별도의 클래스/메서드로 분리함으로써 객체 생성의 변화에 대비에 유용하다.


- 뉴욕풍, 시카고풍 등 피자를 만들 수 있음 

```java
public class NYPizzaStore extends PizzaStore {
  Pizza createPizza(String item) {
    if(item.equals("cheese")) {
      return new NYStyleCheesePizza();
    } else if (item.equals("pepperoni") {
      return new NYStylePepperoniPizza();
    }
  }
}
```

상위클래스인 PizzaStore 의 orederPizza() 에서는 어떤 피자가 만들어지는지 전혀 알 수 없다. 해당 메서드는 피자를 준비하고 굽고 자르고 포장하는 작업을 처리할 뿐이다.

- 피자 클래스

```java
public abstract class Pizaa {
  String name;
  String dough;
  Strin sauce;
  ArrayList toppings = new ArrayList();
  
  void prepare() {
    System.out.println("Prepareing" + name);
    System.out.println("Tossing  dough ...");
    System.out.println("Adding sauce ...");
    ...
  }
  
  void bake() [
    System.out.println("굽는다");
  }
  
  void cut() {
    System.out.println("자른다");
  }
  
  void box() {
    System.out.println("포장한다");
  }
  
  public String getName() {
    return name;
  }

}
```

- 서브클래스

```java
public class NYStyleCheesePizza extends Pizza {
  public NYStyleCheesePizza() {
    name = "NY Style Sauce and Cheese Pizza";
    dough = "Thin Crust Dough";
    sauce = "Marinara Sauce";
    toppings.add("Grated Reggiano Cheese");
  }
}
```

- 피자 만들기

```java
public class PizzaTestDrive {
  public static void main(String[] args) {
    PizzaStore nyStore = new NYPizzaStore();
    PizzaStore chicagoStore = new ChicagoPizzaStore();
    
    Pizza pizza = nyStore.oredrPizza("cheese");
    System.out.println("Ethan ordered a " + pizza.getName());
    
    pizza = chicagoStore.orderPizza("cheese");
    System.out.println("Joel ordered a " pizza.getName());
  }
}
```

- 고수준 구성요소
  - 다른 저수준 구성요소에 의해 정의되는 행동이 들어있는 구성요소를 뜻한다.
  - ex) PizzaStore 의 행동은 피자에 의해 정의되기 때문에, PizzaStore 는 고수준 구성요소이다.
- 저수준 구성요소
  - PizzaStore 에서 사용하는 피자 
  
- 원재료 공장 만들기

반죽, 소스 치즈 같은걸 만들어야 한다.

- 모든 원재료를 생산할 팩토리를 위한 인터페이스 정의

```java
public interface PizzaIngredientFactory {
  public Dough createDough();
  public Suce createSauce();
  public Cheese createCheese();
  public Pepperoni createPepperoni();
  ...
}
```

- 뉴욕 원재료 공장

```java
public class NYPizzaIngredientFactory implements PizzaIngredientFactory {
  public Dough createDough() {
    return new ThinCrustDough();
  }
  public Sauce createSauce() {
    return new MarinaraSauce();
  }
  ...
}
```

- Pizza 추상 팩토리 메서드 수정

```java
public abastract class Pizza {
  // 원재료가 추가된다.
  Dough dough;
  Sauce sauce;
  ...
  
  // prepare() 메서드를 추상 메서드로 만들고 이 부분에서 피자를 만드는 데 필요한 재료드를 정돈한다.
  abstract void prepare();
  
  // 동일

}
```

## 정적 팩터리 메서드(static factory method)

정적 팩토리 메서드란 `객체 생성의 역할을 하는 클래스 메서드` 를 의미한다.

- java.time.LocalTime 의 정적 팩터리 메서드

```java
public static LocalTime of(int hour, int minute) {
  ChronoField.HOUR_OF_DAY.checkValidValue((long)hour);
  if (minute == 0) {
    return HOURS[hour];
  } else {
    ChronoField.MINUTE_OF_HOUR.checkValidValue((long)minute);
    return new LocalTime(hour, minute, 0, 0);
  }
}

// hour, minutes 을 인자로 받아서 9시 30분을 의미하는 LocalTime 객체를 반환한다.
LocalTime openTime = LocalTime.of(9, 30); 
```


### 생성자와의 차이 점

Effective Java 에서 가장 첫 번째로 소개되는 내용이 [생성자 대신 정적 팩토리 메서드를 고려하라](https://github.com/BAEKJungHo/effectiveJava/blob/master/%EA%B0%9D%EC%B2%B4%20%EC%83%9D%EC%84%B1%EA%B3%BC%20%ED%8C%8C%EA%B4%B4/01.%20%EC%83%9D%EC%84%B1%EC%9E%90%20%EB%8C%80%EC%8B%A0%20%EC%A0%95%EC%A0%81%20%ED%8C%A9%ED%84%B0%EB%A6%AC%20%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC%20%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC.md#%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%84%B0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC) 이다.

정적 팩터리 메서드(static factory method) 가 생성자보다 우위를 차지하는 점은 다음과 같다.

#### 이름을 가질 수 있다.

createLottoNumber, createCar 등 이름을 가지게되어 `가독성`이 좋아지고, `객체 생성의 목적`을 나타낼 수 있다.

#### 호출할 때마다 새로운 객체를 생성할 필요가 없다.

enum 과 같이 자주 사용되는 요소의 개수가 정해져있다면 해당 개수만큼 미리 생성해놓고 조회([객체 캐싱](https://github.com/BAEKJungHo/TIL/blob/master/Java/Optimization/%EA%B0%9D%EC%B2%B4%20%EC%BA%90%EC%8B%B1(Objects%20Caching).md))할 수 있는 구조로 만들수 있다. 

생성자의 접근 제한자를 private 으로 설정하면 객체 생성을 정적 팩터리 메서드로만 가능하게하여, 객체 생성을 제한할 수 있다.

#### 하위 자료형 객체를 반환할 수 있다.

- Super Class : Vehicle
- Sub Class : Car, Airplane, Train

클래스가 다음과 같은 구조로 되어있을 때, 여행지에 따라 탈것이 결정되는 정적 팩터리 메서드를 만들 수 있다.

```java
public class Vehicle {
  ...
  public static Vehicle(String country) {
    if(country.equals("Cesko")) {
      return new Airplane();
    }
    ...
  }
  ...
}
```

#### 객체 생성을 캡슐화 할 수 있다.

생성자를 클래스의 메서드 안으로 숨기면서 내부 상태를 외부에 드러낼 필요없이 객체 생성 인터페이스 단순화 시킬 수 있다.

```java
Car carDto = CarDto.from(car); // 정적 팩토리 메서드를 쓴 경우
CarDto carDto = new CarDto(car.getName(), car.getPosition); // 생성자를 쓴 경우, 내부 구현이 다 드러난다.
```

### 네이밍 컨벤션

- `from` : 하나의 매개 변수를 받아서 객체를 생성.
- `of` : 여러개의 매개 변수를 받아서 객체를 생성.
- `getInstance | instance` : 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음.
- `newInstance | create` : 새로운 인스턴스를 생성.
- `get[OtherType]` : 다른 타입의 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음.
- `new[OtherType]` : 다른 타입의 새로운 인스턴스를 생성.
