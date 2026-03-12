# iOS SwiftUI Coding Guidelines for AI Assistants

## Overview

These coding standards define how AI assistants should contribute to the project.

---

## 1. Core Project Requirements

* Xcode Version: 16.0+
* Swift Version: 6.1+
* iOS Deployment Target: 17.0+
* Architecture: MVVM (Modular, feature-first)
* Target Device: iPhone 16 Pro

**Explanation:**

* **Version compatibility:** Using the latest stable versions ensures access to the newest language features, performance improvements, and bug fixes. Swift 6.1 introduces enhanced concurrency safety and improved type inference.
* **iOS 17.0+ deployment target:** Allows leveraging modern SwiftUI features while maintaining reasonable device compatibility. iOS 17 introduced significant SwiftUI improvements including enhanced navigation and data flow patterns.
* **MVVM with modular architecture:** Promotes separation of concerns, testability, and maintainability. Feature-first organization keeps related components together, making the codebase easier to navigate and modify.
* **iPhone 16 Pro target:** Ensures optimal performance and UI layout for the latest hardware while providing guidance for responsive design considerations.

---

## 2. Swift & SwiftUI Best Practices

* Use the Observation framework (@Observable) instead of ObservableObject where supported.
* Always use async/await instead of callbacks or Combine for new code
* Prefer `navigationDestination` and `NavigationStack` over `.sheet` or `.fullScreenCover`
* Avoid UIKit unless SwiftUI cannot support a use-case (justify and document exception)
* Modularize by feature, not by type (views, models, services grouped together per feature)

**Explanation:**

* **Observation framework:** The new `@Observable` macro provides better performance and simpler syntax compared to `ObservableObject`. It automatically tracks property changes without requiring `@Published` wrappers and reduces boilerplate code.
* **Async/await over Combine:** Modern Swift concurrency is more readable, has better error handling, and integrates seamlessly with SwiftUI's lifecycle. It eliminates callback hell and reduces complexity in asynchronous operations.
* **NavigationStack advantages:** The newer navigation system provides more predictable behavior, better state management, and improved performance compared to sheet presentations. It also offers more granular control over navigation flow.
* **SwiftUI-first approach:** Maintaining consistency in the UI framework reduces complexity and ensures better integration with SwiftUI's declarative paradigm. UIKit exceptions should be rare and well-documented.
* **Feature-based modularization:** Groups related functionality together, making it easier to understand, test, and modify specific features without affecting unrelated parts of the application.

---

## 3. Architecture Standards (MVVM)

* Flow: Views → ViewModels → Service / API Client
* API Client → Network Layer (URLSession or 3rd-party lib)
* Never expose raw models to the View layer
* Each ViewModel owns a protocol-conforming Service instance injected via initializer
* Use `Task {}` blocks for async loading in ViewModels, with proper cancellation handling

**Explanation:**

* **Clear separation of concerns:** The unidirectional flow ensures that each layer has a single responsibility. Views handle presentation, ViewModels manage state and business logic, and Services handle data operations.
* **Protocol-based dependency injection:** Using protocols for services enables easy testing with mock implementations and promotes loose coupling between components.
* **Model encapsulation:** Preventing direct model exposure to views allows for better data transformation, validation, and presentation logic without coupling UI to data structures.
* **Task-based async operations:** Structured concurrency with `Task {}` blocks provides automatic cancellation when views disappear, preventing memory leaks and unnecessary work.

```swift
import SwiftUI

// MARK: - Service Protocol
protocol ExampleServiceProtocol {
  func fetchDailyQuote() async throws -> String
}

// MARK: - ViewModel
@Observable
final class ExampleViewModel {
  var quote: String = ""
  var errorMessage: String?
  var isLoading: Bool = false

  private let service: ExampleServiceProtocol

  init(service: ExampleServiceProtocol) {
    self.service = service
  }

  @MainActor
  func loadQuote() async {
    isLoading = true
    errorMessage = nil // Clear previous errors

    do {
      quote = try await service.fetchDailyQuote()
    } catch {
      errorMessage = "Failed to fetch quote: \(error.localizedDescription)"
    }

    isLoading = false
  }
}

// MARK: - SwiftUI View
struct ExampleView: View {
  @State private var viewModel: ExampleViewModel

  init(service: ExampleServiceProtocol) {
    _viewModel = State(wrappedValue: ExampleViewModel(service: service))
  }

  var body: some View {
    VStack(spacing: 16) {
      if let error = viewModel.errorMessage {
        Text(error)
            .foregroundStyle(.red)
            .multilineTextAlignment(.center)
      } else if viewModel.isLoading {
        Text("Loading...")
            .foregroundStyle(.secondary)
      } else {
        Text(viewModel.quote.isEmpty ? "No quote available" : viewModel.quote)
            .multilineTextAlignment(.center)
      }

      Button("Reload Quote") {
        Task {
          await viewModel.loadQuote()
        }
      }
      .disabled(viewModel.isLoading)
    }
    .padding()
    .task {
      await viewModel.loadQuote()
    }
  }
}
```

