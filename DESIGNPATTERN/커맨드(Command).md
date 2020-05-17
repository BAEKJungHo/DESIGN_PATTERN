# 커맨드(Command)

> 커맨드 패턴을 이용하면 요구 사항을 객체로 캡슐화 할 수 있으며, 매개벼수를 써서 여러 가지 다른 요구 사항을 집어 넣을수 도 있다. 또한 요청 내역을 큐에
저장하거나 로그로 기록할 수도 있으며, 작업취소 기능도 지원 가능하다.

커맨드 패턴의 핵심은 `메서드 호출을 캡슐화` 하는 것이다. 메서드 호출을 캡슐화하면 계산 과정의 각 부분들을 결정화시킬 수 있기 때문에, 계산하는 코드를 호출한
객체에서는 어떤 식으로 일을 처리해야 하는지에 대해 전혀 신경 쓰지 않아도 된다. 그 외에도 캡슐화된 메서드 호출을 로그 기록용으로 저장을 한다거나 취소(undo) 기능을
구현하기 위해 재사용하는 것과 같이 신기한 작업을 할 수도 있다.

## UML 

- 구성
  - 클라이언트(Client) : ConcreteCommand 를 생성하고 Receiver 를 설정한다.
  - 인보커(Invoker) : 인보커에는 명령이 들어 있으며, execute() 메서드를 호출함으로써 커맨드 객체에 특정 작업을 수행해 달라는 요구를 하게 된다.
  - 커맨드 인터페이스(Command Interface) : Command 는 모든 커맨드 객체에서 구현해야 하는 인터페이스이다. 모든 명령은 execute() 메서드 호출을 통해 
  수행되고, 이 메서드에서는 리시버에 특정 작업을 처리하라는 지시를 전달한다.
    - execute()
    - undo()
  - 커맨드 구현체(Concrete Command) : 특정 행동과 리시버 사이를 연결해 준다. 인보커에서 execute() 호출을 통해 요청을 하면 ConcreteCommand 객체에서
  리시버에 있는 메서드를 호출하여 작업을 처리한다.
  - 리시버(Receiver) : 리시버는 요구 사항을 수행하기 위해 어떤 일을 처리해야 하는지 알고 있는 객체이다.
  
## Example 1

- Command 인터페이스 만들기

```java
public interface Command {
  public void execute();
}
```

- 전등을 켜기 위한 커맨드 클래스 구현

```java
public class LightOnCommand implements Command {
  Light light;
  
  public LightOnCommand(Light light) {
    this.light = light;
  }
  
  public void execute() {
    light.on();
  }
}
```
