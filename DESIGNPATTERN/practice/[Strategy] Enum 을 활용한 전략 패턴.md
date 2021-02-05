# 전략을 구현한 Enum

```java
public enum EdgeWeightType implements EdgeWeightStrategy {
    DISTANCE(edge -> edge.getDistance()),
    DURATION(edge -> edge.getDuration());
    private final Function<RouteEdge, Integer> edgeWeightStrategy;
    EdgeWeightType(Function<RouteEdge, Integer> edgeWeightStrategy) {
        this.edgeWeightStrategy = edgeWeightStrategy;
    }
    public int getWeight(RouteEdge edge) {
        return edgeWeightStrategy.apply(edge);
    }
}
```

위처럼 구현할 경우의 장점은 다음과 같다.

- 사용자가 interface 를 사용할 수 있도록 유지하면서 enum 이 전략의 구현체가 된다면, 사용자 입장에서 EdgeWeightType 내부 구현은 신경쓰지 않아도 된다. 또한 기존 EdgeWeightType 은 enum 으로서 구현한 전략 객체의 유일성을 보장할 수 있게 된다.
- EdgeWeightType 과 함께 EdgeWeightStrategy 를 구현한 새로운 class, enum 을 이용할 수 있어 코드의 유연성을 확보할 수 있다. 이는 테스트 시에도 EdgeWeightType 과는 무관하게 테스트를 위한 구현체를 만들어 원할히 테스트를 할 수 있다는 점과도 연결이 된다.

# Enum 안에 내부 인터페이스 형식으로 전략을 두어서 응집도 높은 코드 만들기

```java
public enum EdgeWeightType {
    DISTANCE(RouteEdge::getDistance),
    DURATION(RouteEdge::getDuration);

    private final EdgeWeightStrategy edgeWeightStrategy;

    EdgeWeightType(EdgeWeightStrategy edgeWeightStrategy) {
        this.edgeWeightStrategy = edgeWeightStrategy;
    }

    // EdgeWeightStrategy 의 메서드를 위임
    public int getWeight(RouteEdge edge) { 
        return this.edgeWeightStrategy.getWeight(edge);
    }

    // 전략을 함수형 인터페이스로 선언
    @FunctionalInterface
    private interface EdgeWeightStrategy {
        int getWeight(RouteEdge edge);
    }
}
```

위의 코드를 살펴보면 이전에 분리되어 있던 EdgeWeightStrategy가 EdgeWeightType의 내부에서 선언되어 있다.

이로서 EdgeWeightType 에서만 사용되던 인터페이스를 한 곳으로 응집시켜 객체를 사용하는 입장에서 응집도 있는 객체를 활용할 수 있게 되었다.

그리고 EdgeWeightStrategy 에서 선언한 메서드를 EdgeWeightStrategy 를 사용하는 EdgeWeightType 객체에 위임하여서 외부에서도 사용할 수 있도록 구현해두었다.

이처럼 한 곳에서만 사용하는 인터페이스를 내부 인터페이스 형식으로 선언하고 인터페이스의 메서드를 위임해서 외부에서 사용할 수 있도록 한다면 해당 객체를 사용하는 사용자에게 코드의 분산으로 인한 혼란을 감소시키고 응집도 있는 객체를 사용할 수 있게 해준다.

## 참고

> [Javable. 사용성을 고려해 객체를 설계하자](https://woowacourse.github.io/javable/post/2020-08-18-plan-reusable-object/)
