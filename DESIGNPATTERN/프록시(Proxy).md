# 프록시(Proxy)

> 어떤 객체에 대한 접근을 제어하기 위한 용도로 대리인이나 대변인에 해당하는 객체를 제공하는 패턴

프록시는 자신이 대변하는 객체와 그 객체에 접근하고자 하는 클라이언트 사이에서 여러 가지 방식으로 작업을 처리합니다. 프록시는 자신이 대변하는 객체에 대해서
인터넷을 통해 들어오는 메서드 호출을 쫓아내는 것으로 알려져 있습니다. 또한 게으른 객체들을 대신해서 끈기 있게 기다리는 일을 맡고 있습니다.

- 원격 프록시 
  - 원격 객체에 대한 접근을 제어할 수 있다.
- 가상 프록시
  - 생성하기 힘든 자원에 대한 접근을 제어할 수 있다.
- 보호 프록시
  - 접근 권한이 필요한 자원에 대한 접근을 제어할 수 있다.

## 원격 프록시

원격 프록시는 원격 객체에 대한 로컬 대변자 역할을 한다. 원격 객체(remote object)란 다른 자바 가상 머신의 힙에서 살고 있는 객체(조금 더 일반적으로 말하자면 다른 주소 공간에서 돌아가고 있는 원격 객체)를 뜻한다. 로컬 대변자(local representative)는 어떤 메서드를 호출하면 다른 원격 객체한테 그 메서드 호출을 전달해주는 역할을 맡고 있는 객체를 말한다.

`GumballMachine(클라이언트 객체, 프록시와 메시지를 주고받는다.) - 프록시 - 원격 힙에 있는 GumballMachine (JVM 을 갖추고 있는 원격 객체)`

클라이언트 객체에서는 원격 객체의 메서드 호출을 하는 것처럼 행동한다. 하지만 실제로는 로컬 힙에 들어있는 프록시 객체의 메서드를 호출하고 있는 것이다. 네트워크 통신과 관련된 저수준 작업은 이 프록시 객체에서 처리해 준다.

- 원격 메서드의 기초
  - 클라이언트힙
    - 클라이언트 객체 > 클라이언트 보조 객체
  - 서버 힙
    - 서비스 보조 객체 > 서비스 객체
- 클라이언트 객체는 클라이언트 보조 객체랑 통신하며 클라이언트 보조 객체는 서비스 보조 객체랑 통신한다. 서비스 보조 객체는 서비스 객체랑 통신한다.

### 자바 RMI

RMI 는 우리 대신에 클라이언트와 서비스 보조 객체를 만들어 준다. 보조 객체에는 원격 서비스와 똑같은 메서드가 들어있다. 즉, RMI 를 이용하면 우리가 직접 네트워킹 및 입출력 관련 코드를 직접 작성하지 않아도 된다. 또한 클라이언트에서 원격 객체를 찾아서 그 원격 객체에 접근하기 위해 쓸 수 있는 룩업(lookUp) 서비스도 제공한다.

RMI 에서 클라이언트 보조 객체는 `스터브(stub)`, 서비스 보조 객체는 `스켈레톤(skeleton)` 이라고 부른다.

### Example

- 원격 서비스 만드는 순서
  - 원격 인터페이스 만들기
    - MyService.java (여기에는 클라이언트에서 호출할 원격 메서드가 정의된다.)
  - 서비스 구현 클래스 만들기
    - MyServiceImpl.java (실제 서비스 클래스)
  - rmic 를 이용하여 스터브와 스켈레톤 만들기
    - JDK 의 RMIC 툴을 이용하면 알아서 생성해 준다.
  - RMI 레지스트리 실행시키기
    - %rmiregistry
  - 원격 서비스 시작 
    - $java MyServiceImpl
    
 #### 1. 원격 인터페이스 만들기
 
 - java.rmi.Remote 확장
 
 Remote 는 표식용(marker) 인터페이스이다. 아무 메서드도 없지만 특별한 의미를 가지기 때문에 반드시 이 규칙을 따라야 한다.
 
 ```java
 // extends Remote : 이 인터페이스에서 원격 호출을 지원한다는 것을 알려준다.
 public interface MyRemote extends Remote {}
 ```
 
 - 모든 메서드를 `RemoteException` 을 던지는 메서드로 선언한다.
 
 ```java
 import java.rmi.*;
 
 public interface MyRemote extends Remote {
    public String sayHello() throws RemoteException;
 }
 ```
 
 - 인자와 리턴 값은 반드시 원시 형식(primitive) 또는 Serializable 형식으로 선언해야 한다.
 
 그 이유는, 모두 네트워크를 통해서 전달되기 때문에 직렬화를 통해 포장된다. 리턴값도 마찬가지이다. 원시 형식이나 API 에서 많이 사용하는 배열, 컬렉션형식을 사용한다면 걱정을 안해도 되지만, 직접 만든 형식일 경우 클래스를 만들 때 Serializable 인터페이스를 꼭 구현해야 한다.
 
