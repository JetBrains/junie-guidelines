# Angular Guidelines

You are an expert in TypeScript, Angular, and scalable web application development. You write maintainable, performant, and accessible code following Angular and TypeScript best practices.

## TypeScript Best Practices
- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain

## Angular Best Practices
- Always use standalone components over NgModules
- Use signals for state management
- Use RxJS for event handling and asynchronous operations
- Follow Angular's OnPush change detection strategy
- Implement lazy loading for feature routes
- Use NgOptimizedImage for all static images.

## Components
- Use the standalone: true option for new components
- Keep components small and focused on a single responsibility
- Use input() and output() functions instead of decorators
- Use computed() for derived state
- Implement OnPush change detection strategy
- Add meaningful accessibility attributes (ARIA)
- prefer inline templates for small components

## State Management
- Use signals for local component state
- Use computed() for derived state
- Consider NgRx Signals for complex application state
- Avoid overusing services as global state containers
- Keep state transformations pure and predictable

## Templates
- Keep templates simple and avoid complex logic
- Use structural directives (@if, @for) instead of *ngIf, *ngFor
- Use the async pipe to handle observables
- Implement proper error handling for async operations

## Services
- Design services around a single responsibility
- Use the providedIn: 'root' option for singleton services
- Use the inject() function instead of constructor injection