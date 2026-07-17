---
cssclasses:
  - wide-page
---

Definition:
- Objects or entities should be open for extension but closed for modification.
### The Bad Example (Violating OCP)

```swift
enum PaymentType {
    case creditCard
    case applePay
}

class PaymentManager {
    func processPayment(type: PaymentType, amount: Double) {
        // Violates OCP: We have to modify this method every time we add a new payment type.
        switch type {
        case .creditCard:
            print("Processing credit card payment of $\(amount)")
            // Complex credit card logic here...
            
        case .applePay:
            print("Processing Apple Pay payment of $\(amount)")
            // Complex Apple Pay logic here...
        }
    }
}
```

### The Good Example (Following OCP)

```swift
// 1. Define a protocol that encapsulates the behavior
protocol PaymentMethod {
    func process(amount: Double)
}

// 2. Create specific classes/structs for each method
class CreditCardPayment: PaymentMethod {
    func process(amount: Double) {
        print("Processing credit card payment of $\(amount)")
        // Complex credit card logic here...
    }
}

class ApplePayPayment: PaymentMethod {
    func process(amount: Double) {
        print("Processing Apple Pay payment of $\(amount)")
        // Complex Apple Pay logic here...
    }
}

// 3. The manager now relies on the protocol, not concrete types
class PaymentManager {
    func processPayment(method: PaymentMethod, amount: Double) {
        method.process(amount: amount)
    }
}
```