# Spring Boot Guidelines

1. Prefer Constructor Injection over Field/Setter Injection
   * Declare all the mandatory dependencies as `final` fields and inject them through constructor.
   * Spring will auto-detect if there is only one constructor, no need to add `@Autowired` on the constructor.
   * Avoid field/setter injection in production code.

2. Limit using `public` Visibility Modifier
   * Controllers and their methods, `@Configuration` classes and the `@Bean` definition methods visibility can be package-private.

3. Organize Configuration with Typed Properties
   * Group application-specific configuration properties with a common prefix in `application.properties` or `.yml`.
   * Bind them to `@ConfigurationProperties` classes with validation annotations so that the application will fail fast if the configuration is invalid.

4. Define Clear Transaction Boundaries
   * Treat the `@Service` layer methods as transactional units.
   * Mark methods with read-only operations with `@Transactional(readOnly = true)`.
   * Mark methods with write operations with plain `@Transactional`.
   * Keep each transaction's scope as small as possible.

5. Disable Open Session in View
   * While using Spring Data JPA, disable Open Session in View filter by setting `spring.jpa.open-in-view=false` in `application.properties/yml`.

6. Keep JPA Entities Out of Your Web Layer
   * Don't expose entities directly as responses in controllers.
   * Use simple Request/Response records instead.
   * Apply Jakarta Validation annotations on Request records for input validations.

7. Use proper REST API guidelines
   * Use the URL patterns as `/api/{version}/resource`
   * Return appropriate HTTP Status codes using `ResponseEntity`

8. Use Command Objects for Business Operations
   * Create purpose-built command records (e.g., `CreateOrderCommand`) to wrap input data.
   * Pass these commands into service methods to carry out create/update logic.

9. Centralize Error Handling
   * Implement a `@ControllerAdvice` (or `@RestControllerAdvice`) with `@ExceptionHandler` methods.
   * Return uniform error responses—consider using the `ProblemDetails` response format (RFC 7807).

10. Actuator
    * Expose only necessary actuator endpoints (such as `/health`, `/info`, `/metrics`) without security. 
    * Secure all the other actuator endpoints.

11. i18n using ResourceBundles
    * Use ResourceBundles to show text in the user-preferred locale instead of hard coding.

12. Use Testcontainers for integrations. 
    * Spin up real services (databases, message brokers, etc.) in your integration tests to mirror production environments.

13. Randomize the application port during integration testing. 
    * When running integration tests, start the application on a random available port to avoid port conflicts by using `@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)`.

14. Logging
    * Don't use `System.out.println()` to log any information. Use **SLF4J** Logger.
    * Make sure not to log any sensitive information.
    * While logging verbose information using `debug`, or `trace` log level, that may involve method calls, check if the debug/trace log level is enabled.
