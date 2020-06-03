# 이터레이터(Iterator)

- 디자인 원칙
  - 단일 역할 원칙 : 클래스를 바꾸는 이유는 한 가지 뿐이어야 한다.

> 컬렉션 구현 방법을 노출시키지 않으면서도 그 집합체 안에 들어있는 모든 항목에 접근할 수 있게 해주는 방법을 제공한다.

이터레이터의 핵심은 객체를 저장하는 방식을 보여주지 않으면서도 클라이언트로 하여금 객체들에게 일일이 접근할 수 있게 해준다는 것이다.

## Example 1

만약 A 음식점에서는 음식 메뉴를 저장하는 방식을 ArrayList 로 하고 있고, B 음식점에서는 음식 메뉴를 저장하는 방식을 배열로 하고 있다고 했을때, A 와 B 회사가 합병한다고 하면 ArrayList 와 배열에 대한 순환문 2개가 필요할 것이다.

- ArrayList

```java
for(int i=0; i<breakfastItems.size(); i++) {
  MenuItem menuItem = (MenuItem)breakfastItems.get(i);
}
```

- 배열

```java
for(int i=0; i<lunchItems.length; i++) {
  MenuItem menuItem = lunchItems[i];
}
```

이렇게 바뀌는 부분이 순환문 쪽이므로, `바뀌는 부분을 캡슐화 하라` 라는 디자인 원칙에 따라 객체의 컬렉션에 대한 반복작업을 처리하는 방법을 캡슐화한 Iterator 라는 객체를 만들어 사용하면 아래와 같다.

- ArrayList

```java
Iterator iterator = breakfastMenu.createIterator();
while(iterator.hasNext()) {
  MenuItem menuItem = (MenuItem)iterator.next();
}
```

- 배열

```java
Iterator iterator = lunchMenu.createIterator();
while(iterator.hasNext()) {
  MenuItem menuItem = (MenuItem)iterator.next();
}
```

Iterator interface 를 이용하면 배열, 리스트, 해시테이블 등 어떤 종류의 객체 컬렉션에 대해서도 반복자를 구현할 수 있다.

## Example 2

- Diner Menu 에 Iterator 추가하기전에 Iterator interface 정의하기

```java
public interface Iterator {
  boolean hasNext();
  Object next();
}
```

- Iterator 구상 클래스

```java
public class DinnerMenuIterator implements Iterator {
  MenuItem[] items;
  int position = 0;
  
  public DinnerMenuIterator(MenuItem[] items) {
    this.items = items;
  }
  
  public Object next() {
    MenuItem menuItem = items[position];
    position = position + 1;
    return menuItem;
  }
  
  public boolean hasNext() {
    if(position >= items.length || items[position] == null) {
      return false;
    } else {
      return true;
    }
  }
}
```

- Dinner Menu 에서 Iterator 사용하기

```java
public class DinnerMenu {
  static final int MAX_ITEMS = 6;
  int numberOfItems = 0;
  MenuItem[] menuItems;
  
  // 생성자
  
  // addItem 메서드 호출

  /**
   * 클라이언트에서는 menuItem 이 어떻게 관리되는지는 물론 DinnerMenuIterator 가 어떤 식으로 구현되어있는지 알 필요가 없다.
   * 그냥 반복자를 써서 메뉴에 들어있는 항목들에 대해 접근할 수 있으면 된다.
   */
  public Iterator createIterator() [
    return new DinnerMenuIterator(menuItems);
  }
}
```

- 웨이트리스 코드

```java
public class Waitress {
  PancakeHouseMenu pancakeHouseMenu;
  DinnerMenu dinnerMenu;
  
  public Waitress(PancakeHouseMenu pancakeHouseMenu, DinnerMenu dinnerMenu) {
    this.pancakeHouseMenu = pancakeHouseMenu;
    this.dinnerMenu = dinnerMenu;
  }
  
  public void printMenu() {
    Iterator pancakeIterator = pancakeHouseMenu.createIterator();
    Iterator dinnerIterator = dinnerMenu.createIterator();
    
    printMenu(pancakeIterator);
    printMenu(dinnerIterator);
  }
  
  private void printMenu(Iterator iterator) {
    while(iterator.hasNext()) {
      MenuItem menuItem = (MenuItem)iterator.next();
      System.out.println(menuitem.getName());
    }
  }
  
  // 기타 메서드
  
}
```

- 테스트

```java
public class MenuTestDrive {
  public static void main(String[] args) {
    PancakeHouseMenu pancakeHouseMenu = new PancakeHouseMenu();
    DinnerMenu dinnerMenu = new DinnerMenu();
    
    Waitress waitress = new Waitress(pancakeHouseMenu, dinnerMenu);
    waitress.printMenu();

  }
}
```

## Iterator Interface

```java
public interface Iterator<E> {
    /**
     * Returns {@code true} if the iteration has more elements.
     * (In other words, returns {@code true} if {@link #next} would
     * return an element rather than throwing an exception.)
     *
     * @return {@code true} if the iteration has more elements
     */
    boolean hasNext();

    /**
     * Returns the next element in the iteration.
     *
     * @return the next element in the iteration
     * @throws NoSuchElementException if the iteration has no more elements
     */
    E next();

    /**
     * Removes from the underlying collection the last element returned
     * by this iterator (optional operation).  This method can be called
     * only once per call to {@link #next}.  The behavior of an iterator
     * is unspecified if the underlying collection is modified while the
     * iteration is in progress in any way other than by calling this
     * method.
     *
     * @throws UnsupportedOperationException if the {@code remove}
     *         operation is not supported by this iterator
     *
     * @throws IllegalStateException if the {@code next} method has not
     *         yet been called, or the {@code remove} method has already
     *         been called after the last call to the {@code next}
     *         method
     */
    void remove();
}
```
