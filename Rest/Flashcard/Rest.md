#flashcards/Rest

What are the http methods?
?
![[HTTP Methods]]


What is a RESTful API?
?
An architectural style for APIs that uses standard HTTP methods (GET, POST, PUT, DELETE) to interact with resources. 
It is stateless, meaning each request from client to server must contain all information needed to understand and process the request.

What is URLSession in iOS?
?
`URLSession` is the native iOS framework used to manage network data transfer tasks. It provides an API for downloading data from and uploading data to endpoints indicated by URLs.

How do you parse JSON responses from a REST API in Swift?
?
By using the `JSONDecoder` class along with Swift types (structs or classes) that conform to the `Codable` (or `Decodable`) protocol, which automatically maps JSON keys to Swift properties.

What is the difference between GET and POST requests?
?
A GET request retrieves data from a server and appends parameters to the URL. 
A POST request sends data to the server to create a resource, placing the data in the request body, which is much safer for sensitive information.

What is the difference between PUT and PATCH methods?
?
PUT replaces an entire resource with the newly provided data payload. 
PATCH applies partial modifications to an existing resource, updating only the specific fields provided in the request body.

Why are escaping closures (`@escaping`) required for URLSession network calls?
?
Network requests are asynchronous. 
An `@escaping` closure is required because the closure is passed as an argument to the network function but is executed later—after the function has already returned—once the network response is received.

How do you append an authentication token to a REST API request in Swift?
?
By creating a `URLRequest` object, modifying its headers using `request.setValue("Bearer <Token>", forHTTPHeaderField: "Authorization")`, and passing that request to a `URLSession` task.

What is the Codable protocol in Swift?
?
It is a typealias for both the `Encodable` and `Decodable` protocols. It allows native Swift data types to be easily serialized into and deserialized from external data representations like JSON.

How should you handle HTTP status codes in your iOS networking code?
?
By casting the `URLResponse` to an `HTTPURLResponse`, checking its `statusCode` property (e.g., ensuring it falls within the 200...299 success range), and throwing appropriate custom Swift errors for 4xx (client) or 5xx (server) codes.

What is URLComponents and why is it useful for REST APIs?
?
`URLComponents` is a Swift structure that safely constructs and parses URLs. It is especially useful for building REST API endpoints because it safely percent-encodes query parameters, avoiding crashes caused by malformed URL strings.

How do you handle background downloads in an iOS app?
?
By creating a `URLSession` using a background `URLSessionConfiguration` (initialized with a unique identifier). This allows the operating system to handle the download process even if the app is suspended or terminated in the background.

What is SSL/TLS Pinning in iOS networking?
?
A security measure where the iOS app is hardcoded to trust only a specific certificate or public key for a server. This prevents Man-in-the-Middle (MITM) attacks, even if a malicious root certificate is installed on the user's device.

How do you handle API rate limiting (HTTP 429) in iOS?
?
When receiving a 429 (Too Many Requests) status code, the app should parse the `Retry-After` header provided by the server, pause subsequent requests, and implement an exponential backoff strategy before trying again.

How do you implement offline support for REST API data in iOS?
?
By caching API responses using `URLCache` for temporary storage, or by persisting the fetched JSON data locally using databases like Core Data, Realm, or SwiftData so the UI can populate when there is no network reachability.

What are the common types of URLSession tasks?
?
`URLSessionDataTask` (for fetching data directly to memory), `URLSessionDownloadTask` (for downloading files and saving them to disk), `URLSessionUploadTask` (for uploading files or data), and `URLSessionWebSocketTask` (for continuous two-way communication).