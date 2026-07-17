
#flashcards/Swift/Scalable-performant

How does modularizing an iOS application improve both scalability and build performance?
?
Modularization breaks the app into smaller, isolated frameworks or Swift packages. This allows parallel development across multiple feature squads (scalability) and enables build systems to cache unchanged modules, significantly reducing incremental build times (performance).

How do Swift Actors solve data races in highly concurrent, scalable iOS applications?
?
Actors provide a thread-safe state management mechanism by using actor isolation. They serialize access to their mutable state internally, ensuring that only one task can mutate or read the state at a time, effectively eliminating data races without the overhead of manual locking (like NSLock).

What are the main culprits of off-screen rendering in UIKit/CoreAnimation, and how do you mitigate them to maintain 60/120 fps?
?
Off-screen rendering is typically caused by applying corner radius (with masksToBounds), shadows, and opacity to complex layer trees. To mitigate this, render shadows using a UIBezierPath (shadowPath), use opaque background colors, or draw rounded corners directly into a UIImage core graphics context instead of using CALayer's cornerRadius.

How do you profile and optimize an iOS app's pre-main launch time as the codebase scales?
?
Pre-main time is profiled using Instruments (App Launch template) or by setting the DYLD_PRINT_STATISTICS environment variable. Optimization involves minimizing the number of dynamic frameworks (favoring static libraries), reducing Objective-C +load methods (using +initialize instead), and consolidating heavy global static variables.

How do you handle fetching and processing massive datasets in Core Data without blocking the main thread or causing memory spikes?
?
Use NSBatchUpdateRequest and NSBatchInsertRequest for bulk updates without loading objects into memory. For fetching, utilize background NSManagedObjectContexts, fetch limits, and NSFetchRequest's fetchBatchSize to keep the memory footprint low while faulting in objects only as they are actively needed.

In a large SwiftUI application, how can improper use of @ObservedObject or @EnvironmentObject degrade UI performance, and how do you fix it?
?
Passing overly complex objects via @EnvironmentObject or using @ObservedObject high up in the view hierarchy can cause massive, unnecessary view invalidations when a minor, unrelated property changes. Fix this by breaking state into smaller, focused view models, or using EquatableView to carefully observe only the exact data a specific view requires.

What strategies would you implement in the networking layer of a scalable iOS app to minimize battery drain and reduce latency?
?
Implement aggressive caching (URLCache, ETags), use HTTP/2 or HTTP/3 for connection multiplexing, batch non-critical API requests (e.g., analytics) to avoid waking the radio constantly, and utilize Background URL Sessions for large uploads/downloads to defer work to the OS.

In a highly performant data-processing loop on iOS, why might memory grow unbounded even without retain cycles, and how do you resolve it?
?
Temporary objects created inside the loop are added to the thread's autorelease pool and won't be deallocated until the end of the run loop, causing unbounded memory growth. Resolve this by wrapping the contents of the loop in an @autoreleasepool { } block to force immediate deallocation of temporary objects per iteration.

Why is Dependency Injection (DI) crucial for scaling an iOS codebase, and what are the performance implications of different DI approaches?
?
DI decouples components, making them independently testable and reusable across multiple teams. However, dynamic/reflection-based DI containers (like Swinject) incur runtime performance overhead and crash risks. Compile-time DI (like Needle or manual constructor injection) is preferred for senior-level apps due to safety and zero runtime overhead.

How do you achieve buttery smooth scrolling in a UICollectionView displaying highly complex, variable-height cells with media?
?
Use UICollectionViewDataSourcePrefetching to asynchronously download and decode media before the cell appears. Pre-calculate and cache cell layouts/heights on a background thread. Minimize view hierarchy complexity, avoid blocking the main thread during cellForItemAt, and implement prepareForReuse effectively to cancel pending, off-screen image requests.

How does the Coordinator pattern contribute to the scalability of navigation in a large iOS application?
?
The Coordinator pattern entirely removes routing and presentation logic from ViewControllers, preventing them from knowing about each other. This enables isolated unit testing of view controllers, scalable deep-linking logic, and the ability to easily reuse flows across different parts of the application.

Besides using Instruments, what architectural or testing practices can proactively prevent memory leaks at scale?
?
Proactive practices include strict code review guidelines around closures (properly using [weak self]), leveraging Swift's strict memory safety rules, and implementing memory leak unit tests. You can automate this by keeping weak references to System Under Test (SUT) objects and asserting they evaluate to nil in the XCTestCase tearDown method.

As an iOS codebase grows to millions of lines of code, why might an engineering team migrate from standard Xcode projects to Bazel or Tuist?
?
Standard Xcode.xcodeproj files scale poorly due to monolithic project structures that cause frequent merge conflicts, and Xcodebuild has inefficient caching for massive codebases. Tools like Bazel or Tuist generate projects dynamically, enforce strict dependency graphs, and utilize aggressive remote build caching, which dramatically speeds up CI pipelines and local compilation times.

How would you efficiently execute 50 independent network requests concurrently and aggregate the results using Swift Concurrency?
?
Use a TaskGroup (or ThrowingTaskGroup). Iterate through the 50 request operations, adding each to the group using group.addTask { ... }. Then, use a `for await result in group` loop to collect the results as they complete. This maximizes system resources while respecting the cooperative thread-pool limits, avoiding thread explosion.

What techniques do you use to keep the final .ipa binary size small as an application scales with more features, modules, and assets?
?
Techniques include using vector assets (PDF/SVG/SF Symbols) instead of multiple rasterized images (1x/2x/3x), compressing audio/video payloads, aggressively auditing and removing unused code/assets (using tools like Periphery), enabling compiler optimizations (LTO), and offloading non-critical features to On-Demand Resources (ODR).