---

## 4. Architecture Standards (MVVM + Repository)

* View → ViewModel
* ViewModel → Repository (abstracts data source)
* Repository → Service / API Client
* Each ViewModel owns a protocol-conforming Service instance injected via initializer
* In case you need a Repository, each ViewModel owns a protocol-conforming Repository instance, injected via initializer. Each Repository owns one or more protocol-conforming Service/API clients, injected via initializer.

**Explanation:**

* **Repository pattern benefits:** Adds an additional abstraction layer that can coordinate multiple data sources (network, local storage, cache), providing a single source of truth for data operations.
* **Data source abstraction:** Repositories can switch between different data sources transparently, enabling offline functionality, caching strategies, and data synchronization without affecting the ViewModel layer.
* **Enhanced testability:** Repository abstractions make it easier to test different data scenarios and edge cases by providing mock implementations that simulate various data states.

```swift
// MARK: - ViewModel
@Observable
final class ProfileViewModel {
  var profile: UserProfile?
  var errorMessage: String?
  var isLoading: Bool = false

  private let repository: ProfileRepository

  init(repository: ProfileRepository) {
    self.repository = repository
  }

  @MainActor
  func loadProfile() async {
    isLoading = true
    errorMessage = nil

    do {
      profile = try await repository.fetchUserProfile()
    } catch {
      errorMessage = "Failed to load profile: \(error.localizedDescription)"
    }

    isLoading = false
  }
}
```

---

## 5. Swift Style Guide

* Use `camelCase` for properties, methods
* Use `PascalCase` for types, structs, enums
* Use `let` over `var` wherever possible
* Max line length: 120 characters
* Use `guard` for early exits
* Avoid magic numbers; use constants/enums
* Use constants for spaces and colors
* Use trailing closures
* Add inline comments only when logic is non-obvious

**Explanation:**

* **Consistent naming conventions:** Following Swift's standard naming patterns improves code readability and makes it easier for team members to understand and maintain the codebase.
* **Immutability preference:** Using `let` wherever possible prevents accidental mutations and makes code more predictable and thread-safe, especially important in concurrent environments.
* **Early exit pattern:** Guard statements improve code readability by handling error conditions early and reducing nesting levels in the main logic flow.
* **Constants over magic values:** Named constants make code self-documenting and easier to maintain when values need to be changed across the application.
* **Readable line length:** 120-character limit balances readability with modern screen sizes while preventing overly long lines that are hard to follow.

---

## 6. Async & Concurrency

* Use `async throws` for all network calls and async operations
* Cancel outdated tasks with `@MainActor` or `Task.cancel()` as needed
* Prefer `TaskGroup` for parallel loading
* Always show a loading state while awaiting
* Handle timeouts and errors visibly and gracefully

**Explanation:**

* **Structured error handling:** `async throws` provides clear error propagation and handling, making it easier to manage different failure scenarios in a predictable way.
* **Automatic cancellation:** SwiftUI's task modifiers automatically cancel tasks when views disappear, preventing memory leaks and unnecessary network requests.
* **Parallel processing:** `TaskGroup` allows multiple async operations to run concurrently, improving app performance when loading multiple independent data sources.
* **User experience:** Visible loading states and graceful error handling keep users informed about app state and provide clear feedback when operations fail.
* **Resource management:** Proper task cancellation and timeout handling prevent resource waste and improve app responsiveness.

---

## 7. File & Folder Structure

* One view, model, and service per file
* Place unit tests in `Tests/Features/FeatureName/` using mock services

**Explanation:**

* **Single responsibility per file:** Keeping one primary component per file makes code easier to locate, understand, and maintain. It also reduces merge conflicts when multiple developers work on the same feature.
* **Feature-based test organization:** Organizing tests by feature mirrors the production code structure, making it easier to find and maintain relevant tests when features are modified.
* **Mock service usage:** Using mock services in tests provides predictable, fast, and isolated test environments that don't depend on external services or network conditions.

---

## 8. Testing & Mocks

* Ensure substantial coverage for ViewModels and Services
* Use mock objects and containers to simulate network/API and other calls that may be unavailable in SwiftUI previews.

**Explanation:**

