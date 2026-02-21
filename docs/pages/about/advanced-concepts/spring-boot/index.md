# Spring Boot

FF4j has provided Spring Boot starters which helps in integrating FF4j into your Spring Boot
application. The starters provide autoconfiguration for FF4j, making it easy to set up and use. With
the starters, you can easily enable or disable features in your application based on the
configuration of FF4j.

## Overview

FF4j has 2 Spring Boot starter

- [
  `ff4j-spring-boot-starter-webmvc`](https://github.com/ff4j/ff4j-spring-boot-starter-parent?tab=readme-ov-file#ff4j-spring-boot-starter-webmvc) -
  The starter for Spring MVC applications.
- [
  `ff4j-spring-boot-starter-webflux`](https://github.com/ff4j/ff4j-spring-boot-starter-parent?tab=readme-ov-file#ff4j-spring-boot-starter-webflux) -
  The starter for Spring WebFlux applications.

| Feature               |  ff4j-spring-boot-starter-webmvc  | ff4j-spring-boot-starter-webflux  |
|-----------------------|:---------------------------------:|:---------------------------------:|
| RESTful APIs          |                 ✅                 |                 ✅                 |
| OpenAPI Documentation |                 ✅                 |                 ✅                 |
| Web Console           |                 ✅                 |                 ❌                  |

## Sample

### With `ff4j-spring-boot-starter-webmvc`

!!! info

    The complete sample is available in the [ff4j-spring-boot-starter-webmvc-sample](https://github.com/ff4j/ff4j-samples/tree/master/ff4j-spring-boot-samples/ff4j-spring-boot-starter-webmvc-sample)

=== "1. Dependency"

    Add the dependency [`ff4j-spring-boot-starter-webmvc`]()

    ```xml title="pom.xml"
    <dependency>
      <groupId>org.ff4j</groupId>
      <artifactId>ff4j-spring-boot-starter-webmvc</artifactId>
      <version>${ff4j.version}</version>
    </dependency>
    ```

=== "2. Configuration"

    Configure FF4j

    ```kotlin title="FF4JConfiguration.kt"
    import org.ff4j.FF4j
    import org.ff4j.conf.XmlParser
    import org.springframework.context.annotation.Bean
    import org.springframework.context.annotation.Configuration
    
    @Configuration
    class FF4JConfiguration {
      @Bean
      fun getFF4J(): FF4j = FF4j(XmlParser(), "ff4j-features.xml")
    }
    ```

=== "3. Bootstrap"

    Run the application with `mvn spring-boot:run` and access the FF4j web console at `http://localhost:8080/ff4j-console`

### With `ff4j-spring-boot-starter-webflux`

!!! info

    The complete sample is available in the [ff4j-spring-boot-starter-webflux-sample](https://github.com/ff4j/ff4j-samples/tree/master/ff4j-spring-boot-samples/ff4j-spring-boot-starter-webflux-sample)

=== "1. Dependency"

    Add the dependency [`ff4j-spring-boot-starter-webmvc`]()

    ```xml title="pom.xml"
    <dependency>
      <groupId>org.ff4j</groupId>
      <artifactId>ff4j-spring-boot-starter-webflux</artifactId>
      <version>${ff4j.version}</version>
    </dependency>
    ```

=== "2. Configuration"

    Configure FF4j

    ```kotlin title="FF4JConfiguration.kt"
    import org.ff4j.FF4j
    import org.ff4j.conf.XmlParser
    import org.springframework.context.annotation.Bean
    import org.springframework.context.annotation.Configuration
    
    @Configuration
    class FF4JConfiguration {
      @Bean
      fun getFF4J(): FF4j = FF4j(XmlParser(), "ff4j-features.xml")
    }
    ```

=== "3. Bootstrap"

    Run the application with `mvn spring-boot:run` and access the FF4j web console at `http://localhost:8080/ff4j-console`