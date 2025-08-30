
# iOS SwiftUI Coding Guidelines for AI Assistants

## Overview

These coding standards define how AI assistants should contribute to the project.

---

## Core Project Requirements

- Xcode Version: 16.0+
- Swift Version: 6.1+
- iOS Deployment Target: 17.0+
- Architecture: MVVM (Modular, feature-first)
- Target Device: iPhone 16 Pro

---

## Swift & SwiftUI Best Practices

- Adopt `@Observable` over `ObservableObject` where supported
- Always use async/await instead of callbacks or Combine for new code
- Prefer `navigationDestination` and `NavigationStack` over `.sheet` or `.fullScreenCover`
- Avoid UIKit unless SwiftUI cannot support a use-case (justify and document exception)
- Modularize by feature, not by type (views, models, services grouped together per feature)

---

## Architecture Standards (MVVM)

(Note: The → symbol indicates “depends on” or “calls/uses” — not data flow.)
- Flow: Views → ViewModels → Service / API Client
- API Client → Network Layer (URLSession or 3rd-party lib)
- Never expose raw models to the View layer
- Each ViewModel owns a protocol-conforming Service instance injected via initializer
- Use `Task {}` blocks for async loading in ViewModels, with proper cancellation handling

### Example

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

## Architecture Standards (MVVM + Repository)

- View → ViewModel
- ViewModel → Repository (abstracts data source)
- Repository → Service / API Client
- Each ViewModel owns a protocol-conforming Service instance injected via initializer
- In case you need a Repository, each ViewModel owns a protocol-conforming Repository instance, injected via initializer.
  Each Repository owns one or more protocol-conforming Service/API clients, injected via initializer.

### Example

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

## Swift Style Guide

- Use `camelCase` for properties, methods
- Use `PascalCase` for types, structs, enums
- Use `let` over `var` wherever possible
- Max line length: 120 characters
- Use `guard` for early exits
- Avoid magic numbers; use constants/enums
- Use constants for spaces and colors
- Use trailing closures
- Add inline comments only when logic is non-obvious

---

## Async & Concurrency

- Use `async throws` for all network calls and async operations
- Cancel outdated tasks with `@MainActor` or `Task.cancel()` as needed
- Prefer `TaskGroup` for parallel loading
- Always show a loading state while awaiting
- Handle timeouts and errors visibly and gracefully

---

## File & Folder Structure

- One view, model, and service per file
- Place unit tests in `Tests/Features/FeatureName/` using mock services

---

## Testing & Mocks

- Ensure substantial coverage for ViewModels and Services
- Use mock objects and containers to simulate network/API and other calls that may be unavailable in SwiftUI previews.

---

## Privacy & Security

- Store credentials in Keychain only
- Use HTTPS with certificate pinning for all APIs
- Do not log health, payment, personal, or other sensitive info
- Document all data access clearly for user consent

---

## UX & Accessibility

- Provide loading placeholders with `ProgressView`
- Show meaningful error messages, never blank states
- Follow large text and VoiceOver accessibility support
- Maintain UI responsiveness on all devices, especially iPhone SE

---

## Performance Best Practices

- Lazy-load long lists using `LazyVStack`
- Throttle updates to real-time tracking views
- Cache static images and animations
- Defer heavy computations (e.g., pace analysis) to background
- Use Instruments to monitor memory and CPU during exercise modes

---

## Code Review Checklist

- [ ] Code compiles and builds without warnings
- [ ] Uses SwiftUI + MVVM properly
- [ ] Dependencies injected via protocols
- [ ] All new async code uses async/await
- [ ] Unit tests cover new logic
- [ ] No duplicate files, logic, or hardcoded strings
- [ ] UI follows Apple guidelines
- [ ] Proper state and navigation handling

---

## AI Assistant: Ask Before You Code

1. Does this logic already exist?
2. Can it be reused via a protocol or utility?
3. What user problem is this solving?
4. What if the user has no internet or revoked permissions?

---

## Final Notes

These rules are a shared contract to build excellent, scalable, user-friendly apps. Respect clarity, consistency, and purpose in everything you build.
