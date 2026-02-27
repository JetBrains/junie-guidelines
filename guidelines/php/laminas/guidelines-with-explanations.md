# Laminas Guidelines

You are an expert in PHP, Laminas, and scalable web application development. You write secure, maintainable, and performant code following Laminas and PHP best practices.

## PHP Best Practices
- Follow PSR-12 coding standard for consistent code style
- Use strict typing: `declare(strict_types=1);` in all PHP files
- Use type hinting for all function arguments and return types, including union types and `mixed` where appropriate
- Use PHP 8+ features like constructor property promotion, attributes, and match expressions
- Prefer `readonly` classes and properties for immutable data objects

**Explanation:**
- [PSR-12](https://www.php-fig.org/psr/psr-12/) is the current standard for PHP code style, replacing the older PSR-2.
- `strict_types=1` prevents PHP from silently casting types in function calls, helping to catch bugs early.
- PHP 8+ features like constructor property promotion significantly reduce boilerplate code.
- `readonly` properties and classes ensure immutability, making state easier to reason about in your application.

## Laminas Best Practices
- Use the ServiceManager for all dependency injection and management
- Use Factory classes for creating complex services and controllers to keep the ServiceManager configuration clean
- Use Laminas EventManager to decouple components and handle cross-cutting concerns
- Follow the "separation of concerns" by separating business logic from infrastructure logic

**Explanation:**
- The `ServiceManager` is the core of Laminas dependency injection; it centralizes service creation and configuration.
- Using `Factory` classes keeps your configuration clean and allows for complex instantiation logic outside of the `module.config.php`.
- The `EventManager` allows components to communicate without direct dependencies, making the application easier to extend.
- Separating business logic (domain) from infrastructure logic (persistence, framework) ensures that the core of your application remains decoupled and testable.

## Models
- Use Laminas\Db or Doctrine ORM for data persistence
- Use Hydrators to map database results to Plain Old PHP Objects (POPOs)
- Keep business logic in Domain Services, not in the database models themselves
- Use InputFilters to define validation and filtration rules for your domain models

**Explanation:**
- [Laminas\Db](https://docs.laminas.dev/laminas-db/) and [Doctrine](https://www.doctrine-project.org/) are the primary choices for data persistence in the Laminas ecosystem.
- `Hydrators` bridge the gap between database result sets (arrays) and objects, ensuring a clean domain model.
- Business logic belongs in `Domain Services` or the domain layer itself, not inside entities or database abstraction classes.
- `InputFilters` provide a powerful way to define how data should be filtered and validated before it ever reaches your models.

## Views
- Use Laminas\View with the PhpRenderer for standard templates
- Use View Helpers to handle presentation-specific logic (e.g., formatting dates, generating URLs)
- Avoid logic in view scripts beyond basic loops and conditionals
- Keep layouts separated from view scripts for better maintainability

**Explanation:**
- `Laminas\View` with `PhpRenderer` is the default for MVC applications; it uses standard PHP for templating.
- `View Helpers` are essential for logic that is strictly related to presentation, keeping templates clean and focused on structure.
- Avoiding complex logic in view scripts prevents the "spaghetti code" common in traditional PHP applications.
- Layouts provide a common wrapper for multiple view scripts, facilitating easier site-wide changes.

## Controllers
- Keep controllers "thin" by delegating business logic to Service classes
- Use Action Handlers (in Mezzio) or Action Controllers (in Laminas MVC) for request processing
- Return a `Laminas\Http\Response` or `Laminas\Diactoros\Response` (in Mezzio) for all requests
- Use Type Hinting for dependencies in the constructor or factory

**Explanation:**
- "Thin" controllers ensure that request-handling logic is minimal, delegating the heavy lifting to service classes.
- [Mezzio](https://docs.mezzio.dev/) uses PSR-15 Request Handlers, while Laminas MVC uses Action Controllers; both should focus on converting requests into responses.
- Always returning a `Response` object (standardized via PSR-7 in Mezzio) ensures consistency in how the framework sends output to the client.
- Type hinting dependencies in constructors (or factories) is a key part of dependency injection and improves code clarity.

## Forms
- Use the Laminas\Form component for building and handling HTML forms
- Separate form structure (Form classes) from validation logic (InputFilter classes)
- Use Fieldsets to group related form elements and promote reusability
- Always validate forms using the `isValid()` method before processing data

**Explanation:**
- [Laminas\Form](https://docs.laminas.dev/laminas-form/) provides a robust way to bridge HTML forms with your data models.
- Separating structure from validation allows you to reuse `InputFilters` in different contexts (e.g., API requests and web forms).
- `Fieldsets` promote reusability by allowing you to group related elements (like an address) and use them across multiple forms.
- The `isValid()` check is the gatekeeper for ensuring that only clean, validated data enters your application.

## Application Config
- Never commit secrets (API keys, database passwords) to version control
- Use `config/autoload/{{,*.}{global,local}.php}` pattern for environment-specific configuration
- Keep module-specific configuration within each module's `module.config.php`
- Use the `ConfigAbstractFactory` for simple services that don't need a dedicated factory class

**Explanation:**
- Secrets must never be stored in version control; they should be provided via environment variables or local, non-versioned config files.
- The `config/autoload/` pattern allows for overriding global settings with environment-specific (local) configurations.
- Keeping module-specific config within the module keeps it self-contained and portable.
- `ConfigAbstractFactory` is a great way to reduce boilerplate for simple services where you just need to inject other named services.

## Database
- Use database migrations (e.g., Laminas\Db\Sql\Ddl or Phinx) for all database schema changes
- Use database indexes for frequently queried fields to optimize performance
- Use Parameterized Queries or the Laminas\Db\Sql abstraction to prevent SQL injection
- Optimize database performance by avoiding N+1 query problems using eager loading or batch processing

**Explanation:**
- Database migrations provide a safe, versioned way to evolve your database schema alongside your code.
- Proper indexing is the single most important factor for database query performance.
- Using abstractions like `Laminas\Db\Sql` or parameterized queries is critical for preventing SQL injection attacks.
- N+1 query problems occur when a query is executed for each row of a result set; it should be avoided by using joins or batch fetching.

## Unit & Functional Tests
- Use PHPUnit for unit and functional testing
- Write tests if appropriate for new features and check that they pass before merging
- Test both positive (happy path) and negative (error handling) scenarios
- Mock out dependencies to external resources such as third party APIs or long-running DB queries
- Use `Laminas\Test` to perform integration tests for Laminas MVC or Mezzio applications

**Explanation:**
- Testing is essential for long-term maintainability; it ensures that new features don't break existing ones.
- Mocking out external dependencies (like APIs or complex DB states) makes your tests deterministic and fast.
- `Laminas\Test` provides specialized tools for testing the entire request/response lifecycle within the Laminas/Mezzio ecosystem.
