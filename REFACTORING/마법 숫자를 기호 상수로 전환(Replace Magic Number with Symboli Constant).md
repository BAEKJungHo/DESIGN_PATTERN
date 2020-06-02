# 마법 숫자를 기호 상수로 전환(Replace Magic Number with Symboli Constant)

> 특수 의미를 지닌 리터럴 숫자가 있을 땐 의미를 살린 이름의 상수를 작성한 후 리터럴 숫자를 그 상수로 교체하자.

## 동기

마법 숫자가 여러군데 사용되며, 변경될 가능성이 있다면 기호 상수로 전환해야 한다. 마법 숫자가 분류 부호라면 분류 부호 클래스로 전환 기법 적용을 고려하고,
마법 숫자가 배열의 길이라면 array.length 를 사용해서 배열 원소 전체에 대해 루프를 실행해야 한다.
  
## Before Refactoring

```java
double potentialEnergy(double mass, double height) {
  return mass * 9.81 * height;
}
```

## After Refactoring

```java
static final double GRAVITATIONAL_CONSTANT = 9.81;

double potentialEnergy(double mass, double height) {
  return mass * GRAVITATIONAL_CONSTANT * height; 
}
```

## How

- 상수를 선언하고 그 상수에 마법 숫자의 값을 할당하자.
- 마법 숫자가 사용되는 부분을 모두 찾아내자.
- 마법 숫자가 상수의 용도와 맞는지 살펴보고, 그렇다면 마법 숫자를 상수로 교체하자.
- 컴파일하자.
- 모든 마법 숫자를 상수로 고쳤으면 테스트를 실시하자. 이때 모든 기능이 수정 전과 동일하게 작동해야 한다.
