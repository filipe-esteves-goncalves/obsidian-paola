---
cssclasses:
  - wide-page
---

Definition:
- Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

### The Bad Example: Tight Coupling
```swift
import Foundation

// LOW-LEVEL MODULE: The concrete network class
class NetworkManager {
    func fetchUser(id: String, completion: @escaping (String) -> Void) {
        // Imagine a real URLSession call here
        print("Fetching user from the live internet...")
        completion("User: Alice")
    }
}

// HIGH-LEVEL MODULE: The view model
class UserProfileViewModel {
    // 🚨 VIOLATION: We are tightly coupled to a concrete class.
    private let networkManager = NetworkManager()
    
    func loadUser() {
        networkManager.fetchUser(id: "123") { [weak self] user in
            print("Loaded \(user)")
            // Update UI...
        }
    }
}
```

###
```swift
import Foundation

// 1. THE ABSTRACTION: Both high and low levels will depend on this.
protocol UserService {
    func fetchUser(id: String, completion: @escaping (String) -> Void)
}

// 2. LOW-LEVEL MODULE: Conforms to the abstraction.
class NetworkManager: UserService {
    func fetchUser(id: String, completion: @escaping (String) -> Void) {
        // Real URLSession call
        print("Fetching user from the live internet...")
        completion("User: Alice")
    }
}

// 3. ANOTHER LOW-LEVEL MODULE (For testing!): Conforms to the abstraction.
class MockNetworkManager: UserService {
    func fetchUser(id: String, completion: @escaping (String) -> Void) {
        // Instantly returns fake data for unit tests
        print("Returning mock user...")
        completion("Mock User: Bob")
    }
}

// 4. HIGH-LEVEL MODULE: Depends on the abstraction, not the concrete class.
class UserProfileViewModel {
    private let userService: UserService
    
    // ✅ SUCCESS: We inject the dependency via the initializer (Dependency Injection)
    init(userService: UserService) {
        self.userService = userService
    }
    
    func loadUser() {
        userService.fetchUser(id: "123") { [weak self] user in
            print("Loaded \(user)")
            // Update UI...
        }
    }
}
```