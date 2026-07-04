
```swift
// 1. Defining a simple blueprint (a Structure) for a User
struct User {
    var name: String
    var age: Int
    let isSubscribed: Bool 
    
    // 2. A function (an action) inside the structure
    func greet() {
        // String interpolation makes it easy to mix text and variables
        print("Hello, my name is \(name) and I am \(age) years old!")
    }
}
// 3. Creating a new user based on that blueprint
let myUser = User(name: "Alex", age: 25, isSubscribed: true)

// 4. Calling the function to print the message
myUser.greet()
```