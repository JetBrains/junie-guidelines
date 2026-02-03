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

### 3.1. Use Records for data holder classes
-   **Guideline:** Prefer using Java records for storing data holder classes.

-   **Example:**

    ```java
    // Good
    public record CustomerDTO(String name, String email) { }
    
    // Bad
    public class CustomerDTO {
        private String name;
        private String email;

        // Getters and setters
    }
    ```

### 3.2. Immutability

-   **Guideline:** Prefer immutable classes whenever possible.

-   **Example:**

    ```java
    // Good
    public final class Customer {
        private final String name;
        private final String email;

        public Customer(String name, String email) {
            this.name = name;
            this.email = email;
        }

        // Getters only
    }

    // Bad
    public class Customer {
        private String name;
        private String email;

        // Getters and setters
    }
    ```

-   **Explanation:** Immutable objects are inherently thread-safe and make the code easier to reason about.

### 3.3. Use Interfaces

-   **Guideline:** Program to interfaces, not implementations.

-   **Example:**

    ```java
    // Good
    List<String> names = new ArrayList<>();

    // Bad
    ArrayList<String> names = new ArrayList<>();
    ```

-   **Explanation:** This makes the code more flexible and easier to test, as you can easily swap out implementations.

### 3.4. Use Enums

-   **Guideline:** Use enums instead of string constants or integer constants.

-   **Example:**

    ```java
    // Good
    public enum Status {
        PENDING,
        ACTIVE,
        INACTIVE
    }

    // Bad
    public static final String STATUS_PENDING = "PENDING";
    public static final int STATUS_ACTIVE = 1;
    ```

-   **Explanation:** Enums are type-safe and provide more readable and maintainable code.

## 4. Exception Handling

### 4.1. Specific Exceptions

-   **Guideline:** Catch specific exceptions instead of `Exception` or `Throwable`.

-   **Example:**

    ```java
    // Good
    try {
        // ...
    } catch (IOException e) {
        // ...
    }

    // Bad
    try {
        // ...
    } catch (Exception e) {
        // ...
    }
    ```

-   **Explanation:** Catching specific exceptions makes the error handling more robust and prevents catching unexpected exceptions.

### 4.2. Don't Ignore Exceptions

-   **Guideline:** Never ignore exceptions. If you catch an exception, either handle it or rethrow it.

-   **Example:**

    ```java
    // Good
    try {
        // ...
    } catch (IOException e) {
        log.error("Failed to read file", e);
    }

    // Bad
    try {
        // ...
    } catch (IOException e) {
        // ignored
    }
    ```

-   **Explanation:** Ignoring exceptions can lead to subtle bugs and make the code harder to debug.

## 5. Concurrency

### 5.1. Use `java.util.concurrent`

-   **Guideline:** Prefer the high-level concurrency utilities in the `java.util.concurrent` package over low-level primitives like `wait()` and `notify()`.

-   **Example:**

    ```java
    // Good
    ExecutorService executor = Executors.newFixedThreadPool(10);
    executor.submit(() -> {
        // ...
    });

    // Bad
    new Thread(() -> {
        // ...
    }).start();
    ```

-   **Explanation:** The `java.util.concurrent` package provides a rich set of tools that are more robust and easier to use than the low-level concurrency primitives.

### 5.2. Avoid `volatile` for Complex Operations

-   **Guideline:** Use `volatile` only for simple atomic operations. For more complex operations, use `java.util.concurrent.atomic` or locks.

-   **Explanation:** `volatile` ensures visibility but not atomicity for compound actions. Using it incorrectly can lead to subtle concurrency bugs.

## 6. Use of `Optional`

### 6.1. `Optional` for Return Types

-   **Guideline:** Use `Optional` for return types when a method might not return a value.

-   **Example:**

    ```java
    // Good
    public Optional<Customer> findCustomerById(Long id) {
        // ...
    }

    // Bad
    public Customer findCustomerById(Long id) {
        // returns null if not found
    }
    ```

-   **Explanation:** Using `Optional` makes the API clearer and helps prevent `NullPointerException`.

### 6.2. Don't Use `Optional` for Fields or Parameters

-   **Guideline:** Do not use `Optional` for class fields or method parameters.

-   **Explanation:** This can make the code more complex and less readable. For optional dependencies, use method overloading or a nullable annotation.

## 7. Stream API Best Practices

### 7.1. Avoid Side Effects

-   **Guideline:** Avoid side effects in stream operations like `map()` and `filter()`.

-   **Example:**

    ```java
    // Good
    List<String> names = customers.stream()
        .map(Customer::getName)
        .collect(Collectors.toList());

    // Bad
    List<String> names = new ArrayList<>();
    customers.stream()
        .forEach(c -> names.add(c.getName()));
    ```

