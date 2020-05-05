# 옵저버(Observer)

- 디자인 원칙
  - 서로 상호작용을 하는 객체 사이에서는 가능하면 느슨하게 결합하는 디자인을 사용해야 한다.

> 옵저버 패턴에서는 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식으로 `일대다(one-to-many)`
의존성을 정의한다. one 이 주제(subject) 이며, many 는 옵저버(observer) 이다.

어떤 이벤트가 발생했을 때 한 객체(__주제(subject)__ 라 불리는)가 다른 객체 리스트(__옵저버(observer)__ 라 불리는)에 자동으로 알림을 보내야 하는 상황에서 옵저버 디자인 패턴을 사용한다. GUI 애플리케이션에서 옵저버 패턴이 자주 등장한다. 버튼 GUI 컴포넌트에 옵저버를 설정할 수 있다. 
그리고 사용자가 버튼을 클릭하면 옵저버에 알림이 전달되고 정해진 동작이 수행된다.

## 느슨한 결합(Loose coupling)

두 객체가 느슨하게 결합되어 있다는 것은, 그 둘이 상호작용을 하긴 하지만 서로에 대해 잘 모른다는 것을 의미한다. 옵저버 패턴은 느슨한 결합을 제공한다. 이유는 아래와 같다.

- 주제가 옵저버에 대해서 아는 것은 옵저버가 트정 인터페이스(Observer 인터페이스)를 구현 한다는 것 뿐이다. 
  - 옵저버의 구상 클래스가 무엇인지, 옵저버가 무엇을 하는지 등에 대해서 알 필요가 없다.
- 옵저버는 언제든 새로 추가할 수 있다.
  - 실행 중 옵저버를 변경할 수도 있고, 제거할 수도 있다.
- 새로운 형식의 옵저버를 추가하려고 할 때도 주제를 전혀 변경할 필요가 없다.
  - 새로운 클래스에서 Observer 인터페이스를 구현하고 옵저버로 등록하면 된다.
- 주제나 옵저버가 바뀌더라도 서로한테 영향을 미치지는 않는다.

> 느슨한 결합하는 디자인을 사용하면 변경 사항이 생겨도 무난히 처리할 수 있는 유연한 객체지향 시스템을 구축할 수 있다. 객체 사이의 상호의존성을 최소화 할 수 있다.

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
  void removeObserver(Observer o);
}
```

주제는 registerObserver 메서드로 새로운 옵저버를 등록한 다음에 notifyObservers 메서드로 트윗의 옵저버에 이를 알린다.

```java
calss Feed implements Subject {
  private final List<Observer> observers = new ArrayList<>();
  // 옵저버 등록
  public void registerObserver(Observer o) {
    this.observers.add(o);
  }
  // 알림
  public void notifyObservers(String tweet) {
    observers.forEach(o -> o.notify(tweet));
  }
  // 옵저버 제거
  public void removeObserver(Observer o) {
    int i = this.observers.indexOf(o);
    if(i >= 0) {
      observers.remove(i);
    }
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

### 람다로 리팩토링하기 


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

## Example 2

> 기상 스테이션

- 주제 인터페이스

```java
public inteface Subject {
  // 옵저버 등록
  public void registerObserver(Observer o);
  // 옵저버 제거
  public void removeObserver(Observer o);
  // 주제 객체의 상태가 변경되었을 때 모든 옵저버들한테 알리기 위해 호출되는 메서드
  pulbic void notifyObservers();
}
```

- 옵저버 인터페이스

```java
public interface Observer {
  // 기상정보가 변경되었을 때 옵저버한테 전달되는 상태값들
  public void update(float temp, float humidity, float pressure);
}
```

- 디스플레이 항목에서 구현해야 하는 인터페이스

```java
public interface DisplayElement {
  public void display();
}
```

- 주제 인터페이스 구현체

```java
public class WeatherData implements Subject {
  private ArrayList observers;
  private float temperature;
  private float humidity;
  private float pressure;
  
  /**
   * 생성자에서 Observer 객체를 저장하기 위한 observers 초기화
   */
  public WeatherData() {
    observers = new ArrayList();
  }
  
  /**
   * 옵저버가 등록을 하면 목록 맨 뒤에 추가
   */
  public void registerObserver(Observer o) {
    observers.add(o);
  }
  
  
  /**
   * 옵저버가 탈퇴를 신청하면 목록에서 제거
   */
  pulbic void removeObserver(Observer o) {
    int i = observers.indexOf(o);
    if (i >= 0) {
      observers.remove(i);
    }
  }
  
  
  /**
   * 상태가 변경되었을때, 모든 옵저버들한테 알리는 역할
   * ex) 유튜브 구독을 생각했을때, 보겸 이라는 유튜버가 영상을 올리면 해당 구독자들 전부한테 알리는 역할 등을 구현할 수 있다.
   */
  pulbic void notifyObservers() {
    for(int i=0; i<observers.size(); i++) {
      Observer observer = (Observer) observers.get(i);
      observer.update(temperature, humidity, pressure);
    }
  }
  
  
  /**
   * 기상 스테이션으로부터 갱신된 측정치를 받으면 옵저버한테 알린다.
   */
  public void measurementsChanged() {
    notifyObservers();
  }
  
  public void serMeasurements(float temperature, float humidity, float pressure) {
    this.temperature = temperature;
    this.humidity = humidity;
    this.pressure = pressure;
    measurementsChanged();
  }
  
  // 기타 메서드
}
```

- 디스플레이 항목

```java
public class CurrentConditionsDisplay implements Observer, DisplayElement {
  private float temperature;
  private float humidity;
  private Subject weatherData;
  
  
  /**
   * 생성자에 weatherData 라는 주제 객체가 전달되며, 그 객체를 써서 디스프레이를 옵저버로 등록한다.
   */
  public CurrentConditionsDisplay(Subject weatherData) {
    this.weatherData = weatherData;
    weatherData.registerObservers(this);
  }
  
  
  /**
   * update() 가 호출되면 기온과 습도를 저장하고 display() 호출
   */
  public void update(float temperature, float humidity, float pressure) {
    this.temperature = temperature;
    this.humidity = humidity;
    diplay();
  }
  
  
  /**
   * 가장 최근에 얻은 기온과 습도 출력
   */
  public void display() {
    System.out.println("Current conditions : " + temperature + "F degrees and " + humidity + "% humidity");
  }
}
```

- 테스트용 클래스

```java
public class WeatherStation {
  public static void main(String[] args) {
    WeatherData weatherData = new WeatherData();
    
    CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay(weatherData);
    StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);
    ForecastDisplay forecastDisplay = new ForecastDisplay(weatherData);
    
    weatherData.setMeasurements(80, 65, 30.4f);
    weatherData.setMeasurements(82, 70, 29.2f);
    weatherData.setMeasurements(78, 90, 29.4f);
  }
}
```
