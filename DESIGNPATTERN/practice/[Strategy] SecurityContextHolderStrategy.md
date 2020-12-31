# SecurityContextHolderStrategy

> SecurityContextHolder 도 내부적으로 전략 패턴(Strategy Pattern) 을 사용하고, ThreadLocal 을 사용한다.

```java
public interface SecurityContextHolderStrategy {
    void clearContext();

    SecurityContext getContext();

    void setContext(SecurityContext var1);

    SecurityContext createEmptyContext();
}
```

## SecurityContextHolder

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.security.core.context;

import java.lang.reflect.Constructor;
import org.springframework.util.ReflectionUtils;
import org.springframework.util.StringUtils;

public class SecurityContextHolder {
    public static final String MODE_THREADLOCAL = "MODE_THREADLOCAL";
    public static final String MODE_INHERITABLETHREADLOCAL = "MODE_INHERITABLETHREADLOCAL";
    public static final String MODE_GLOBAL = "MODE_GLOBAL";
    public static final String SYSTEM_PROPERTY = "spring.security.strategy";
    private static String strategyName = System.getProperty("spring.security.strategy");
    private static SecurityContextHolderStrategy strategy;
    private static int initializeCount = 0;

    public SecurityContextHolder() {
    }

    public static void clearContext() {
        strategy.clearContext();
    }

    public static SecurityContext getContext() {
        return strategy.getContext();
    }

    public static int getInitializeCount() {
        return initializeCount;
    }

    private static void initialize() {
        if (!StringUtils.hasText(strategyName)) {
            strategyName = "MODE_THREADLOCAL";
        }

        if (strategyName.equals("MODE_THREADLOCAL")) {
            strategy = new ThreadLocalSecurityContextHolderStrategy();
        } else if (strategyName.equals("MODE_INHERITABLETHREADLOCAL")) {
            strategy = new InheritableThreadLocalSecurityContextHolderStrategy();
        } else if (strategyName.equals("MODE_GLOBAL")) {
            strategy = new GlobalSecurityContextHolderStrategy();
        } else {
            try {
                Class<?> clazz = Class.forName(strategyName);
                Constructor<?> customStrategy = clazz.getConstructor();
                strategy = (SecurityContextHolderStrategy)customStrategy.newInstance();
            } catch (Exception var2) {
                ReflectionUtils.handleReflectionException(var2);
            }
        }

        ++initializeCount;
    }

    public static void setContext(SecurityContext context) {
        strategy.setContext(context);
    }

    public static void setStrategyName(String strategyName) {
        SecurityContextHolder.strategyName = strategyName;
        initialize();
    }

    public static SecurityContextHolderStrategy getContextHolderStrategy() {
        return strategy;
    }

    public static SecurityContext createEmptyContext() {
        return strategy.createEmptyContext();
    }

    public String toString() {
        return "SecurityContextHolder[strategy='" + strategyName + "'; initializeCount=" + initializeCount + "]";
    }

    static {
        initialize();
    }
}
```