> 즉, 직렬화(Serializable) 를 한다는 것은 네트워크 전송을 한다는 것이다.
 
 ```java
 public String sayHello() throws RemoteException;
 ```
 
 #### 2. 서비스 구현 클래스 만들기
 
 - 원격 인터페이스를 구현
 
 ```java
 public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {
    public String sayHello() {
      return "Server ays, Hey";
    }
    // 생략
 }
 ```

- UnicastRemoteObject 를 확장한다.

원격 서비스 객체 역할을 하기 위해서는 원격 객체가 되기 위한 기능을 추가해야 하는데 UnicastRemoteObject 를 확장하면 된다.

```java
public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {}
```

- RemoteException 을 선언하는 인자가 없는 생성자 만들기

수퍼클래스인 UnicastRemoteObject 의 한가지 문제점은 생성자에서 RemoteException 을 던진다.

```java
public MyRemoteImpl() throws RemoteException {}
```

- 서비스를 RMI 레지스트리에 등록 

원격 서비스가 완성되고 나면 클라이언트에서 쓸 수 있게 해줘야 한다. 방법은 인스턴스를 만든 다음 RMI 레지스트리에 등록하기만 하면 된다.(이 클래스가 실행될 때 RMI 레지스트리가 돌아가고 있어야 한다.) 서비스를 구현한 객체를 등록하면 RMI 시스템에서는 스터브만 레지스트리에 등록한다. 왜냐하면 클라이언트는 스터브만 필요하다. 서비스를 등록할 때는 java.rmi.Naming 클래스에 있는 rebind() 라는 정적 메서드를 사용하면 된다.

```java
try {
  MyRemote service = new MyRemoteImpl();
  
  /**
   * 서비스를 등록할 때는 이름을 지정해야 한다.
   * 왜냐하면 그 이름을 써서 레지스트리를 검색한다.
   * rebind() 메서드로 서비스 객체를 결합시키면 RMI 에서는 서비스에 해당하는 스터브를 레지스트리에 집어 넣는다.
   */
  Naming.rebind("RemoteHello", service);
} catch(Exception ex) {}
```
 
 #### 3. 서비스 구현 클래스 만들기 

- 서비스를 구현한 클래스에 대해서 rmic 를 돌린다. 
  - `%rmic MyRemoteImpl`
  - .class 추가할 필요 없다.

#### 4. rmiregistry 실행
  
- 터미널을 새로 띄워서 rmiregistry 를 실행 시킨다.
  - 클래스에 접근할 수 있는 디렉터리인 classes 에서 실행시킨다.
  - `%rmiregistry`
 
#### 5. 서비스 가동

- 다른 터미널을 열고 서비스를 시작한다.
  - `%java MyRemoteImpl`

#### 서버 코드

- 원격 인터페이스

```java 
import java.rmi.*;

public interface MyRemote extends Remote {
  public String sayHello() throws RemoteException;
}
```

- 원격 서비스를 구현한 클래스

```java
import java.rmi.*;
import java.rmi.server.*;

public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {
  
  public String sayHello() {
    return "Server says, Hey";
  }
  
  public MyRemoteImpl() throws RemoteException {}
  
  public static void main(String[] args) {
    try {
      MyRemote service = new MyRemoteImpl();
      Naming.rebind("RemoteHello", service);
    } catch(Exception ex) {
      ex.printStackTrace();
    }
  }

}
```

#### 클라이언트 코드

```java
import java.rmi.*;

public class MyRemoteClient {

  public static void main(String[] args) {
    new MyRemoteClient().go();
  }
  
  public void go() {
    try {
      /**
       * 서비스가 돌아가고 있는 시스템의 호스트 이름 또는 IP 주소를 입력하면 된다.
       * 리턴된 스터브는 인터페이스 형식으로 
       */
      MyRemote service = (MyRemote) Naming.lookup("rmi://127.0.0.1/RemoteHello");
      String s = service.sayHello();
      System.out.println(s);
    } catch(Excpetion e) {
      e.printStackTrace();
    }
  }
  
}
```

