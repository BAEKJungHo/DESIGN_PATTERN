#  직관적 임시변수 사용(Introduce Explaining Variable)

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
   
