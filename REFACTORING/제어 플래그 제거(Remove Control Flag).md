# 제어 플래그 제거(Remove Control Flag)

> 논리 연산식의 제어 플래그 역할을 하는 변수가 있을 땐 그 변수를 break 문이나 return 문으로 바꾸자.

## 동기

여러 조건문이 사용된 코드에는 다음과 같이 조건문을 빠져나갈 시점을 결정하는 제어 플래그가 흔히 사용된다.

```
done 변수에 false 값 할당
while done 이 false 인 동안
  if(조건식)
    뭔가 기능을 수행
    done 변수에 true 값 할당
  루프의 다음 단계로 넘어감
```

제어 플래그는 유용함을 능가하는 단점이 있다. 진입점과 이탈점이 하나씩 있는 루틴을 호출하는 구조적 프로그래밍 문법의 잔재이다. 
진입점이 하나인 것엔 문제가 없지만 이탈점을 하나만 사용하면 코드 안의 각종 특이한 플래그로 인해 조건문이 복잡해진다. 그래서 프로그래밍 언어엔
복잡한 조건문을 방지하는 break 와 continue 문이 있다. 제어 플래그를 없애면 조건문의 진정한 의도를 쉽게 파악할 수 있다.

## 제어 플래그를 break 문으로 교체

- Before Refactoring

```java
void checkSecurity(String[] people) {
  boolean found = false;
  for(int i=0; i<people.length; i++) {
    if(!found) {
      if(people[i].equals("Don")) {
        sendAlert();
        found = true;
      }
      if(people[i].equals("John")) {
        sndAlert();
        found = true;
      }
    }
  }
}
```

- After Refactoring

```java
void checkSecurity(String[] people) {
  for(int i=0; i<people.length; i++) {
    if(people[i].equals("Don")) {
      sendAlert();
      break;
    }
    if(people[i].equals("John")) {
      sndAlert();
      break;
    }
  }
}
```

## 제어 플래그를 return 문으로 교체

- Before Refactoring

```java
void checkSecurity(String[] people) {
  String found = "";
  for(int i=0; i<people.length; i++) {
    if("".equals(found)) {
      if(people[i].equals("Don")) {
        sendAlert();
        found = "Don";
      }
      if(people[i].equals("John")) {
        sndAlert();
        found = "John";
      }
    }
  }
}
```

- After Refactoring

```java
String checkSecurity(String[] people) {
  for(int i=0; i<people.length; i++) {
    if(people[i].equals("Don")) {
      sendAlert();
      return "Don";
    }
    if(people[i].equals("John")) {
      sndAlert();
      return "John";
    }
  }
  return "";
}
```

이 메서드의 기능엔 아직 부작용이 있다. 그래서 상태 변경 메서드와 값 반환 메서드를 분리 기법을 실시해야한다.

## How

- 논리문을 빠져나오게 하는 제어 플래그 값을 찾자.
- 그 제어 플래그 값을 대입하는 코드를 break 문이나 continue 문으로 바꾸자.
- 하나씩 바꿀 때마다 컴파일과 테스트를 실시하자.
