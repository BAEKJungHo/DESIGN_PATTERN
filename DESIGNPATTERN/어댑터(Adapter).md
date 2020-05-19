# 어댑터(Adapter)

- 디자인 원칙
  - 최소지식원칙 : 정말 친한 친구하고만 얘기하라.

- 어댑터(Adapter)

> 한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 다른 인터페이스로 변환한다. 어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있다.

어댑터의 역할은 국산 플러그와 유럽식 소켓 사이에서 국산 전원 플러그를 유럽식 소켓에 꽂을 수 있게 해주는 역할을 한다. 만약 새로운 업체에서 제공한 클래스 라이브러리를 사용해야 하는데 기존 업체에서 사용하던 인터페이스와 다른경우, 두 코드는 변경하지 않게하고 어댑터를 만들어서 연결 시킬 수 있다.

## UML

- Client
   - 클라이언트는 타겟 인터페이스만 바라본다.
- Target Inteface
- Adapter
   - 어댑터에서 타겟 인터페이스를 구현한다.
- Adaptee
  - 모든 요청은 어댑티에게 위임된다.
  
## Example 1

- 오리 인터페이스

```java
public interface Duck {
  public void quack();
  public void fly();
}
```

- Duck 을 구현하는 MallardDuck 클래스

```java
public class MallardDuck implements Duck {
  public void quack() {
    System.out.println("Quack");
  }
  public void fly() {
    System.out.println("I'm flying");
  }
}
```

- 새로 적용시켜야할 칠면조 인터페이스

```java
public interface Turkey {
  public void gobble();
  public void fly();
}
```

- Turkey 를 구현하는 WildTurkey 클래스

```java
public class WildTurkey implements Turkey {
  public void gobble() {
    System.out.println("Gobble gobble");
  }
  public void fly() {
    System.out.println("I'm flying a short distance");
  }
}
```

만약 Duck 객체가 모자라서 Turkey 객체를 대신 사용해야 하는 경우 어댑터를 만들어야 한다.

- 어댑터

1. 적용시킬 형식의 인터페이스를 구현한다.
2. 원래 형식의 객체에 대한 래퍼런스가 필요하다. 생성자에서 래퍼런스를 받아서 처리
3. 인터페이스에 들어있는 메서드를 구현
5. 메서드 오버라이딩을 통해 메서드 내부를 새로 적용시킬 객체 동작에 맞게 

```java
public class TurkeyAdapter implements Duck {
  Turkey turkey;
  
  public TurkeyAdapter(Turkey turkey) {
    this.turkey = turkey;
  }
  
  public void quack() {
    turkey.gobble();
  }
  
  public void fly() {
    for(int i=0; i<5; i++) {
      turkey.fly();
    }
  }
  
}
```

## Example 2

Enummeration 과 Iterator 를 연결하는 어댑터 클래스 만들기

- Iterator (타겟)
  - hasNext()
  - next()
  - remove()
- Enummeration
  - hasMoreElements()
  - nextElement()

메서드 구성을 보니 remove() 가 Enummeration 에는 존재하지 않아서 어댑터 클래스를 만들어야 한다.

- 어댑터

```java
public class EnummerationIterator implements Iterator {
  Enummeration enum;
  
  public EnumerationIterator(Enumeration enum) {
    this.enum = enum;
  }
  
  public boolean hasNext() {
    return enum.hasMoreElements();
  }
  
  public Object next() {
    return enum.nextElement();
  }
  
  public void remove() {
    throw new UnsupportedOperationException();
  }
}
```
