#flashcards/Architecture

How does VIPER differ from MVVM in terms of responsibility segregation, and when would you choose one over the other in an iOS app?
?
VIPER completely isolates routing (Router) and business logic (Interactor) from the presentation layer (Presenter). MVVM typically leaves routing to the View layer and keeps business logic in the ViewModel. Choose VIPER for large, highly complex apps needing strict boundaries and modularity; use MVVM for standard apps where VIPER's boilerplate would be over-engineering.

What are the architectural benefits of Dependency Injection (DI) in iOS, and what are the trade-offs between initializer, property, and method injection?
?
DI decouples object creation from usage, making components highly testable via mocks. Initializer injection enforces dependency requirements at compile time (safest). Property injection allows late initialization (e.g., Storyboards) but risks runtime crashes if unset. Method injection is best when a dependency varies per method call.

What problem does the Coordinator pattern solve in iOS architecture, and how does it interact with the view layer?
?
It removes navigation and routing logic from ViewControllers, adhering to the Single Responsibility Principle. The Coordinator handles flow control, view instantiation, and dependency injection. The view layer is kept completely oblivious to context, only reporting user actions back to the Coordinator via delegates or closures.

In Uncle Bob's Clean Architecture applied to iOS, what is the Dependency Rule, and how do the Domain, Data, and Presentation layers interact?
?
The Dependency Rule states that source code dependencies must point only _inward_ toward the Domain layer (business rules). The Presentation and Data layers depend on the Domain layer, but the Domain knows nothing about UI, databases, or APIs. Protocols are used to invert dependencies from the Domain to the outer layers.

Why might a senior iOS team choose to break a monolithic app into a multi-module architecture using Swift Package Manager?
?
Modularization drastically reduces build times, enforces strict architectural boundaries (preventing spaghetti code), enables horizontal scaling across multiple squads, and allows for isolated feature testing and shared code reusability across multiple targets (e.g., App Clips, Widgets).

How do you violate the Liskov Substitution Principle (LSP) in Swift, and what architectural code smells does it create?
?
LSP is violated when a subclass or protocol implementation alters the expected behavior of the parent (e.g., throwing unexpected errors, or using `fatalError()` for unimplemented methods). The resulting architectural smell is code sprinkled with `is` or `as?` type checks to handle specific subclass behaviors, defeating the purpose of polymorphism.

In modern SwiftUI apps, what are the architectural trade-offs of using The Composable Architecture (TCA) versus standard MVVM?
?
TCA provides strict unidirectional data flow, highly predictable state mutations, and first-class testing capabilities. However, it introduces a steep learning curve and significant boilerplate. MVVM is native and faster to build with, but can lead to fragmented, hard-to-track state changes and reference cycles in complex view hierarchies.

How does the Dependency Inversion Principle differ from Dependency Injection, and how is it natively implemented in Swift?
?
Dependency Injection is the _technique_ of passing dependencies in. Dependency Inversion is the _principle_ that high-level modules shouldn't depend on low-level concrete modules; both should depend on abstractions. In Swift, this is implemented by defining `Protocols` for dependencies rather than referencing concrete classes.

Architecturally, how do Swift `Actors` prevent data races, and when should you use a `@MainActor` annotation?
?
Actors use actor isolation to ensure only one task can access their mutable state at a time, enforcing thread safety at compile time. The `@MainActor` attribute architecturally binds UI-updating classes (like ViewModels) or global UI state to the main dispatch queue, ensuring UI updates are inherently thread-safe without manual `DispatchQueue.main.async` wrappers.

What are the architectural advantages of Protocol-Oriented Programming (POP) over classic Object-Oriented Inheritance in iOS?
?
POP allows for multiple conformance (solving the diamond problem of inheritance), favors value types (structs) which prevent unintended state sharing and race conditions, and enables default behaviors via protocol extensions. It results in flatter, more modular, and highly composable architectures.

How should a scalable iOS architecture handle deep links and push notifications without tightly coupling the AppDelegate/SceneDelegate to the UI?
?
The App/Scene delegate should only parse the incoming URL or Notification payload into a generic deep-link entity and pass it to a centralized AppCoordinator or Router. The routing layer evaluates the current app state and orchestrates the necessary navigation stack updates, keeping lifecycle delegates lean and decoupled.

How do architectural patterns like MVVM or VIPER accidentally introduce retain cycles, and how do you architecturally prevent them?
?
Cycles occur when components hold strong references to each other (e.g., a View strongly holding a ViewModel, and the ViewModel holding an observer closure that strongly captures the View). Architecturally, establish strict ownership rules: parents own children (strong), children delegate to parents (weak), and reactive closures must capture `[weak self]`.

How should Core Data be integrated into an iOS app to avoid leaking its framework details (like `NSManagedObject`) into the Presentation layer?
?
Hide Core Data behind a generic Data Repository or DAO (Data Access Object) protocol. The repository fetches `NSManagedObject`s but maps them directly to plain Swift structs (Domain Models) before returning them to the Interactor or ViewModel. This ensures the UI remains completely ignorant of the persistence framework.

What is Unidirectional Data Flow (UDF), and why is it highly recommended for complex SwiftUI architectures?
?
UDF is a pattern where state flows down to the views, and user events flow up as actions to a single source of truth (a store/reducer) that mutates the state. It pairs perfectly with SwiftUI—which is essentially a function of state—because UDF ensures state changes are entirely predictable and prevents infinite rendering loops caused by two-way bindings.

What is the architectural difference between a Mock, a Stub, and a Spy, and how do they inform your protocol design?
?
A Stub returns hardcoded data for state-based testing. A Spy records method calls and arguments for behavior-based testing. A Mock does both and verifies strict expectations (e.g., "was called exactly once"). To use them, you must design your architecture so that all side-effecting code (network, database) is hidden behind protocols, allowing these test doubles to be seamlessly injected.