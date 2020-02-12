# 의무 체인(Chain of Responsibility, 책임 연쇄)

작업 처리 객체의 체인(동작 체인 등)을 만들 때는 의무 체인 패턴을 사용한다. 한 객체가 어떤 작업을 처리한 다음에 다른 객체로 결과를 전달하고,
다른 객체도 해야할 작업을 처리한 다음 또 다른 객체로 전달하는 식이다.

일반적으로 처리할 객체 정보를 유지하는 필드를 포함하는 작업 처리 추상 클래스로 의무 체인 패턴을 구성한다. 작업 처리 객체가 자신의 작업을 끝냈으면
다음 작업 처리 객체로 결과를 전달한다.

## 작업 처리 객체 Example

1. 작업 처리 추상 클래스 만들기

```java
public abstract class ProcessingObject<T> {
  protected ProcessingObject<T> successor;
  public void setSuccessor(ProcessingObject<T> successor) {
    this.successor = successor;
  }
  public T handle(T input) {
    T r = handelWork(input);
    if(successor != null) {
      return successor.handle(r);
    }
    return r
  }
  abstract protected T handleWork(T input);
}
```

2. 작업 처리 객체를 상속받아서 추상 메서드 오버라이딩하여 재구현

```java
public class HeaderTextProcessing extends ProcessingObject<String> {
  public String handleWork(String text) {
    return "From Raoul, Mario and Alan: " + text;
  }
}

public class SpellCheckerProcessing extends ProcessingObject<String> {
  public String handleWork(String text) {
    return text.replaceAll("labda", "lambda");
  }
}
```

3. 두 작업 처리 객체를 연결하고, handle 메서드 호출

```java
ProcessingObject<String> p1 = new HeaderTextProcessing();
PorcessingObjecT<String> p2 = new SpellCheckerProcessing();
p1.setSuccessor(p2);
String result = p1.handle("Aren't labdas really sexy?!!");
System.out.println(result); // From Raoul, Mario And Alan: Aren't lambdas really sexy?!!
```

## 람다 표현식으로 리팩토링

작업 처리 객체를 Function<String, String>, 더 정확히 표현하자면 UnaryOperator<String> 형식의 인스턴스로 표현할 수 있다. 그리고
andThen 메서드로 함수 체인을 이용하여 만들 수 있다.

```java
UnaryOperator<String> headerProcessing = (String text) -> "From Raoul, Mario and Alan: " + text;
UnaryOperator<String> spellCheckerProcessing = (String text) -> text.replaceAll("abda", "lambda");
Function<String, String> pipeline = headerProcessing.andThen(spellCheckerPorcessing);
String result = pipeline.apply("Aren't labdas really sexy?!!");
```
