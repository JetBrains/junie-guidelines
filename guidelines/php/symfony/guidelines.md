# Symfony Guidelines

You are an expert in PHP, Symfony, and scalable web application development. You write secure, maintainable, and performant code following Symfony and PHP best practices.

## PHP Best Practices
- Follow PSR-12 coding standard for consistent code style
- Use strict typing: `declare(strict_types=1);` in all PHP files
- Use type hinting for all function arguments and return types, including union types and `mixed` where appropriate
- Use PHP 8+ features like constructor property promotion, attributes, and match expressions
- Prefer `readonly` classes and properties for immutable data objects

## Symfony Best Practices
- Use Autowiring and Autoconfiguration for services whenever possible
- Define services in `config/services.yaml` only when manual configuration is required
- Use Symfony Attributes (`#[Route]`, `#[AsEventListener]`, etc.) instead of YAML or XML configuration
- Keep the `public/` directory for public assets only; all logic should be in `src/`
- Use the Symfony CLI for local development and managing the application environment

## Models (Doctrine Entities)
- Use Doctrine ORM for database interactions; keep entities as Plain Old PHP Objects (POPOs)
- Use Repository pattern for custom database queries; keep logic out of Entity classes
- Use PHP attributes for mapping (`#[ORM\Entity]`, `#[ORM\Column]`, etc.)
- Prefer `uuid` or `ulid` over auto-incrementing integers for primary keys in distributed systems
- Always add updated_at, and deleted_at datetime fields, with the soft delete filter

## Views (Twig)
- Use Twig for all HTML templates
- Follow the "base template" pattern with blocks for inheritance
- Keep templates logic-free; move complex logic to Twig Extensions or View Models
- Use the `asset()` function for linking CSS, JS, and images

## Controllers
- Keep controllers "thin" by delegating business logic to Service classes
- Extend `AbstractController` to access helpful shortcuts like `render()`, `redirectToRoute()`, and `createForm()`
- Use PHP attributes for routing (`#[Route('/path', name: 'route_name')]`)
- Use Type Hinting for dependency injection in controller actions
- Always return a `Response` object (or a subclass like `JsonResponse` or `RedirectResponse`)

## Forms
- Use the Symfony Form component for handling user input
- Create dedicated Form Type classes in `src/Form/` instead of building forms in controllers
- Use Data Transformers for complex data mapping if necessary
- Validate data using Symfony Validator constraints directly on the underlying Data Transfer Object (DTO) or Entity
- Use `handleRequest()` and `isSubmitted() && isValid()` pattern for form processing

## Application Config
- Use environment variables (`.env`, `.env.local`, `.env.test`) for environment-specific configuration
- Never commit secrets (API keys, database passwords) to version control; use Symfony Secrets (Vault) for sensitive data
- Organize configuration into separate files in `config/packages/` for better maintainability

## Database
- Use Doctrine Migrations for all database schema changes
- Use database indexes for frequently queried fields to optimize performance
- Use type-safe repository methods to fetch data

## Unit & Functional Tests
- Use PHPUnit for unit and functional testing
- Write tests if appropriate for new features and check that they pass before merging
- Use `WebTestCase` for functional tests to simulate browser interactions and crawl the DOM
- Use `KernelTestCase` for integration tests that require access to the service container
- Test both positive (happy path) and negative (error handling) scenarios
- Mock out dependencies to external resources such as third party APIs or long-running DB queries