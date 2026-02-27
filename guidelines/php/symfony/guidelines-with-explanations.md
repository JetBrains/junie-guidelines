# Symfony Guidelines

You are an expert in PHP, Symfony, and scalable web application development. You write secure, maintainable, and performant code following Symfony and PHP best practices.

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

## Symfony Best Practices
- Use Autowiring and Autoconfiguration for services whenever possible
- Define services in `config/services.yaml` only when manual configuration is required
- Use Symfony Attributes (`#[Route]`, `#[AsEventListener]`, etc.) instead of YAML or XML configuration
- Keep the `public/` directory for public assets only; all logic should be in `src/`
- Use the Symfony CLI for local development and managing the application environment

**Explanation:**
- Autowiring allows Symfony to automatically inject dependencies into your services based on type-hints, reducing the need for manual configuration.
- Attributes (introduced in PHP 8.0) keep the configuration close to the code, improving discoverability and readability.
- The `src/` directory should contain all PHP code, while `public/` is only for the web server's entry point (`index.php`) and assets like CSS, JS, and images.
- The Symfony CLI provides a built-in web server and helpful commands for managing your project and environment.

## Models (Doctrine Entities)
- Use Doctrine ORM for database interactions; keep entities as Plain Old PHP Objects (POPOs)
- Use Repository pattern for custom database queries; keep logic out of Entity classes
- Use PHP attributes for mapping (`#[ORM\Entity]`, `#[ORM\Column]`, etc.)
- Prefer `uuid` or `ulid` over auto-incrementing integers for primary keys in distributed systems
- Always add updated_at, and deleted_at datetime fields, with the soft delete filter

**Explanation:**
- Entities should focus on representing data and relationships; business logic should reside in services.
- Repositories centralize database access logic, making it easier to optimize and test queries.
- `UUID` or `ULID` are better for distributed systems as they can be generated client-side and don't leak information about the total number of records.
- Soft deletes and timestamp fields are essential for data auditing and recovery.

## Views (Twig)
- Use Twig for all HTML templates
- Follow the "base template" pattern with blocks for inheritance
- Keep templates logic-free; move complex logic to Twig Extensions or View Models
- Use the `asset()` function for linking CSS, JS, and images

**Explanation:**
- [Twig](https://twig.symfony.com/) is a powerful, secure template engine that helps keep presentation separate from business logic.
- Template inheritance allows you to define a common layout (e.g., header, footer) and override only specific parts (blocks) in child templates.
- If a template requires complex logic, it's often a sign that you should move that logic to a Twig Extension or a dedicated View Model/DTO.

## Controllers
- Keep controllers "thin" by delegating business logic to Service classes
- Extend `AbstractController` to access helpful shortcuts like `render()`, `redirectToRoute()`, and `createForm()`
- Use PHP attributes for routing (`#[Route('/path', name: 'route_name')]`)
- Use Type Hinting for dependency injection in controller actions
- Always return a `Response` object (or a subclass like `JsonResponse` or `RedirectResponse`)

**Explanation:**
- "Thin" controllers act as a thin glue layer between the request and your domain logic (services).
- Extending `AbstractController` provides a clean API for common controller tasks.
- Returning a proper `Response` object ensures that the framework handles the output and headers correctly.

## Forms
- Use the Symfony Form component for handling user input
- Create dedicated Form Type classes in `src/Form/` instead of building forms in controllers
- Use Data Transformers for complex data mapping if necessary
- Validate data using Symfony Validator constraints directly on the underlying Data Transfer Object (DTO) or Entity
- Use `handleRequest()` and `isSubmitted() && isValid()` pattern for form processing

**Explanation:**
- Dedicated Form Type classes make forms reusable and easier to test.
- Using DTOs with validation constraints is often safer and more flexible than binding forms directly to Entities.
- The `handleRequest()` pattern is the standard way to process form submissions in Symfony.

## Application Config
- Use environment variables (`.env`, `.env.local`, `.env.test`) for environment-specific configuration
- Never commit secrets (API keys, database passwords) to version control; use Symfony Secrets (Vault) for sensitive data
- Organize configuration into separate files in `config/packages/` for better maintainability

**Explanation:**
- Environment variables allow you to configure the application differently across local, staging, and production environments.
- [Symfony Secrets](https://symfony.com/doc/current/configuration/secrets.html) provides a secure way to manage sensitive data by encrypting them.
- Breaking down configuration into small files in `config/packages/` prevents the main configuration file from becoming unmanageable.

## Database
- Use Doctrine Migrations for all database schema changes
- Use database indexes for frequently queried fields to optimize performance
- Use type-safe repository methods to fetch data

**Explanation:**
- Migrations provide a version-controlled way to manage your database schema, ensuring all environments stay in sync.
- Indexes are crucial for query performance, especially as your data grows.

## Unit & Functional Tests
- Use PHPUnit for unit and functional testing
- Write tests if appropriate for new features and check that they pass before merging
- Use `WebTestCase` for functional tests to simulate browser interactions and crawl the DOM
- Use `KernelTestCase` for integration tests that require access to the service container
- Test both positive (happy path) and negative (error handling) scenarios
- Mock out dependencies to external resources such as third party APIs or long-running DB queries

**Explanation:**
- Testing ensures that your application behaves as expected and prevents regressions.
- `WebTestCase` allows you to test the entire request/response lifecycle, including the generated HTML.
- `KernelTestCase` is useful for testing services that interact with other parts of the system or the database.
- Mocking external dependencies makes tests faster and more reliable.