# 스테이트(State)

> 스테이트 패턴을 이용하면 객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있다. 마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있다.

## Strategy vs State

스트래티지 패턴은 바꿔 쓸 수 있는 알고리즘이 핵심 개념이고, 스테이트 패턴은 내부 상태를 바꿈으로써 객체에서 행동을 바꾸는 것을 도와주는 역할이 핵심이다.

## Example 1

동그라미 모양으로 된 상태 다이어그램에서 동그라미 하나하나가 상태를 나타낸다. 뽑기 기계를 예를 들면, 동전 없음, 동전 있음, 알맹이 매진, 알맹이 판매가 상태가 된다.

- 뽑기 기계와 관련된 모든 행동에 대한 메서드가 들어있는 State 인터페이스 정의
- 기계의 모든 상태에 대해서 상태 클래스를 구현

- State Interface

```java
public interface State {
  void insertQuarter();
  void ejectQuarter();
  void turnCrank();
  void dispense();
}
```

- 각 상태와 관련된 클래스들

```java
public class NoQuarterState implements State {

  GumballMachine gumballMachine;
  
  public NoQuarterState(GumballMachine gumballMachine) {
    this.gumballMachine = gumballMachine;
  }
  
  public void insertQuarter() {
    System.out.println("동전을 넣으셨습니다.");
    // 누군가 동전을 집어넣으면 메시지를 출력하고 기계의 상태를 변경한다.
    gumballMachine.setState(gumballMachine.getHasQuarterState());
  }
  
  public void ejectQuarter() {
    System.out.println("동전을 넣어주세요.");
  }
  
  public void turnCrank() {
    System.out.println("동전을 넣어주세요.");
  }
  
  public void dispense() {
    System.out.println("동전을 넣어주세요.");
  }

}
```

```java
public class HasQuarterState implements State {

  GumballMachine gumballMachine;
  
  public HasQuarterState(GumballMachine gumballMachine) {
    this.gumballMachine = gumballMachine;
  }
  
  public void insertQuarter() {
    System.out.println("동전은 한 개만 넣어주세요.");
  }
  
  public void ejectQuarter() {
    System.out.println("동전이 반환됩니다.");
    gumballMachine.setState(gumballMachine.getNoQuarterState());
  }
  
  public void turnCrank() {
    System.out.println("손잡이를 돌리셨습니다.");
    gumballMachine.setState(gumballMachine.getSoldState());
  }
  
  public void dispense() {
    System.out.println("알맹이가 나갈 수 없습니다.");
  }

}
```

- GumballMachine

```java
public class GumballMachine {

  State soldOutState;
  State noQuarterState;
  State hasQuarterState;
  State soldState;
  
  State state = soldOutState;
  int count = 0;
  
  public GumballMachine(int numberGumballs) {
    soldOutState = new SoldOutState(this);
    noQuarterState = new NoQuarterState(this);
    hasQuarterState = new HasQuarterState(this);
    soldState = new SoldState(this);
    this.count = numberGumballs;
    if(numberGumballs > 0) {
      state = noQuarterState;
    }
  }
  
  public void insertQuarter() {
    state.insertQuarter();
  }
  
  public void ejectQuarter() {
    state.ejectQuarter();
  }
  
  /**
   * 현재 상태에 따라 행동이 결정된다. 
   * 즉, 내부 상태가 바뀜에 따라 행동이 달라진다.
   */ 
  public void turnCrank() {
    state.turnCrank(); // 각 행동을 구현한 클래스에서 turnCrank() 를 호출하면 turnCrank() 메서드에서 상태 변경이 있을 경우, 상태가 변경된다.
    state.dispense();
  }
  
  void setState(State state) {
    this.state = state;
  }
  
  void releaseBall() {
    System.out.println("A gumball comes rolling out the slot ... ");
    if(count != 0) {
      count = count -1;
    }
  }
  
  // 기타 메서드 생략

}
```


