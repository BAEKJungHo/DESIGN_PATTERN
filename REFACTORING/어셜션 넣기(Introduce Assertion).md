# 어셜션 넣기(Introduce Assertion)

> 일부 코드가 프로그램의 어떤 상태를 전제할 땐, 어설션을 넣어서 그 전제를 확실하게 코드로 작성하자. (즉, 코드가 동작하기위한 전제를 나타내는 것. 따라서 코드의 원리와 전제를 알기 쉬워진다.)

## 동기

특정 조건이 참일 때만 코드의 일부분이 실행되는 경우가 많다. 그런 전제는 대개 코드로 작성되어 있지 않고, 알고리즘을 살피거나 주석으로 처리된 경우도 있다. 이런 전제는 어설션을 넣어 명확히 드러나게 하는 것이 좋다.

어설션이란 항상 참으로 전제되는 조건문을 뜻한다. 따라서 어설션이 실패할 경우 반드시 예외를 통지하게 해야 한다.

어설션은 결과가 참이 아니라면 RuntimeException 을 통지한다.

어설션은 대게 제품화 단계에서 삭제한다. 따라서 코드에서 어설션을 넣은 부분을 꼭 표시해둬야 한다.

> // TODO Using Assertion 이런식으로 어설션 사용한 곳에 주석 표시

어설션은 시스템의 가동에 영향을 미치지 않으므로(성능에 영향을 주지 않는다.) 어설션을 넣어도 기능은 변하지 않는다.

어설션 남용에 주의해야하며, 코드 일부에서 참이라고 생각되는 모든 곳에 넣으면 안되며, 반드시 참이 되어야 하는 곳에만 넣어야 한다.

- 장점
  - 어설션을 통해 의사소통과 디버깅에 도움을 줄 수 있다.
  - 개발자는 코드가 전제하는 내용을 쉽게 이해할 수 있다.
  - 디버깅할 때 좀 더 근원적인 버그를 잡아낼 수 있다.
  
## 주의 사항

어설션을 사용하면서 주의해야할 점이 있다. 어설션이 최종 실행환경에서는 빠져버리고 실행되지 않기 때문에 업무 로직에 해당하는 체크사항을 어설션을 사용해서 체크하게 되면 실행시에는 체크가 되지 않아서 문제가 발생할 수 있다. 개발환경과 실행환경의 프로그램이 달라지는것이다.

즉, 어설션은 해당 코드의 전제조건을 나타내는 의사소통과 디버깅을 편리하게 해주지만, 절대 예외처리 용도로 사용되면 안된다.

## Before Refactoring

```java
double getExpenseLimit() {
  // 비용 한도를 두든지, 주요 프로젝트를 정해야 한다.
  return (_expenseLimit != NULL_EXPENSE) ?
    _expenseLimit:
    _primaryProject.getMemberExpenseLimit();
}
```

## After Refactoring

```java
double getExpenseLimit() {
  Assert.isTrue(_expenseLimit != NULL_EXPENSE || _primaryPorject != null);
  return (_expenseLimit != NULL_EXPENSE) ?
    _expenseLimit:
    _primaryProject.getMemberExpenseLimit();
}
```

## 속성값 검증(필수값, 기본키 등)

```java
public class PropertyValidator {

    /**
     * 필수값 Validate - Integer version
     * @param requiredValue
     * @param message
     */
    public static void requiredValueValidate(Integer requiredValue, String message) {
        if(requiredValue == null) {
            throw new ShowUserMessageException(message);
        }
    }

    /**
     * 필수값 Validate - String version
     * @param requiredValue
     * @param message
     */
    public static void requiredValueValidate(String requiredValue, String message) {
        if(StringUtils.isBlank(requiredValue)) {
            throw new ShowUserMessageException(message);
        }
    }

}
```

```java
PropertyValidator.requiredValueValidate("seq", "기본키는 빈 값이 올 수 없습니다.");
```

## How

- 어떤 조건이 참으로 전제된다면 어설션을 넣어 그 전제를 드러내자
  - 어설션 기능을 사용할 수 있는 Assert 클래스를 작성하자.
- 어설션이 실패하더라도 코드가 작동하는지 항상 자문해보고 어설션 실패에도 코드가 돌아간다면 그 어설션은 삭제하자.
- 어설션 안에 중복 코드가 안생기게 주의하자.
