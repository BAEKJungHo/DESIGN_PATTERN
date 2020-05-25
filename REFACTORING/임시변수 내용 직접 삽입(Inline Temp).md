# 임시변수 내용 직접 삽입(Inline Temp)

> 간단한 수식을 대입받는 임시변수로 인해 다른 리팩토링 기법 적용이 힘들 땐 그 임시변수를 참조하는 부분을 전부 수식으로 치환하자.

## Before Refactoring

```java
double basePrice = anOrder.basePrice();
return (basePrice > 1000);
```

## After Refactoring

```java
return (anOrder.basePrice() > 1000);
```

## How

- 대입문의 우변에 문제가 없는지 확인하자.
- 문제가 없다면 임시변수를 final 로 선언하고 컴파일하자. 
  - 이것으로 그 임시변수에는 값을 한 번만 대입할 수 있다.
- 그 임시변수를 참조하는 모든 부분을 찾아서 대입문 우변의 수식으로 바꾸자
- 하나씩 수정을 마칠 때마다 컴파일과 테스트를 실시하자.
- 임시변수 선언과 대입문을 삭제하자.
- 컴파일과 테스트를 실시하자.
   
