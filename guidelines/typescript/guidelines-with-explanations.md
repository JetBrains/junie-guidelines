# TypeScript Guidelines

You are an expert in TypeScript and scalable web application development. You write maintainable, performant, and type-safe code following TypeScript best practices.

## Source File Basics
- Use UTF-8 for file encoding
- Use 2 spaces for indentation
- Use LF (Unix-style) line endings
- End files with a newline

**Explanation:**
- UTF-8 is the standard encoding for web development and supports all characters
- 2 spaces is the convention in the TypeScript community and matches the official style guide
- LF line endings ensure consistency across different operating systems
- Ending files with a newline is a common convention that prevents issues with some tools

## Naming Conventions
- Use camelCase for variables, functions, and methods
- Use PascalCase for classes, interfaces, types, and enums
- Use UPPER_SNAKE_CASE for constants
- Avoid using underscore prefix for private properties
- Use descriptive names that accurately reflect the purpose

**Explanation:**
- These naming conventions follow JavaScript and TypeScript community standards
- PascalCase for type-level constructs helps distinguish them from variables and functions
- Descriptive names improve code readability and self-documentation
- Modern TypeScript uses access modifiers (private, protected) instead of underscores

## Types
- Enable strict type checking with `"strict": true` in tsconfig.json
- Prefer type inference when the type is obvious
- Avoid using `any` type; use `unknown` when type is uncertain
- Only use `any` inside generic functions when TypeScript cannot match runtime logic to type logic
- Use interfaces for object shapes that will be implemented or extended
- Use type aliases for union types, intersection types, and complex types
- Use generics to create reusable components
- Use discriminated unions to model data with different shapes and prevent impossible states

**Explanation:**
- Strict type checking catches more errors at compile time, reducing runtime errors
- Type inference keeps code cleaner while maintaining type safety
- `unknown` is type-safe and requires type checking before use, unlike `any` which bypasses type checking
- In complex generic functions, TypeScript sometimes cannot match runtime logic to type logic, making `any` necessary in specific cases
- Interfaces are better for objects that will be implemented or extended, while type aliases are better for complex types
- Generics allow you to create reusable components that work with a variety of types
- Discriminated unions use a common property (the discriminant) to distinguish between different object shapes, preventing impossible states and enabling exhaustive type checking

## Classes
- Keep classes focused on a single responsibility
- Use access modifiers (public, private, protected) for class members
- Prefer readonly for immutable properties
- Use parameter properties to simplify constructor definitions
- Implement interfaces to ensure type compatibility

**Explanation:**
- Single responsibility principle makes classes easier to understand, test, and maintain
- Access modifiers enforce encapsulation and prevent unintended modifications
- Readonly properties prevent accidental mutations and make code more predictable
- Parameter properties reduce boilerplate by combining parameter declaration and class property assignment
- Implementing interfaces ensures that classes adhere to a specific contract

## Functions
- Prefer arrow functions for callbacks and anonymous functions
- Use function declarations for named functions
- Use parameter default values instead of conditional logic in the function body
- Use rest parameters instead of arguments object
- Use async/await instead of raw promises when possible

**Explanation:**
- Arrow functions maintain the lexical `this` context, preventing common bugs
- Function declarations are hoisted and provide better stack traces
- Default parameter values make function signatures more self-documenting
- Rest parameters are type-safe, unlike the arguments object
- Async/await makes asynchronous code more readable and easier to reason about

## Interfaces and Types
- Use interfaces for object shapes that will be implemented or extended
- Use type aliases for union types, intersection types, and tuple types
- Prefer interfaces over type aliases for public APIs
- Use readonly modifier for immutable properties
- Extend interfaces instead of creating intersection types when possible

**Explanation:**
- Interfaces can be extended and implemented, making them more flexible for object types
- Type aliases are better for complex types like unions and intersections
- Interfaces are more familiar to developers coming from other languages
- Readonly properties prevent accidental mutations and make code more predictable
- Extended interfaces are often more readable than intersection types

## Enums
- Avoid introducing new enums into the codebase
- Use `as const` objects with type assertions for enum-like behavior
- For existing enums, be aware that numeric enums produce reverse mappings

**Explanation:**
- Enums can lead to unexpected behavior, especially numeric enums which produce reverse mappings
- `as const` objects with type assertions provide similar functionality with more predictable behavior
- Numeric enums create both forward (name → value) and reverse (value → name) mappings, doubling the number of keys
- String enums are more predictable than numeric enums but still have limitations compared to `as const` objects

## Imports and Exports
- Use named exports instead of default exports (unless explicitly required by a framework)
- Group imports by their source
- Avoid namespace imports (import * as X)
- Use absolute imports for external dependencies and relative imports for internal modules
- Use top-level `import type` when importing types (e.g., `import type { User } from "./user"`)
- Export types explicitly with the type keyword

**Explanation:**
- Named exports are more refactoring-friendly and work better with static analysis tools
- Default exports can create confusion from the importing file and make refactoring more difficult
- Some frameworks (like Next.js) require default exports for specific features
- Grouped imports improve readability and organization
- Namespace imports can lead to name collisions and make it harder to track usage
- Top-level `import type` ensures type imports are completely erased during transpilation
- Consistent import styles make the codebase more maintainable
- Explicit type exports help TypeScript optimize the build process

## Control Flow
- Use early returns to reduce nesting
- Prefer conditional expressions for simple conditionals
- Use switch statements for multiple conditions on the same variable
- Always use strict equality (===) and inequality (!==)
- Use optional chaining (?.) and nullish coalescing (??) operators

**Explanation:**
- Early returns reduce cognitive load by minimizing nesting and making code more linear
- Conditional expressions are more concise for simple cases
- Switch statements are more readable than multiple if/else statements for the same variable
- Strict equality avoids unexpected type coercion issues
- Optional chaining and nullish coalescing operators simplify handling of potentially undefined values

## Error Handling
- Use specific error types instead of generic Error
- Handle errors at the appropriate level
- Use try/catch blocks for synchronous code that may throw
- Use async/await with try/catch for asynchronous code
- Avoid swallowing errors silently

**Explanation:**
- Specific error types provide more information about what went wrong
- Handling errors at the appropriate level prevents error propagation and improves user experience
- Try/catch blocks allow for graceful error handling and recovery
- Async/await with try/catch makes asynchronous error handling more intuitive
- Silent error swallowing makes debugging difficult and can hide serious issues

## Comments and Documentation
- Use JSDoc comments for public APIs
- Keep comments up-to-date with code changes
- Document non-obvious behavior
- Use inline comments sparingly
- Write self-documenting code with descriptive names

**Explanation:**
- JSDoc comments provide rich documentation that integrates with IDEs
- Outdated comments are worse than no comments as they can mislead developers
- Non-obvious behavior needs explanation to prevent confusion
- Excessive inline comments can indicate overly complex code that should be refactored
- Self-documenting code with descriptive names reduces the need for comments
