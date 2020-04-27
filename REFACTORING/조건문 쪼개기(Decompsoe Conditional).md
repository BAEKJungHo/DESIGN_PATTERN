# 조건문 쪼개기(Decompsoe Conditional)

> 복잡한 조건문(if-then-else)이 있을 땐, if, then, else 부분을 각각 메서드로 빼내자. 코드의 의도를 더욱 분명히 알 수 있다. 가독성이 좋아진다.

## Before Refactoring

```java
if(date.before(SUMMER_START) || date.after(SUMMER_END)) 
  charge = quantity * _winterRate + _winterServiceCharge;
else charge = quantity * _summerRate;
```

## After Refactoring

```java
if(notSummer(date))
  charge = winterCharge(quantity);
else charge = summerCharge(quantity);
```

## How

- if 절을 별도의 메서드로 빼내자.
- then 절과 else 절을 각각 메서드로 빼내자.
