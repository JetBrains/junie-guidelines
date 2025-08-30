# Golang - Gin Guidelines

## 1. Organize Project Structure

* Follow a domain-driven or feature-based structure rather than organizing by technical layers
* Keep related functionality together to improve code discoverability
* Use a consistent naming convention for packages and files

## 2. Dependency Injection with Explicit Construction

* Create service structs with explicit dependencies passed via constructors
* Avoid global variables or singletons
* Use interfaces to define dependencies for better testability

## 3. Centralized Error Handling

* Define custom error types for different error categories
* Use middleware to catch and handle errors consistently
* Return structured error responses with appropriate HTTP status codes

## 4. Secure Middleware Configuration

* Configure security-related middleware in the correct order
* Use HTTPS by default in production
* Implement proper CORS, CSP, and other security headers

## 5. Input Validation and Sanitization

* Validate all input data before processing
* Use a structured validation library compatible with Gin
* Sanitize inputs to prevent injection attacks

## 6. Secure Authentication and Authorization

* Use JWT or sessions with proper security configurations
* Don't store and use passwords, instead rely on OAuth2 and OIDC protocols
* If you must store passwords, use strong hashing algorithm (bcrypt/argon2)
* Implement proper authorization checks at every secured endpoint

## 7. Database Access Best Practices

* Use prepared statements to prevent SQL injection
* Implement context-aware database calls
* Apply proper database connection management

## 8. Structured Logging

* Use a structured logging library (e.g.: slog)
* Include contextual information in logs, (e.g.: traceId)
* Avoid logging sensitive information

## 9. API Design and Response Structure

* Define consistent response formats
* Use appropriate HTTP status codes
* Include pagination for list endpoints

## 10. Effective Testing

* Write unit tests for business logic
* Use Go's testing package and testify for assertions
* Implement integration tests for critical paths

## 11. Configuration Management

* Use environment variables for configuration
* Implement secure handling of secrets
* Provide sensible defaults

## 12. Context Propagation

* Use context for request scoped values and cancellation
* Propagate context through all layers of the application
* Set appropriate timeouts

## 13. Graceful Shutdown

* Implement graceful shutdown to handle in-flight requests
* Close resources properly when shutting down
* Use appropriate timeouts for shutdown
