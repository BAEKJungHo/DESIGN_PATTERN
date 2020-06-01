# 생성자를 팩토리 메서드로 전환(Replace Constructor with Factory Mehtod)

> 객체를 생성할 때 단순한 생성만 수행하게 해야 할 땐 생성자를 팩토리 메서드로 교체하자. 

## 동기

생성자를 팩토리 메서드로 전환을 실시해야 할 가장 확실한 상황은 `분류 부호를 하위클래스로 바꿀 때 발생한다.` 분류 부호를 사용해 작성한 객체가 있는데
현시점에서 하위클래스가 필요해졌다. 어느 하위클래스를 사용할지는 분류 부호에 따라 달라진다. 하지만 생성자는 요청된 객체의 인스턴스 반환만 할 수 있다.
따라서 생성자를 팩토리 메서드로 바꿔야 한다.

팩토리 메서드는 값을 참조로 변환을 실시하기 위해 꼭 필요하다. 팩토리 메서드는 매개변수의 숫자와 타입을 벗어나는 다른 생성 동작을 나타낼 때도 사용할 수 있다.
  
## Before Refactoring

```java
class Employee {
  // 필드
  
  Employee(int type) {
    this.type = type;
  }
}
```

## After Refactoring

```java
class Employee {
  // 필드
  
  static Employee create(int type) {
    return new Employee(type);
  }
}
```

## Example 1

```java
class Employee {
  private int _type;
  static final int ENGINNER = 0;
  static final int SALESMAN = 1;
  static final int MANAGER = 2;
  
  Employee(int type) {
    _type = type;
  }
}
```

각 분류 부호에 해당하는 Employee 클래스의 하위클래스를 작성하려고 한다. 아래와 같이 팩토리 메서드를 작성 하면 된다.

```java
static Employee create(int type) {
  return new Employee(type);
}
```  

- 클라이언트 코드

```java
Employee eng = Employee.create(Employee.ENGINEER);
```

## Example 2

- 문자열을 사용하는 하위클래스 작성
  - 하위클래스를 클라이언트가 볼 수 없게 은폐할 수 있다.
  - 단점은 switch 문이 생긴다.
  
```java
static Employee create(int type) {
  switch(type) {
    case ENGINEER:
      return new Engineer();
    case SALSEMAN:
      return new Salesman();
    case MANAGER:
      return new Manager();
    default:
      throw new IllegalArgumentException("없는 분류 부호 값");
  }
}
```

새 하위클래스를 추가해야할 땐 이 switch 문을 수정해야한다. 하지만 이 작업은 잊기 쉽니다.

잊는 것을 방지하려면 `Class.forName` 을 사용하는 것이 좋다.

```java
static Employee create(String name) {
  try {
    return (Employee) Class.forName(name).newInstance();
  } catch(Exception e) {
    throw new IllegalArgumentException("객체 " + name + "를 인스턴스화 할 수 없음");
  }
} 
```

- 클라이언트 코드

```java
Employee.create("Enginner");
```

이 방법은 Employee 클래스의 새 하위클래스를 추가할 때 create 메서드를 업데이트할 필요가 없어져서 좋다. 하지만 단점은 오타가 발생하여 RuntimeException 이 발생할 수 도 있다.
Class.forName 을 사용하기 망설여지는 이유는 클라이언트에 클래스 이름이 노출된다는 것이다. 하지만 다른 문자열을 사용해서 팩토리 메서드로는 다른 기능을
수행할 수 있으므로 이건 그다지 단점이라고 할 순 없다.

## Example 3

- 메서드를 사용하는 하위클래스 작성

또 다른 방법으로는 직접 작성한 메서드를 사용해서 하위클래스를 은폐하는 것이다. 이 방법은 변하지 않는 두세 개의 하위클래스만 있을 때 사용 가능하다.

```java
class Person {
  static Person createMale() {
    return new Male();
  }
  static Person createFemale() {
    return new Female();
  }
}
```

- 클라이언트 코드

```java
Person kent = Person.createMale();
```

이렇게 하면 상위클래스가 하위클래스 정보를 알고 있는 상태가 유지된다. 이를 방지하려면 `Product Trader 패턴` 같은 더 복잡한 기법을 사용해야 한다.
그러나 대체로 앞에 설명한 방법으로 충분하기에 이런 복잡한 방법은 불필요하다.

## How

- 팩토리 메서드를 작성하자. 그 메서드의 내용을 기존의 생성자 호출로 수정하자.
- 모든 생성자 호출을 팩토리 메서드 호출로 바꾸자.
- 하나씩 바꿀 때마다 컴파일과 테스트를 실시하자.
- 생성자를 private 으로 선언하자.
- 컴파일하자.
