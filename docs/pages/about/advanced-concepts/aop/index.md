# Aspect Oriented Programming (AOP)

FF4j leverages on Spring AOP framework to provide a seamless integration for feature toggling using
annotations, making it easier to manage and maintain feature toggles in a clean and efficient
manner.

## Overview

From the beginning of this guide, we use imperative statements within source code to perform feature
toggle check like:

```java title="Snippet for checking the state of a feature"
if (ff4j.check("f1")) {
    System.out.println("The feature 'f1' is 'ON'");
} else {
    System.out.println("The feature 'f1' is 'OFF'");
}
```

This approach is quite intrusive into source code. You can nest different feature toggles at you
may consider to clean often your code and remove obsolete features.

A good alternative is to rely on Dependency Injection, also called Inversion of control (IoC) to
choose the correct implementation of the service at runtime.

Ff4j provide the **`@Flip`** annotation to perform flipping on methods using AOP proxies. At runtime, the
target service is proxied by the FF4j component which choose an implementation instead of another
using feature status (enable/disable). It leverages on Spring AOP Framework.

## Sample

!!!info 

    The complete sample is available in the [ff4j-samples/ff4j-aop-sample](https://github.com/ff4j/ff4j-samples/tree/master/ff4j-aop-sample) repository.

=== "1. Dependency"

    Add the dependency [`ff4j-aop`](https://github.com/ff4j/ff4j/tree/main/ff4j-aop)

    ```xml title="pom.xml"
    <dependency>
      <groupId>org.ff4j</groupId>
      <artifactId>ff4j-aop</artifactId>
      <version>${ff4j.version}</version>
    </dependency>
    ```

=== "2. Service interface"

    Create interface with the annotation `@Flip` on the method to be flipped
    
    ```java title="org.ff4j.sample.aop.service.GreetingService.java"
    package org.ff4j.sample.aop.service;
    
    import org.ff4j.aop.Flip;
    
    public interface GreetingService {
    
      @Flip(name = "language", alterBean = "greeting.french")
      String sayHello(String name);
    }
    ```

=== "3. Service implementation"

    We will define two implementations of the service, one for English and another for French. The
    `@Flip` annotation on the `sayHello` method indicates that the implementation will be switched based
    on the state of the "language" feature.
    
    ```java title="org.ff4j.sample.aop.service.EnglishGreetingService.java"
    package org.ff4j.sample.aop.service;
    
    import org.springframework.context.annotation.Primary;
    import org.springframework.stereotype.Component;
    
    @Primary
    @Component("greeting.english")
    public class EnglishGreetingService implements GreetingService {
    
      @Override
      public String sayHello(String name) {
        return "Hello, " + name + "!";
      }
    }
    ```

    !!!note "Note"

        The `@Primary` annotation on the `EnglishGreetingService` indicates that it will be the default implementation when the "language" feature is disabled.
    
    ```java title="org.ff4j.sample.aop.service.FrenchGreetingService.java"
    package org.ff4j.sample.aop.service;
    
    import org.springframework.stereotype.Component;
    
    @Component("greeting.french")
    public class FrenchGreetingService implements GreetingService {
    
      @Override
      public String sayHello(String name) {
        return "Bonjour, " + name + "!";
      }
    }
    ```

=== "4. FF4j Configuration"

    Create an FF4j configuration class to set up the FF4j bean and enable component scanning for the AOP
    package and your service package.
    
    ```java title="org.ff4j.sample.aop.config.FF4jConfig.java"
    package org.ff4j.sample.aop.config;
    
    import org.ff4j.FF4j;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.ComponentScan;
    import org.springframework.context.annotation.Configuration;
    
    @Configuration
    @ComponentScan({"org.ff4j.aop", "org.ff4j.sample.aop"})
    public class FF4jConfig {
    
      @Bean
      public FF4j ff4j() {
        return new FF4j("ff4j-language-sample.xml");
      }
    }
    ```
    
    Create the FF4j configuration file to define the "language" feature, which will control the switching between English and French greetings.
    
    ```xml title="resources/ff4j-language-sample.xml" hl_lines="6"
    <?xml version="1.0" encoding="UTF-8" ?>
    <ff4j xmlns="https://www.ff4j.org/schema/ff4j"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="https://www.ff4j.org/schema/ff4j https://ff4j.org/schema/ff4j-1.4.0.xsd">
      <features>
        <feature uid="language" enable="false"></feature>
      </features>
    </ff4j>
    ```

=== "5. Test"

    ```java title="org.ff4j.sample.aop.FF4jAopTest.java"
    package org.ff4j.sample.aop;
    
    import org.ff4j.FF4j;
    import org.ff4j.sample.aop.config.FF4jConfig;
    import org.ff4j.sample.aop.service.GreetingService;
    import org.junit.jupiter.api.Test;
    import org.junit.jupiter.api.extension.ExtendWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.test.context.ContextConfiguration;
    import org.springframework.test.context.junit.jupiter.SpringExtension;
    
    import static org.assertj.core.api.Assertions.assertThat;
    
    @ExtendWith(SpringExtension.class)
    @ContextConfiguration(classes = FF4jConfig.class)
    class FF4jAopTest {
    
      @Autowired
      private FF4j ff4j;
    
      @Autowired
      private GreetingService greetingService;
    
      @Test
      void defaultEnglishGreeting() {
        // Given
        assertThat(ff4j.getFeatureStore().exist("language")).isTrue();
        assertThat(ff4j.check("language")).isFalse();
        // When
        String greeting = greetingService.sayHello("John");
        // Then
        assertThat(greeting).isEqualTo("Hello, John!");
      }
    
      @Test
      void toggleFrenchGreeting() {
        // Given
        assertThat(ff4j.getFeatureStore().exist("language")).isTrue();
        assertThat(ff4j.check("language")).isFalse();
        // When
        ff4j.enable("language");
        // Then
        assertThat(ff4j.check("language")).isTrue();
        // When
        String greeting = greetingService.sayHello("John");
        // Then
        assertThat(greeting).isEqualTo("Bonjour, John!");
      }
    }
    ```