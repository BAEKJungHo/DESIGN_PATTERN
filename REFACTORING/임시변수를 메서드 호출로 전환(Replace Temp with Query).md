# 임시변수를 메서드 호출로 전환(Replace Temp with Query)

> 수식의 결과를 저장하는 임시변수가 있을 땐, 그 수식을 빼내어 메서드로 만든 후, 임시변수 참조 부분을 전부 수식으로 교체하자. 새로 만든 메서드는 다른 메서드에서도 호출 가능하다.

## Before Refactoring

```java
double basePrice = _quantity * _itemPrice;
if(basePrice > 1000)
  return basePrice * 0.95;
else 
  return basePrice * 0.98;
```

## After Refactoring

```java
if(basePrice() > 1000)
  return basePrice() * 0.95;
else 
  return basePrice() * 0.98;
  
double basePrice() {
  return _quantity * _itemPrice;
}
```

## How

- 임시변수를 메서드 호출로 전환은 메서드 추출전에 적용해야 한다.
- 값이 한 번만 대입되는 임시변수를 찾자.
  - 값이 여러번 대입되는 임시변수가 있으면 `임시변수 분리` 기법 실시를 고려하자.
- 그 임시변수를 final 로 선언하자.
- 컴파일을 실시하자.
- 대입문 우변을 빼내어 메서드로 만들자.
  - 처음엔 메서드를 private 으로 선언하자. 그러다 나중에 더 여러 곳에서 사용하게 되면 접근 제한을 완화하면된다.
  - 추출 메서드에 문제는 없는지 확인하자. 문제가 있다면 `상태 변경 메서드와 값 반환 메서드를 분리` 기법을 실시하자.
- 컴파일과 테스트를 실시하자.
- 임시변수를 대상으로 `임시변수 내용 직접 삽입` 기법을 실시하자.
