# 직관적 임시변수 사용(Introduce Explaining Variable)

> 사용된 수식이 복잡할 땐 수식의 결과나 수식의 일부분을 용도에 부합하는 직관적 이름의 임시변수에 대입하자.

## Before Refactoring

```java
if( (platform.toUpperCase().indexOf("MAC") > -1) &&
   (browser.toUpperCase().indexOf("IE") > -1) &&
   (wasInitialized() && resize > 0) ) 
{
  // ...   
}
```

## After Refactoring

```java
final boolean isMacOs = platform.toUpperCase().indexOf("MAC") > -1;
final boolean isIEBrowser = browser.toUpperCase().indexOf("IE") > -1;
final boolean wasResized = resize > 0;

if(isMacOs && isIEBrowser && wasInitialized() && wasResized) {
  // 기능 코드
}
```

## How

- 임시변수를 final 로 선언하고, 복잡한 수식에서 한 부분의 결과를 그 임시변수에 대입하자.
- 그 수식에서 한 부분의 결과를 그 임시변수의 값으로 교체하자. (수식의 결과 부분이 반복될 경우엔 한 번에 하나씩 교체)
- 컴파일과 테스트 실시하자.
- 수식의 다른 부분을 대상으로 위의 과정을 반복 실시하자.
   
