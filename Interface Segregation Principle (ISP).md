---
cssclasses:
  - wide-page
---
Definition:
- A client should never be forced to implement an interface that it doesn’t use, 
  or clients shouldn’t be forced to depend on methods they do not use.

## The Bad Example (Violating the Interface Segretation)

```swift
// A "fat" protocol that does too much
protocol ViewInteractionDelegate {
    func didTap()
    func didDoubleTap()
    func didLongPress()
    func didPan()
}

// ❌ BAD: StandardButton only cares about single taps, 
// but is forced to conform to the entire protocol.
class StandardButton: ViewInteractionDelegate {
    
    func didTap() {
        print("Button was tapped - performing action.")
    }
    
    // We are forced to implement these methods even though we don't need them.
    // This clutters the code and creates potential points of confusion.
    func didDoubleTap() {
        // Do nothing
    }
    
    func didLongPress() {
        // Do nothing
    }
    
    func didPan() {
        // Do nothing
    }
}
```

### The Good Example

```swift
// Segregated, specific protocols
protocol Tappable {
    func didTap()
    }

protocol DoubleTappable {
    func didDoubleTap()
}

protocol LongPressable {
    func didLongPress()
}

protocol Pannable {
    func didPan()
}

// ✅ GOOD: StandardButton only implements what it actually uses.
class StandardButton: Tappable {
    func didTap() {
        print("Button was tapped - performing action.")
    }
}

// ✅ GOOD: A more complex view can conform to multiple protocols 
// to compose the exact functionality it needs.
class InteractiveImageView: Tappable, DoubleTappable, Pannable {
    
    func didTap() {
        print("Show image overlay.")
    }
    
    func didDoubleTap() {
        print("Zoom into the image.")
    }
    
    func didPan() {
        print("Swipe to the next image.")
    }
}
```