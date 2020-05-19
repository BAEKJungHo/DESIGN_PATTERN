# 퍼사드(Facade)

> 어떤 서브시스템의 일련의 인터페이스에 대한 통합된 인터페이스를 제공한다. 퍼사드에서 고수준 인터페이스를 정의하기 때문에 서브시스템을 더 쉽게 사용할 수 있다.

## 어댑터 vs 퍼사드

- 어댑터
  - 인터페이스를 변경해서 클라이언트에서 필요로 하는 인터페이스로 적응시키기 위함
- 퍼사드
  - 서브시스템에 대한 간단한 인터페이스를 제공하기 위한 용도

## Example 1

예를 들어 영화를 보기 위해서 아래의 작업을 거친다고 생각해보자.

1. 팝콘 기계를 켠다
2. 팝콘을 튀긴다
3. 전등을 어둡게 조절
4. 스크린을 내린다
5. 프로젝터를 켠다
6. ...

영화가 끝나면 이와 같은 과정을 역순으로 해야한다.

이처럼 인터페이스 사용방법이 복잡한 경우 훨씬 쓰기 쉬운 통합된 인터페이스를 제공하는 `퍼사드 패턴`을 사용하면된다. 퍼사드를 사용하더라도 서브시스템의 고급기능을 사용하고 싶은 경우에는 언제든 사용할 수 있다. 퍼사드 클래스는 서브시스템 클래스들을 캡슐화 하는 것이 아니라, 서브시스템의 기능을 사용할 수 있는 `간단한 인터페이스를 제공`하는 것이다.

- 퍼사드 

```java
public class HomeTheaterFacade {
  // 구성(Composition) 사용
  Amplifier amp;
  Tuner tuner;
  DvdPlayer dvd;
  CdPlayer cd;
  Projector projector;
  TheaterLight light;
  Screen screen;
  PopcornPopper popper;
  
  public HomeTheaterFacade(
    Amplifier amp,
    Tuner tuner,
    DvdPlayer dvd,
    Projector projector,
    TheaterLight light,
    Screen screen,
    PopcornPopper popper
  ) {
    this.amp = amp;
    this.tunner = tunner;
    this.dvd = dvd;
    this.projector = projector;
    this.light = light;
    this.screen = screen;
    this.popper = popper;
  }
  
  // 서브시스템의 구성요소들을 모두 합친 메서드
  public void watchMovie(String movie) {
    System.out.println("Get ready to watch a movie ...");
    popper.on();
    popper.pop();
    lights.dim(10);
    screen.down();
    projector.on();
    projector.wideScreenMode();
    amp.on();
    amp.setDvd(dvd);
    amp.setVolume(5);
    dvd.on();
    dvd.play(movie);
  }
  
  public void endMovie() {
    System.out.println("Shutting movie theater down ...");
    popper.off();
    lights.on();
    screen.up();
    projector.off();
    amp.off();
    dvd.stop();
    dvd.eject();
    dvd.off();
  }
  
}
```

- 클라이언트

```java
public class HomeTeaterTestDrive {
  public static void main(String[] args) {
    // instance components here
    
    HomeTeaterFacade homeTeater = new HomeTeaterFacade(amp, tuner, dvd, cd, projecter, screen, lights, popper);
    homeTeater.watchMovie("Raiders of the Lost Ark");
    homeTeater.endMove();
  }

}
```
