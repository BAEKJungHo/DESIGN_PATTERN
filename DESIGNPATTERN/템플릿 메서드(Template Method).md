# 템플릿 메서드(Template Method)

- 디자인 원칙
  - 할리우드 원칙 : 먼저 연락하지 마세요. 저희가 연락 드리겠습니다.
  - 장점 : 의존성 부패(dependency rot)를 방지할 수 있다.
  - 의존성 부패(dependency rot)
    - 어떤 고수준 구성요소가 저수준 구성요소에 의존하고, 그 저수준 구성요소는 다시 고수준 구성요소에 의존하고 등..

> 어떤 작업 알고리즘의 골격을 정의한다. 일부 단계는 서브클래스에서 구현하도록 할 수 있다. 템플릿 메서드를 이용하면 알고리즘의 구조는 그대로 유지하면서 특정 단계만 서브클래스에서 새로 정의하도록 할 수 있다.

- 알고리즘을 캡슐화한다. (서로 공통점이 있는 메서드를 일반화하여 새로 만든다.)
- 어떤 알고리즘에 대한 템플릿 역할을 메서드가 한다. (`메서드 내부에는 알고리즘의 각 단계를 나타내는 메서드들로 구성`)
- 서브클래스에서 특정 알고리즘에 선택적으로 적용되야 하는 경우에는 `후크(hook)` 를 사용한다.

## Example 1

커피와 차를 만든다고할 때, 두 클래스를 먼저 비교해보자.

- Coffee

```java
void prepareRecipe() {
  boilWater();
  brewCoffeeGrinds();
  pourInCup();
  addSugarAndMilk();
}
```

- Tea

```java
void prepareRecipe() {
  boilWater();
  steepTeaBag();
  pourInCup();
  addLemon();
}
```

두 메서드에서 서로 다른 메서드를 하나로 합쳐서 만든다.

- Coffee + Tea

```java
void prepareRecipe() {
  boilWater();
  brew(); // 일반화된 메서드
  pourInCup();
  addCondiments(); // 일반화된 메서드
}
```

- CaffeineBeverage 

```java
public abstract class CaffeineBeverage {

  /**
   * Template Method
   * 템플릿 내에서 알고리즘의 각 단계는 메서드로 표현된다.
   * 슈퍼클래스에서 처리되는 메서드도 있고, 서브클래스에서 처리되는 메서드도 있다.
   * 서브클래스에서 처리될 메서드는 abstract 으로 선언해야한다.
   * 서브클래스에서 이 메서드를 오버라이드해서 아무렇게나 음료를 만들지 못하도록 final 로 선언
   */
  final void prepareRecipe() {
    boilWater();
    brew();
    pourInCup();
    if(customerWantsCondiments()) {
      addCondiments();
    }
  }
  
  // Coffee 와 Tea 에서 서로 다른 방식으로 구현해야 하므로 추상 메서드로 선언
  abstract void brew();
  
  // Coffee 와 Tea 에서 서로 다른 방식으로 구현해야 하므로 추상 메서드로 선언
  abstract void addCondiments();
  
  // 서로 기능이 동일한 메서드는 슈퍼클래스에서 기능 구현
  void boilWater() {
    // 물 끓이기
  } 
  
  // 서로 기능이 동일한 메서드는 슈퍼클래스에서 기능 구현
  void pourInCup() {
    // 컵에 따르기
  }
  
 /**
  * 후크(hook) : 서브클래스에서 알고리즘이 선택적으로 적용되야 하는 경우
  * 필요한 서브클래스에서는 오버라이딩 하여 구현한다.
  */
  boolean customerWantsCondiments() {
    return true;
  }

}
```

- 후크 활용

```java
public class Coffee extends CaffeineBeverage {
  
  // 생략
  
  // hook
  public boolean customerWantsCondiments() {
    if(...) {
      return true;
    } else {
      return false;
    }
  }
  
}
```
