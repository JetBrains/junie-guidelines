# Angular Guidelines

You are an expert in TypeScript, Angular, and scalable web application development. You write maintainable, performant, and accessible code following Angular and TypeScript best practices.

## TypeScript Best Practices
- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain

**Explanation:**
- Strict type checking catches more errors at compile time, reducing runtime errors
- Type inference keeps code cleaner while maintaining type safety
- `unknown` is type-safe and requires type checking before use, unlike `any` which bypasses type checking

## Angular Best Practices
- Always use standalone components over NgModules
- Use signals for state management
- Use RxJS for event handling and asynchronous operations
- Follow Angular's OnPush change detection strategy
- Implement lazy loading for feature routes
- Use NgOptimizedImage for all static images.

**Explanation:**
- Standalone components (introduced in Angular 14+) reduce boilerplate and simplify the application structure
- Signals (introduced in Angular 16+) provide a more efficient and predictable way to manage state compared to traditional observables
- RxJS is the recommended approach for handling asynchronous operations in Angular
- OnPush change detection improves performance by only checking for changes when inputs change or events occur
- Lazy loading reduces initial bundle size and improves application startup time
- NgOptimizedImage automatically optimizes images for better performance and Core Web Vitals

## Components
- Use the standalone: true option for new components
- Keep components small and focused on a single responsibility
- Use input() and output() functions instead of decorators
- Use computed() for derived state
- Implement OnPush change detection strategy
- Add meaningful accessibility attributes (ARIA)
- prefer inline templates for small components

**Explanation:**
- The "one concept per file" principle makes code more maintainable and easier to understand
- The new input()/output() functions (Angular 16+) provide better type safety and are more concise than decorators
- computed() signals automatically update when dependencies change, making derived state management more efficient
- Accessibility attributes ensure your application is usable by people with disabilities
- Inline templates are easier to maintain for small components as they keep related code together

## State Management
- Use signals for local component state
- Use computed() for derived state
- Consider NgRx Signals for complex application state
- Avoid overusing services as global state containers
- Keep state transformations pure and predictable

**Explanation:**
- Signals provide a more efficient and predictable way to manage state with less boilerplate
- Pure functions for state transformations make debugging easier and prevent side effects
- NgRx Signals combines the benefits of NgRx (predictable state management) with the simplicity of signals
- Overusing services as global state can lead to difficult-to-debug issues and tight coupling

## Templates
- Keep templates simple and avoid complex logic
- Use structural directives (@if, @for) instead of *ngIf, *ngFor
- Use the async pipe to handle observables
- Implement proper error handling for async operations

**Explanation:**
- Complex template logic is harder to test and maintain; move complex logic to the component class
- The new control flow syntax (@if, @for) introduced in Angular 17 is more efficient and type-safe
- The async pipe automatically subscribes and unsubscribes from observables, preventing memory leaks
- Error handling for async operations prevents broken UI states when operations fail

## Services
- Design services around a single responsibility
- Use the providedIn: 'root' option for singleton services
- Use the inject() function instead of constructor injection

**Explanation:**
- Single responsibility makes services easier to test and maintain
- providedIn: 'root' ensures a service is a singleton and enables tree-shaking
- The inject() function (introduced in Angular 14+) is more readable and offers better type inference than constructor injection