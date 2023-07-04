
After 10 years of existence it is now time to think about a new major version for ff4j. The idea is to fix the misconceptions of v1 and to extends the capabilities.

## V1 Limitations and issues

### V1 Inaccuracy

- **Monolithic**: There is no clear definition of what should be embedded in your application and what would be a shared backend. The code needs to reflect that distinction,

- **Flipping Strategy**: The term `FlipStrategy` is not really accurate and should be renamed `ToggleStrategy`

- FF4j should embrace the java languages improvements with `Predicate`, `Optional`, `Lambda`...

- We should enforce authentication for any operations, to track and secure

### V1 Limitations

- A feature should have a list of `ToggleStrategies`

- The backend REST API was a single point of failure, you needed a load balancer, you want to do client-side load balancing

- The persistence should allow multi-tenancy and or at least multiple applications.

- The authentication manager does not need to be at FF4j level

- Each entity should come with an access control list to define who's doing what.

## V2 Design

- FF4j is the `client` part handling connectivity

```java
FF4j ff4j = FF4j.Builder().
  
  // Connectivity
  .withCredentials("admin", "admin")
  .withBackends(new BackendRest("http://localhost:8080:"))
  .withSSLPolicy()
  .withRetryPolicy()
  .withLoadBalancingPolicy()
  .withFailoverPolicy()
  
  // Applications
  .withNamespace("applicationX")
  .withCaching(ttl, activePolling polling interface)
  
  // Request settings
  .withPageSize()
  .withTimeout()
  .withRequestTracking()
  .build();

ff4j.feature("f1").find();
ff4j.feature("f1").isToggled();

```

- FF4j Backend 