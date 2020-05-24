# 컴포지트(Composite)

> 컴포지트 패턴을 이용하면 객체들을 트리 구조로 구성하여 부분과 전체를 나타내는 계층구조로 만들 수 있다. 이 패턴을 이용하면 클라이언트에서 개별 객체와 다른 객체들로 구성된 복합 객체(composite)를 똑같은 방법으로 다룰 수 있다.

이터레이터의 핵심은 객체를 저장하는 방식을 보여주지 않으면서도 클라이언트로 하여금 객체들에게 일일이 접근할 수 있게 해준다는 것이다.

컴포지트 패턴은 단일 역할 원칙을 깨면서 대신에 투명성(transparency)을 확보하기 위한 패턴이다. Component 클래스에는 두 종류의 기능이 모두 들어있다 보니까 안정성은 약간 떨어진다.
하지만, 컴포지트 패턴을 배움으로써 가이드라인을 항상 따를 필요는 없고 상황에 따라 원칙을 적절하게 사용해야한다는것을 보여준다.

## Example 1

컴포지트 패턴을 이용하면 메뉴안의 메뉴를 구현하기 위해서는, 부모와 자식을 나타내는 트리구조가 적합하다.

- MenuComponent

```java
/**
 * 모든 구성요소에서는 MenuComponent 를 구현해야만 한다. 하지만 Leaf 와 Node 는 각각 역할이 다르다.
 * 따라서, 자기 역할에 맞지 않은 메서드에 대해서는 예외를 던지는 방법을 사용한다.
 */
public abstract class MenuComponent {
  
  public void add(MenuComponent menuComponent) {
    throw new UnsupportedOperationException();
  }
  
  public void remove(MenuComponent menuComponent) {
    throw new UnsupportedOperationException();
  }
  
  public MenuComponent getChild(int i) {
    throw new UnsupportedOperationException();
  }
  
  public String getName() {
    throw new UnsupportedOperationException();
  }
  
  public Strin getDescription() {
    throw new UnsupportedOperationException();
  }
  
  public double getPrice() {
    throw new UnsupportedOperationException();
  }
  
  public booleanisVegetarian() {
    throw new UnsupportedOperationException();
  }
  
  public void print() {
    throw new UnsupportedOperationException();
  }
  
}
```

- MenuItem

```java
public class MenuItem extends MenuComponent {
  String name;
  String description;
  boolean vegetarian;
  double price;
  
  public MenuItem(String name, String description, boolean vegetarian, double price) {
    this.name = name;
    this.description = description;
    this.vegetarian = vegetarian;
    this.price = price;
  }
  
  public String getName() {
    return name;
  }
  
  public String getDescription() {
    return description;
  }
  
  public double getPrice() {
    return price;
  }
  
  public boolean isVegetarian() [
    return vegetarian;
  }
  
  public void print() {
    System.out.println(getName());
    if(isVegetarian()) {
      System.out.println("v");
    }
    System.out.println(getPrice());
  }
  
}
```

- Menu 와 print() 메서드

Menu 는 복합 객체이며 그 안에는 MenuItem 과 Menu 가 모두 들어있을 수 있다. 따라서 메뉴의 print() 메서드를 호출하면 그 안에 있는 모든 구성요소들이 출력되야 한다.

```java
public class Menu extends MenuComponent {
  ArayList menuComponents = new ArrayList();
  String name;
  String description;
  
  // 생성자
  
  // 기타 메서드
  
  // 재귀 호출 
  public void print() {
    Iterator iterator = menuComponents.iterator();
    while(iterator.hasNext()) {
      MenuComponent menuComponent = (MenuComponent)iterator.next();
      menuComponent.print();
    }
  }
  
}
```

- Waitress 

```java
public class Waitress {
  MenuComponent allMenus;
  
  /**
   * 다른 모든 메뉴를 포함하고 있는 최상위 메뉴 구성 요소만 넘겨주면 된다.
   */
  public Waitress(MenuComponent allMenus) {
    this.allMenus = allMenus;
  }
  
  public void printMenu() {
    allMenus.print();
  }
  
}
```

- 테스트 코드

```java
public class MenuTestDrive {
  public static void main(String[] args) {
    MenuComponent pancakeHouseMenu = new Menu("팬케이크하우스", "아침메뉴");
    MenuComponent dinnerMenu = new Menu("객체마을식당", "저녁메뉴");
    MenuComponent cafeMenu = new Menu("카페메뉴", "아침메뉴");
    MenuComponent dessertMenu = new Menu("디저트메뉴", "디저트를 즐겨 보세요!");
    
    MenuComponent allMenus = new Menu("전체 메뉴", "전체 메뉴");
    
    allMenus.add(pancakeHsoueMenu);
    allMenus.add(dinnerMenu);
    allMenus.add(cafeMenu);
    
    dinnerMenu.add(new MenuIte("파스타", "마리나라 소스 스파게티", true, 3.89));
    dinnerMenu.add(dessertMenu);
    
    ...
    
    Waitress waitress = new Waitress(allMenus);
    waitress.printMenu();
  }
}
```

- 복합 반복자

복합 객체 안에 들어있는 MenuItem 에 대해 반복작업을 할 수 있게 해주는 기능을 제공한다.

public class CompositeIterator implements Iterator {
  Stack stack = new Stack();
  
  public CompositeIterator(Iterator iterator) {
    stack.push(iterator);
  }
  
  public Object next() {
    if(hasNext()) {
      Iterator iterator = (Iterator)stack.peek();
      MenuComponent component = (MenuComponent) iterator.next();
      if(component instanceof Menu) {
        stack.push(component.createIterator());
      }
      return component;
    } else {
      return null;
    }
  }
  
  public boolean hasNext() {
    if(stack.empty()) {
      return false;
    } else {
      Iterator iterator = (Iterator)stack.peek();
      if(!iterator.hasNext()) {
        stack.pop();
        return hasNext();
      } else {
        return true;
      }
  }
  
  public void remove() {
    throw new UnsupportedOperationException();
  }
}
```
