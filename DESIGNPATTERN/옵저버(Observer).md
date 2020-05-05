# 옵저버(Observer)

- 디자인 원칙
  - 서로 상호작용을 하는 객체 사이에서는 가능하면 느슨하게 결합하는 디자인을 사용해야 한다.

> 옵저버 패턴에서는 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식으로 `일대다(one-to-many)`
의존성을 정의한다. one 이 주제(subject) 이며, many 는 옵저버(observer) 이다.

어떤 이벤트가 발생했을 때 한 객체(__주제(subject)__ 라 불리는)가 다른 객체 리스트(__옵저버(observer)__ 라 불리는)에 자동으로 알림을 보내야 하는 상황에서 옵저버 디자인 패턴을 사용한다. GUI 애플리케이션에서 옵저버 패턴이 자주 등장한다. 버튼 GUI 컴포넌트에 옵저버를 설정할 수 있다. 
그리고 사용자가 버튼을 클릭하면 옵저버에 알림이 전달되고 정해진 동작이 수행된다.

## Example 1

옵저버 패턴으로 트위터 같은 커스터마이즈된 알림 시스템을 설계하고 구현할 수 있다. 다양한 신문 매체(뉴욕타임스, 가디언 등)가 뉴스 트윗을
구독하고 있으며 큭정 키워드를 포함하는 트윗이 등록되면 알림을 받고 싶어한다.

- 옵저버 인터페이스는 새로운 트윗이 있을 때 주제(Feed)가 호출할 수 있도록 notify라고 하는 하나의 메서드를 제공한다.

- Observer Interface

```java
interface Observer {
  void notify(String tweet);
}
```

- 옵저버 구현

```java
class NYTimes implements Observer {
  public void notify(String tweet) {
    if(tweet != null & tweet.contains("money")) {
      System.out.println("Breaking news in NY! " + tweet);
    }
  }
}

class Guardian implements Observer {
  public void notify(String tweet) {
    if(tweet != null & tweet.contains("queen")) {
      System.out.println("Yet more news from London! " + tweet);
    }
  }
}
```

- 주제 구현

```java
interface Subject {
  void registerObserver(Observer o);
  void notifyObservers(String tweet);
}
```

주제는 registerObserver 메서드로 새로운 옵저버를 등록한 다음에 notifyObservers 메서드로 트윗의 옵저버에 이를 알린다.

```java
calss Feed implements Subject {
  private final List<Observer> observers = new ArrayList<>();
  public void registerObserver(Observer o) {
    this.observers.add(o);
  }
  public void notifyObservers(String tweet) {
    observers.forEach(o -> o.notify(tweet));
  }
}
```

- 주제와 옵저버를 연결하는 데모 애플리케이션

```java
Feed f = new Feed();
f.registerObserver(new NYTimes());
f.registerObserver(new Guardian());
f.notifyObservers("The queen said her favourite book is Modern Java in Action!");
```

## 람다로 리팩토링하기 


```java
f.registerObserver(String(tweet) -> {
  if(tweet != null && tweet.contains("money")) {
    System.out.println("Breaking news in NY! " + tweet);
  }
});
f.registerObserver(String(tweet) -> {
  if(tweet != null && tweet.contains("money")) {
    System.out.println("Yet more news from London! " + tweet);
  }
});
```
