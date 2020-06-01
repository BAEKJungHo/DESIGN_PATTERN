# 중복 조건식 통합(Consolidate Conditional Expression)

> 여러 조건 검사식의 결과가 같을 땐 하나의 조건문으로 합친 후 메서드로 빼내자.

## Before Refactoring

```java
double disabilityAmount() {
  if(_seniority < 2) return 0;
  if(_monthsDisabled > 12) return 0;
  if(_isPartTime) return 0;
}
```

## After Refactoring

```java
double disabilityAmount() {
  if(isNotEligableForDisability()) return 0;
}
```

## How

- 모든 조건문에 부작용이 없는지 검사하자. 
  - 하나라도 부작용이 있으면 이 리팩토링 기법을 실시할 수 없다.
- 여러 개의 조건문을 논리 연산자를 사용해 하나의 조건문으로 바꾸자.
- 컴파일과 테스트를 실시하자.
- 합친 조건문에 메서드 추출 기법 적용을 고려하자.
