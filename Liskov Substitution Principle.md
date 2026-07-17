---
cssclasses:
  - wide-page
---

Definition
- Let q(x) be a property provable about objects of x of type T. 
  Then q(y) should be provable for objects y of type S where S is a subtype of T.

Explanation:
- Subtypes must be replaceable for their base types without breaking expected behavior.

### The Bad Example (Violating LSP)

```swift
class CacheManager {
    func save(data: Data, forKey key: String) {
        UserDefaults.standard.set(data, forKey: key)
    }
    
    func loadData(forKey key: String) -> Data? {
        return UserDefaults.standard.data(forKey: key)
    }
}

class BundleResourceLoader: CacheManager {
    override func loadData(forKey key: String) -> Data? {
        // Correctly loads pre-packaged files from the iOS Main Bundle
        guard let url = Bundle.main.url(forResource: key, withExtension: nil) else { return nil }
        return try? Data(contentsOf: url)
    }
    
    override func save(data: Data, forKey key: String) {
        // VIOLATION: The iOS App Bundle is read-only at runtime!
        // We have to crash the app or silently fail because this subclass 
        // cannot fulfill the contract of its superclass.
        fatalError("Cannot save data to the read-only app bundle.")
    }
}

// ❌ Usage that breaks the app:
func downloadAndCacheImages(using cache: CacheManager, images: [String: Data]) {
    for (name, data) in images {
        // If someone passes a BundleResourceLoader into this function, 
        // the app will instantly crash.
        cache.save(data: data, forKey: name) 
    }
}
```

### The Good Example (Adhering to LSP)

```swift
// 1. Define capabilities using protocols
protocol DataReader {
    func loadData(forKey key: String) -> Data?
}

protocol DataWriter {
    func save(data: Data, forKey key: String)
}

// 2. A cache that can both read and write
class UserDefaultsCache: DataReader, DataWriter {
    func save(data: Data, forKey key: String) {
        UserDefaults.standard.set(data, forKey: key)
    }
    
    func loadData(forKey key: String) -> Data? {
        return UserDefaults.standard.data(forKey: key)
    }
}

// 3. A loader that strictly reads (No forced 'save' method!)
class BundleResourceLoader: DataReader {
    func loadData(forKey key: String) -> Data? {
        guard let url = Bundle.main.url(forResource: key, withExtension: nil) else { return nil }
        return try? Data(contentsOf: url)
    }
}

// ✅ Usage that respects LSP:
// We explicitly require a `DataWriter` here, not just a generic manager.
func downloadAndCacheImages(using cache: DataWriter, images: [String: Data]) {
    for (name, data) in images {
        cache.save(data: data, forKey: name)
    }
}
```