* **Business logic testing:** ViewModels contain most business logic and state management, making them critical components to test thoroughly for various scenarios and edge cases.
* **Service layer testing:** Services handle data operations and external dependencies, requiring tests to ensure proper error handling, data transformation, and network resilience.
* **Preview compatibility:** Mock objects enable SwiftUI previews to work reliably without network dependencies, improving the development experience and allowing designers to see UI states.
* **Isolation and reliability:** Mock implementations provide consistent, controllable test environments that eliminate flakiness caused by external dependencies.

---

## 9. Privacy & Security

* Store credentials in Keychain only
* Use HTTPS with certificate pinning for all APIs
* Do not log health, payment, personal, or other sensitive info
* Document all data access clearly for user consent

**Explanation:**

* **Keychain security:** The iOS Keychain provides hardware-backed encryption for sensitive data and survives app reinstalls, making it the most secure option for credential storage.
* **Certificate pinning:** Prevents man-in-the-middle attacks by ensuring the app only accepts connections to servers with expected certificates, adding an extra layer of security beyond HTTPS.
* **Privacy compliance:** Avoiding logging of sensitive information protects user privacy and helps comply with regulations like GDPR and CCPA.
* **Transparency and consent:** Clear documentation of data access helps users make informed decisions and builds trust by being transparent about data usage.

---

## 10. UX & Accessibility

* Provide loading placeholders with `ProgressView`
* Show meaningful error messages, never blank states
* Follow large text and VoiceOver accessibility support
* Maintain UI responsiveness on all devices, especially iPhone SE

**Explanation:**

* **Loading feedback:** `ProgressView` provides visual feedback during async operations, preventing users from thinking the app has frozen or crashed.
* **Error communication:** Meaningful error messages help users understand what went wrong and often suggest how to resolve the issue, improving the overall user experience.
* **Accessibility compliance:** Supporting Dynamic Type and VoiceOver ensures the app is usable by people with visual impairments and meets Apple's accessibility guidelines.
* **Device compatibility:** Testing on iPhone SE (smallest screen) ensures the UI works well across all device sizes and prevents layout issues on smaller screens.

---

## 11. Performance Best Practices

* Lazy-load long lists using `LazyVStack`
* Throttle updates to real-time tracking views
* Cache static images and animations
* Defer heavy computations (e.g., pace analysis) to background
* Use Instruments to monitor memory and CPU during exercise modes

**Explanation:**

* **Lazy loading efficiency:** `LazyVStack` only creates views that are visible on screen, dramatically improving performance and memory usage for long lists.
* **Update throttling:** Real-time views can generate excessive updates; throttling prevents UI lag and reduces battery consumption while maintaining smooth user experience.
* **Resource caching:** Caching frequently used assets reduces network requests and improves app responsiveness, especially important for images and animations used across multiple screens.
* **Background processing:** Moving heavy computations off the main thread prevents UI blocking and maintains smooth animations and user interactions.
* **Performance monitoring:** Using Instruments during development helps identify performance bottlenecks and memory leaks before they affect users.

---

## 12. Code Review Checklist

* [ ] Code compiles and builds without warnings
* [ ] Uses SwiftUI + MVVM properly
* [ ] Dependencies injected via protocols
* [ ] All new async code uses async/await
* [ ] Unit tests cover new logic
* [ ] No duplicate files, logic, or hardcoded strings
* [ ] UI follows Apple guidelines
* [ ] Proper state and navigation handling

**Explanation:**

* **Quality assurance:** A systematic checklist ensures consistent code quality and catches common issues before they reach production.
* **Architecture compliance:** Verifying MVVM implementation and dependency injection patterns maintains codebase consistency and testability.
* **Modern practices:** Ensuring async/await usage and proper testing coverage helps maintain modern, maintainable code standards.
* **User experience:** Following Apple's UI guidelines and proper state management ensures the app feels native and behaves predictably for users.

---

## 13. AI Assistant: Ask Before You Code

1. Does this logic already exist?
2. Can it be reused via a protocol or utility?
3. What user problem is this solving?
4. What if the user has no internet or revoked permissions?

**Explanation:**

* **Avoid duplication:** Checking for existing implementations prevents code duplication and promotes reuse of tested, working solutions.
* **Reusability assessment:** Evaluating whether new code can be made reusable through protocols or utilities improves long-term maintainability and consistency.
* **User-centered development:** Always connecting code changes to user problems ensures that development efforts create actual value rather than unnecessary features.
* **Edge case consideration:** Thinking about offline scenarios and permission states early in development prevents runtime crashes and improves user experience in adverse conditions.

---

## Final Notes

These rules are a shared contract to build excellent, scalable, user-friendly apps. Respect clarity, consistency, and purpose in everything you build.