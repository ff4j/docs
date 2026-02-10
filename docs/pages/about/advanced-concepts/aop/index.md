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

1. Add the dependency to your project:

    ```xml title="pom.xml"
    <dependency>
      <groupId>org.ff4j</groupId>
      <artifactId>ff4j-aop</artifactId>
      <version>${ff4j.version}</version>
    </dependency>
    ```

2. Create interface with the annotation @Flip on the method to be filpped:

    ```java title="GreetingService.java"
    public interface GreetingService {
      @Flip(name = "language-french", alterBean = "greeting.english")
      String sayHello(String name);
    }
    ```

3. Create the implementation of the service:

    ```java title="EnglishGreetingService.java"
    @Component("greeting.english")
    public class EnglishGreetingService implements GreetingService {
    
      @Override
      public String sayHello(String name) {
        return "Hello " + name;
      }
    }
    ```

4. Create another implementation of the service:

    ```java title="FrenchGreetingService.java"
    @Component("greeting.french")
    public class FrenchGreetingService implements GreetingService { 
    
      @Override
      public String sayHello(String name) {
        return "Bonjour " + name;
      }
    }
    ```
   
5. Scan the package for components and enable AOP in your Spring configuration:

    ```java title="AppConfig.java"
    @Configuration
    @ComponentScan(basePackages = {"org.ff4j.aop","org.ff4j.sample"})
    @EnableAspectJAutoProxy
    public class FF4jConfig {
    
      @Bean
      public FF4j ff4j() {
        FF4j ff4j = new FF4j("ff4j-language-sample.xml");
        return ff4j;
      }
    }
    ```
   
6. Create ff4j configuration:

    ```xml title="ff4j-language-sample.xml" hl_lines="3"
    <?xml version="1.0" encoding="UTF-8" ?>
    <features>
      <feature uid="language-french" enable="false" />
    </features>
    ```
   
7. Test the service:

    ```java title="GreetingServiceTest.java"
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = FF4jConfig.class)
    public class GreetingServiceTest {
    
      @Autowired
      private GreetingService greetingService;
   
      @Autowired
      private FF4j ff4j;
   
      @Test void defaultGreeting() {   
        // Given
        FF4j ff4j = new FF4j("ff4j-language-sample.xml");
        // When
        String greeting = greetingService.sayHello("John");
        // Then
        assertEquals("Hello John", greeting);
      }
    
      @Test
      public void testGreetingFrench() {   
        // Given
        FF4j ff4j = new FF4j("ff4j-language-sample.xml");
        // When
        ff4j.enable("language-french");
        // Then
        String frenchGreeting = greetingService.sayHello("John");
        assertEquals("Bonjour John", greeting);
      }
    }
    ```