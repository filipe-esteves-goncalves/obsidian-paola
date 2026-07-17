
#flashcards/Swift/General


How do you prevent a retain cycle when using an escaping closure inside a class, and when is unowned preferable to weak?
?
By using capture lists [weak self] or [unowned self]. You should use unowned only when you are absolutely certain the captured reference will never be nil while the closure executes, as it avoids optional unwrapping overhead but will crash if accessed after deallocation.

What is an actor in Swift Concurrency, and how does it prevent data races compared to traditional GCD barriers?
?
An actor is a reference type that isolates its mutable state, guaranteeing mutually exclusive access automatically. Unlike GCD concurrent queues with barrier flags, which require manual boilerplate and are prone to developer error, actors enforce synchronization and thread safety at compile-time.

Why is @MainActor commonly applied to an entire SwiftUI ObservableObject or ViewModel class instead of individual properties?
?
It guarantees that all UI state mutations happen on the main thread, preventing undefined behavior or crashes. It eliminates the need to scatter 'await MainActor.run' throughout methods, letting the compiler enforce main-thread execution for all state updates automatically.

How does SwiftData differ from Core Data in terms of model declaration and SwiftUI integration?
?
SwiftData uses the @Model macro directly on Swift classes instead of requiring a separate .xcdatamodeld file or NSManagedObject subclasses. It integrates natively with SwiftUI using the @Query property wrapper and #Predicate for filtering, eliminating the need for string-based NSPredicates.

How would you optimize the build time of a large Swift codebase in Xcode?
?
By utilizing Swift Package Manager for modularization, avoiding implicit type inference in complex expressions, using Whole Module Optimization for release builds, caching dependencies with precompiled XCFrameworks, and resolving target dependency bottlenecks shown in Xcode's Build Timeline.

How do you embed a SwiftUI View inside an existing UIKit application, and conversely, a UIKit View inside SwiftUI?
?
You use UIHostingController to wrap a SwiftUI View for use in UIKit. To use a UIKit view in SwiftUI, you conform to UIViewRepresentable (or UIViewControllerRepresentable), implementing makeUIView and updateUIView to manage the lifecycle and state synchronization.

Explain Protocol-Oriented Programming in Swift. How do protocol extensions solve the "fragile base class" problem?
?
POP emphasizes composition over inheritance. Protocol extensions allow you to provide default implementations for protocol requirements. This avoids the fragile base class problem by enabling horizontal behavior sharing across value types (structs/enums) without relying on deep, rigid class inheritance hierarchies.

How do you bridge a Combine AnyPublisher to modern Swift async/await code?
?
You can use the .values property on an asynchronous Combine publisher, which exposes an AsyncPublisher that conforms to AsyncSequence. This allows you to iterate over the emitted values seamlessly using a 'for try await' loop inside an async context.

How does SwiftUI handle app lifecycle events compared to AppDelegate and SceneDelegate in UIKit?
?
SwiftUI uses the App protocol and the ScenePhase environment value. You can monitor changes using .onChange(of: scenePhase) to detect when the app moves between .active, .inactive, and .background states without relying on delegate callbacks.

How does Swift implement Copy-on-Write (CoW) for its standard library collections, and how would you implement it for a custom struct?
?
Swift standard collections share a single backing reference until one is mutated, at which point the buffer is copied. For a custom struct, you implement CoW by wrapping your data in a private reference type (class) and checking isKnownUniquelyReferenced(&ref) before mutating; if it is not unique, you copy the underlying object.

How do you manage background location updates efficiently using CoreLocation without draining the user's battery?
?
Request 'Always' authorization, enable "Location updates" in Background Modes, and use the Significant-Change Location Service or Region Monitoring (Geofencing) instead of continuous high-accuracy updates. The allowsBackgroundLocationUpdates flag must be true, and pausesLocationUpdatesAutomatically should be leveraged.

What is Strict Concurrency Checking in Swift 6, and what does the Sendable protocol do?
?
Strict Concurrency Checking enforces thread safety at compile time by ensuring mutable state isn't shared unsafely across concurrency domains. The Sendable protocol indicates that a type can be safely passed across actor boundaries. Value types are implicitly Sendable, whereas classes must be final and immutable or internally synchronized.

How do you mock native network requests using URLProtocol when writing XCTest unit tests?
?
Instead of mocking URLSession directly, you subclass URLProtocol to intercept network requests and return stubbed responses. You then inject this custom URLProtocol into the URLSessionConfiguration, allowing you to test the actual networking code without hitting a live server.

Which Xcode Instruments would you use to track down a UI freeze (hitch) versus a memory leak?
?
Use the Time Profiler and Animation Hitches instruments to find methods blocking the main thread or causing dropped frames. For memory leaks, use the Allocations instrument combined with the Leaks tool and the Debug Memory Graph in Xcode to visualize strong reference cycles.

What is the difference between an opaque return type (some View) and an existential type (any View) in Swift?
?
The 'some' keyword (opaque type) hides the concrete return type from the caller but guarantees it is a specific, single underlying type known at compile time, preserving type identity and performance. The 'any' keyword (existential type) acts as a runtime box that can hold any type conforming to the protocol, incurring a performance overhead due to dynamic dispatch.