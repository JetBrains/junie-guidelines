# Java Guidelines

These are the general guidelines for writing Java code.

## Table of Contents

- [1. Naming Conventions](#1-naming-conventions)
- [2. Code Layout](#2-code-layout)
- [3. Best Practices for Classes, Interfaces, and Enums](#3-best-practices-for-classes-interfaces-and-enums)
- [4. Exception Handling](#4-exception-handling)
- [5. Concurrency](#5-concurrency)
- [6. Use of `Optional`](#6-use-of-optional)
- [7. Stream API Best Practices](#7-stream-api-best-practices)
- [8. Collections](#8-collections)
- [9. Date and Time](#9-date-and-time)
- [10. Strings](#10-strings)

## 1. Naming Conventions

Follow the Java naming conventions.

- Package names should be all lowercase, without underscores or other special characters. They should be short, meaningful, and based on the project's domain.
- Class and interface names should be in `PascalCase` and should be nouns or noun phrases.
- Method names should be in `camelCase` and should be verbs or verb phrases.
- Variable names should be in `camelCase` and should be short and meaningful. Avoid single-letter variable names except for loop counters.
- Constant names should be in `UPPER_SNAKE_CASE`.

## 2. Code Layout

- Use 4 spaces for indentation. Do not use tabs.
- Consistent indentation is crucial for readability. Using spaces instead of tabs ensures that the code looks the same on all systems.
- Keep lines of code under 120 characters.
- When wrapping lines, break after a comma or an operator. Indent the new line with 8 spaces.

## 3. Best Practices for Classes, Interfaces, and Enums

- Prefer using Java records for storing data holder classes.
- Prefer immutable classes whenever possible.
- Program to interfaces, not implementations.
- Use enums instead of string constants or integer constants.

## 4. Exception Handling

- Catch specific exceptions instead of `Exception` or `Throwable`.
- Never ignore exceptions. If you catch an exception, either handle it or rethrow it.

## 5. Concurrency

- Prefer the high-level concurrency utilities in the `java.util.concurrent` package over low-level primitives like `wait()` and `notify()`.
- Use `volatile` only for simple atomic operations. For more complex operations, use `java.util.concurrent.atomic` or locks.

## 6. Use of `Optional`

- Use `Optional` for return types when a method might not return a value.
- Do not use `Optional` for class fields or method parameters.

## 7. Stream API Best Practices

- Avoid side effects in stream operations like `map()` and `filter()`.
- Prefer method references over lambdas when possible.

## 8. Collections

- Choose the right collection for the job. Use `List` for ordered collections, `Set` for unordered collections with no duplicates, and `Map` for key-value pairs.
- Use `isEmpty()` to check if a collection is empty.
- Methods that return collections should return an empty collection instead of `null`.
- Use the diamond operator (`<>`) for generic type inference.
- Prefer the `for-each` loop for iterating over collections.

## 9. Date and Time

- Prefer using the Java 8 Date-Time API (`java.time.*`) over the legacy `java.util.Date`/`java.util.Calendar` APIs.

## 10. Strings

- Use multiline text blocks (`"""`), available since Java 15, for multi-line string literals (e.g., SQL, JSON, XML) instead of concatenation or newline escapes.
