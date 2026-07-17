---
cssclasses:
  - wide-page
---

Definition:
- A class or module should have **one** clear responsibility and one primary reason to change.

### Good example

This class handles networking. It only changes if we switch APIs, change our networking library (e.g., from `URLSession` to Alamofire), or update our authentication logic.
```swift
import Foundation

class UserService {
    func fetchUser(id: Int) async throws -> User {
        guard let url = URL(string: "https://api.example.com/user/\(id)") else {
            throw URLError(.badURL)
        }
        
        let (data, _) = try await URLSession.shared.data(from: url)
        return try JSONDecoder().decode(User.self, from: data)
    }
}
```

### Bad example
In this example, the `ProfileViewController` is doing everything. 
It handles the 
- view lifecycle
- makes the network request
- parses the JSON
- and updates the UI
```swift

import UIKit

class ProfileViewController: UIViewController {
    @IBOutlet weak var nameLabel: UILabel!
    @IBOutlet weak var emailLabel: UILabel!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        fetchUserData()
    }
    
    // BAD: The View Controller is acting as a networking layer and a data parser.
    private func fetchUserData() {
        guard let url = URL(string: "https://api.example.com/user/1") else { return }
        
        URLSession.shared.dataTask(with: url) { [weak self] data, response, error in
            guard let data = data, error == nil else {
                print("Network error")
                return
            }
            
            // BAD: The View Controller is parsing raw JSON.
            do {
                if let json = try JSONSerialization.jsonObject(with: data, options: []) as? [String: Any],
                   let name = json["name"] as? String,
                   let email = json["email"] as? String {
                    
                    // Update UI on main thread
                    DispatchQueue.main.async {
                        self?.nameLabel.text = "Name: \(name)"
                        self?.emailLabel.text = "Email: \(email)"
                    }
                }
            } catch {
                print("Parsing error")
            }
        }.resume()
    }
}
```