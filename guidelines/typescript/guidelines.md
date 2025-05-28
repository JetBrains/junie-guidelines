# TypeScript Guidelines

You are an expert in TypeScript and scalable web application development. You write maintainable, performant, and type-safe code following TypeScript best practices.

## Naming Conventions
- Use camelCase for variables, functions, and methods
- Use PascalCase for classes, interfaces, and types
- Use UPPER_SNAKE_CASE for constants
- Avoid using underscore prefix for private properties
- Use descriptive names that accurately reflect the purpose

## Types
- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid using `any` type; use `unknown` when type is uncertain
- Only use `any` inside generic functions when TypeScript cannot match runtime logic to type logic
- Use interfaces for object shapes that will be implemented or extended
- Use type aliases for union types, intersection types, and complex types
- Use generics to create reusable components
- Use discriminated unions to model data with different shapes and prevent impossible states

## Classes
- Keep classes focused on a single responsibility
- Use access modifiers (public, private, protected) for class members
- Prefer readonly for immutable properties
- Use parameter properties to simplify constructor definitions

## Functions
- Prefer arrow functions for callbacks and anonymous functions
- Use function declarations for named functions
- Use parameter default values instead of conditional logic in the function body
- Use rest parameters instead of arguments object
- Use async/await instead of raw promises when possible

## Interfaces and Types
- Use interfaces for object shapes that will be implemented or extended
- Use type aliases for union types, intersection types, and tuple types
- Prefer interfaces over type aliases for public APIs
- Use readonly modifier for immutable properties
- Extend interfaces instead of creating intersection types when possible

## Enums
- Avoid introducing new enums into the codebase
- Use `as const` objects with type assertions for enum-like behavior
- For existing enums, be aware that numeric enums produce reverse mappings

## Imports and Exports
- Use named exports instead of default exports (unless explicitly required by a framework)
- Group imports by their source
- Avoid namespace imports (import * as X)
- Use absolute imports for external dependencies and relative imports for internal modules
- Use top-level `import type` when importing types (e.g., `import type { User } from "./user"`)
- Export types explicitly with the type keyword

## Control Flow
- Use early returns to reduce nesting
- Prefer conditional expressions for simple conditionals
- Use switch statements for multiple conditions on the same variable
- Always use strict equality (===) and inequality (!==)
- Use optional chaining (?.) and nullish coalescing (??) operators

## Error Handling
- Use specific error types instead of generic Error
- Handle errors at the appropriate level
- Use try/catch blocks for synchronous code that may throw
- Use async/await with try/catch for asynchronous code
- Avoid swallowing errors silently

## Comments and Documentation
- Use JSDoc comments for public APIs
- Keep comments up-to-date with code changes
- Document non-obvious behavior
- Use inline comments sparingly
- Write self-documenting code with descriptive names