#### GumballMachine 을 원격 서비스로 개조하는 방법

원격 프록시를 쓸 수 있도록 코드를 개조할 때 가장 먼저 할 일은 GumballMachine 을 클라이언트로부터 전달된 원격 요청에 대한 서비스를 제공할 수 있는 방식으로 개조하는 것입니다. 즉, 서비스를 구현한 클래스로 만들어야 한다.

- GumballMachine 용 원격 인터페이스 만들기. 이 인터페이스에서는 원격 클라이언트에서 호출할 수 있는 메서드들을 정의해야 한다.
- 인터페이스의 모든 리턴 형식이 직렬화할 수 있는 형식인지 확인
- 구상 클래스에서 인터페이스 구현

```java
import java.rmi.*;

public interface GmballMachineRemote extends Remote {
  public int getCount() throws RemoteException;
  public String getLocation() throws RemoteException;
  public State getState() throws RemoteException;
}
```

지원해야 하는 메서드는 모두 throws RemoteException 을 던질 수 있다. 그리고 State 와 같은 형식은 직렬화 가능한 형식으로 변경해야 한다.

```java
import java.io.*;
public interface State extends Serializable {
  public void insertQuarter();
  public void ejectQuarter();
  public void turnCrank();
  public void dispense();
}
```

아직 Serializable 과 관련된 문제가 완전히 해결되진 않았다. 모든 State 객체에는 뽑기 기계의 메서드를 호출하거나 상태를 변경할 때 사용하기 위한 뽑기 기계에 대한 래퍼런스가 들어있다. 하지만 State 객체가 직렬화되어 전송될 때 뽑기 기계도 전부 직렬화 시켜서 같이 보내는 것은 바람직하지 않다. 이 문제는 아래와 같이 해결할 수 있다.

```java
public class NoQuarterState implements State {

  /**
   * 직렬화하고 싶지 않은 필드의 경우 transient 키워드로 해결할 수 있다.
   * 하지만 객체를 직렬화 해서 전송받은 후에 이 필드를 호출하게되면 안 좋은 상황이 생길 수 있다.
   */
  transient GumballMachine gumballMachine;
}
```

GumballMachine 을 서비스 역할을 할 수 있도록, 그리고 네트워크를 통해 들어온 요청을 처리할 수 있도록 고쳐야 한다.

```java
import java.rmi.*;
import java.rmi.server.*;

public class GumballMachine extends UnicastRemoteObject implements GumballMachineRemote {
  
  public GumballMachine(Strin location, int numberGumballs) throws RemoteExcpetion {
  
  }
  
  // 생략
}
```

- RMI 레지스트리 등록

```java
public class GumballMachineTestDrive {
  public static void main(String[] args) {
    GumballMachineRemote remote = null;
    int count;
    if(args.length < 2) {
      System.out.println("GumballMachine <name> <inventory>");
      System.exit(1);
    }
    
    try {
      count = Integer.parseInt(args[1]);
      gumballMachine = new GumballMachine(args[0], count);
      Naming.rebind("//" + args[0] + "/gumballMachine", gumballMachine);
  } catch(Exception e) {
    e.printStackTrace();
  }

}
```

- 테스트 클래스 실행
  - %rmiregistry
  -% java GumballMachineTestDrive seattle.mightygumball.com 100
  
- GumballMonitor 클라이언트 수정

```java
import java.rmi.*;

public class GumballMonitor {
  GumballMachineRemote machine;
  
  public GumballMonitor(GumballMachineRemote machine) {
    this.machine = machine;
  }
  
  public void report() {
    try {
      // 생략
    } catch(RemoteException e) {
      e.printStackTrace();
    }
  }

}
```

- 모니터링 클래스

```java
import java.rmi.*;

public class GumballMonitorTestDrive {
  public static void main(String[] args) {
    String[] location = {"rmi://santafe.mightygumball.com/gumballmachine",
              "rmi://boulder.mightygumball.com/gumballmachine",
              "rmi://seattle.mightygumball.com/gumballmachine"};
              
    GumballMonitor[] monitor = new GumballMonitor[location.lenght];
    
    for(int i=0; i<location.length; i++) {
      try {
        GumballMachineRemote machine = (GumballMachineRemote) Naming.lookup(location[i]);
        monitor[i] = new GumballMonitor(machine);
        System.out.println(monitor[i]);
      } catch(Exception e) {
        e.printStackTrace();
      }
    }
    
    for(int i=0; i<monitor.length; i++) {
      monitor[i].report();
    }
  }

}
```
