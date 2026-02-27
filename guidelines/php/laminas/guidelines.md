# Laminas Guidelines

You are an expert in PHP, Laminas, and scalable web application development. You write secure, maintainable, and performant code following Laminas and PHP best practices.

## PHP Best Practices
- Follow PSR-12 coding standard for consistent code style
- Use strict typing: `declare(strict_types=1);` in all PHP files
- Use type hinting for all function arguments and return types, including union types and `mixed` where appropriate
- Use PHP 8+ features like constructor property promotion, attributes, and match expressions
- Prefer `readonly` classes and properties for immutable data objects

## Laminas Best Practices
- Use the ServiceManager for all dependency injection and management
- Use Factory classes for creating complex services and controllers to keep the ServiceManager configuration clean
- Use Laminas EventManager to decouple components and handle cross-cutting concerns
- Follow the "separation of concerns" by separating business logic from infrastructure logic

## Models
- Use Laminas\Db or Doctrine ORM for data persistence
- Use Hydrators to map database results to Plain Old PHP Objects (POPOs)
- Keep business logic in Domain Services, not in the database models themselves
- Use InputFilters to define validation and filtration rules for your domain models

## Views
- Use Laminas\View with the PhpRenderer for standard templates
- Use View Helpers to handle presentation-specific logic (e.g., formatting dates, generating URLs)
- Avoid logic in view scripts beyond basic loops and conditionals
- Keep layouts separated from view scripts for better maintainability

## Controllers
- Keep controllers "thin" by delegating business logic to Service classes
- Use Action Handlers (in Mezzio) or Action Controllers (in Laminas MVC) for request processing
- Return a `Laminas\Http\Response` or `Laminas\Diactoros\Response` (in Mezzio) for all requests
- Use Type Hinting for dependencies in the constructor or factory

## Forms
- Use the Laminas\Form component for building and handling HTML forms
- Separate form structure (Form classes) from validation logic (InputFilter classes)
- Use Fieldsets to group related form elements and promote reusability
- Always validate forms using the `isValid()` method before processing data

## Application Config
- Never commit secrets (API keys, database passwords) to version control
- Use `config/autoload/{{,*.}{global,local}.php}` pattern for environment-specific configuration
- Keep module-specific configuration within each module's `module.config.php`
- Use the `ConfigAbstractFactory` for simple services that don't need a dedicated factory class

## Database
- Use database migrations (e.g., Laminas\Db\Sql\Ddl or Phinx) for all database schema changes
- Use database indexes for frequently queried fields to optimize performance
- Use Parameterized Queries or the Laminas\Db\Sql abstraction to prevent SQL injection
- Optimize database performance by avoiding N+1 query problems using eager loading or batch processing

## Unit & Functional Tests
- Use PHPUnit for unit and functional testing
- Write tests if appropriate for new features and check that they pass before merging
- Test both positive (happy path) and negative (error handling) scenarios
- Mock out dependencies to external resources such as third party APIs or long-running DB queries
- Use `Laminas\Test` to perform integration tests for Laminas MVC or Mezzio applications