-   **Explanation:** Side effects in stream operations can lead to unpredictable behavior, especially in parallel streams.

### 7.2. Prefer Method References

-   **Guideline:** Prefer method references over lambdas when possible.

-   **Example:**

    ```java
    // Good
    List<String> names = customers.stream()
        .map(Customer::getName)
        .collect(Collectors.toList());

    // Bad
    List<String> names = customers.stream()
        .map(c -> c.getName())
        .toList();
    ```

-   **Explanation:** Method references are more concise and readable.

## 8. Collections

### 8.1. Use the Right Collection

-   **Guideline:** Choose the right collection for the job. Use `List` for ordered collections, `Set` for unordered collections with no duplicates, and `Map` for key-value pairs.
-   **Explanation:** Using the appropriate collection type improves performance and makes the code's intent clearer. For example, using a `Set` when you need to ensure uniqueness is more efficient than checking for duplicates in a `List` manually.

### 8.2. Prefer `isEmpty()` over `size() == 0`

-   **Guideline:** Use `isEmpty()` to check if a collection is empty.
-   **Example:**
    ```java
    // Good
    if (names.isEmpty()) { ... }

    // Bad
    if (names.size() == 0) { ... }
    ```
-   **Explanation:** `isEmpty()` is more readable and can be more performant for some collection types, as it may not need to count all the elements.

### 8.3. Return Empty Collections, Not Null

-   **Guideline:** Methods that return collections should return an empty collection instead of `null`.
-   **Example:**
    ```java
    // Good
    public List<String> getNames() {
        if ( ... ) {
            return Collections.emptyList();
        }
        // ...
    }

    // Bad
    public List<String> getNames() {
        if ( ... ) {
            return null;
        }
        // ...
    }
    ```
-   **Explanation:** This prevents `NullPointerException`s in the calling code and simplifies it, as the caller doesn't need to handle a `null` case.

### 8.4. Use Diamond Operator

-   **Guideline:** Use the diamond operator (`<>`) for generic type inference.
-   **Example:**
    ```java
    // Good
    List<String> names = new ArrayList<>();

    // Bad
    List<String> names = new ArrayList<String>();
    ```
-   **Explanation:** The diamond operator reduces boilerplate code and improves readability without sacrificing type safety.

### 8.5. Use `for-each` loop

-   **Guideline:** Prefer the `for-each` loop for iterating over collections.
-   **Example:**
    ```java
    // Good
    for (String name : names) { ... }

    // Bad
    for (int i = 0; i < names.size(); i++) { ... }
    ```
-   **Explanation:** The `for-each` loop is more concise, less error-prone (no off-by-one errors), and more readable. Use an iterator or a traditional for loop only when you need to modify the collection while iterating.

## 9. Date and Time

### 9.1. Prefer Java 8 Date-Time API

-   **Guideline:** Prefer using the Java 8 Date-Time API (`java.time.*`) over legacy `java.util.Date` and `java.util.Calendar`.

-   **Example:**

    ```java
    // Good
    LocalDate birthday = LocalDate.of(1990, Month.MAY, 12);
    LocalDate today = LocalDate.now(ZoneId.of("UTC"));
    Period age = Period.between(birthday, today);

    ZonedDateTime meeting = ZonedDateTime.of(2026, 2, 3, 9, 30, 0, 0, ZoneId.of("Europe/Berlin"));
    String iso = meeting.format(DateTimeFormatter.ISO_ZONED_DATE_TIME);

    // Bad
    Date date = new Date();
    Calendar cal = Calendar.getInstance();
    cal.set(1990, Calendar.MAY, 12);
    ```

-   **Explanation:** The `java.time` API is immutable, thread-safe, and more expressive. It offers clear types for different concepts (`Instant`, `LocalDate`, `LocalDateTime`, `ZonedDateTime`, `Duration`, `Period`) and comprehensive formatting/parsing with `DateTimeFormatter`.

## 10. Strings

### 10.1. Use Multiline Text Blocks for Multi-line Strings

-   **Guideline:** Use text blocks (`"""`), available since Java 15, for multi-line string literals (e.g., SQL, JSON, XML) instead of concatenation or `\n` escapes.

-   **Example:**

    ```java
    // Good
    String sql = """
        SELECT id, name
        FROM customers
        WHERE status = 'ACTIVE'
        ORDER BY name
        """;

    String json = """
        {
          "name": "Alice",
          "roles": ["USER", "ADMIN"]
        }
        """;

    // Bad
    String sqlBad = "SELECT id, name\n" +
                    "FROM customers\n" +
                    "WHERE status = 'ACTIVE'\n" +
                    "ORDER BY name";
    ```

-   **Explanation:** Text blocks improve readability, reduce escaping, and preserve intended formatting. They also support strip indentation and trailing newline control, making them ideal for embedded resources and templates.