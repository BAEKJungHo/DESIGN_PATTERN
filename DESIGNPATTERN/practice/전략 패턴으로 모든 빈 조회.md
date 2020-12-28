# 전략 패턴으로 모든 빈 조회

```java
public class AllBeanTest {
 @Test
 void findAllBean() {
     ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);
     DiscountService discountService = ac.getBean(DiscountService.class);
     Member member = new Member(1L, "userA", Grade.VIP);
     int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");
     assertThat(discountService).isInstanceOf(DiscountService.class);
     assertThat(discountPrice).isEqualTo(1000);
 }
 
 static class DiscountService {
     private final Map<String, DiscountPolicy> policyMap;
     private final List<DiscountPolicy> policies;
     
     public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
       this.policyMap = policyMap;
       this.policies = policies;
       System.out.println("policyMap = " + policyMap);
       System.out.println("policies = " + policies);
      }
     public int discount(Member member, int price, String discountCode) {
         DiscountPolicy discountPolicy = policyMap.get(discountCode);
         System.out.println("discountCode = " + discountCode);
         System.out.println("discountPolicy = " + discountPolicy);
         return discountPolicy.discount(member, price);
     }
  }
}
```

위 코드에서 discount 메서드를 보면 policyMap.get 코드를 봤을때 discountCode 라는걸 넘겨서 객체를 생성하는 구나 정도로만 알지 어떠한 빈들이 생성되는지는 한눈에 보기 어렵다.
이러한 경우에는 자동 빈 등록 보다는 수동 빈 등록을 이용해서 명확하게 나타내는 것이 좋다. 즉 전략 패턴과 같이 [비즈니스 로직 중에서 다형성을 적극 활용할 때](https://github.com/BAEKJungHo/spring-core-principle#%EB%B9%84%EC%A6%88%EB%8B%88%EC%8A%A4-%EB%A1%9C%EC%A7%81-%EC%A4%91%EC%97%90%EC%84%9C-%EB%8B%A4%ED%98%95%EC%84%B1%EC%9D%84-%EC%A0%81%EA%B7%B9-%ED%99%9C%EC%9A%A9%ED%95%A0-%EB%95%8C) 는 `수동 빈 등록`을 사용하는 것이 더 좋다.
